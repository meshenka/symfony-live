# Symfony Live Paris 2016

## Aller plus loin avec Doctrine2
Par André Tapia et Amine Mokeddem @nh293 (Doctrine Core Developpers + Webnet)
Slides : http://blog.webnet.fr/wp-content/uploads/2016/04/2016-04-06-FINAL-Pr%C3%A9sentation-SymfonyLive-Doctrine2.pdf

Nous avons vue:
  * Comment ajouter du vocabulaire à DQL https://github.com/beberlei/DoctrineExtensions fourni pleins de nouveau DQL
  * La gestion des évenements
    * LifeCycleCallbacks
    * Listener onFlush()
    * EventSubscribers

```php
class EntityTimestamperSubscriber implements EventSubscriber {
    (...)
    public function preUpdate(PreUpdateEventArgs $args)
    {
        $entity = $args->getEntity();
        if (!$entity instanceof UpdateTimestampableEntityInterface) {
            return;
        }
        $entity->setUpdatedAt(new \DateTime());

        // notez bien ici l'utilisation de recomputeSingleEntityChangeSet
        // c'est nécessaire si le listener modifie l'entité en cours
        $args->getEntityManager()->getUnitOfWork()
            ->recomputeSingleEntityChangeSet(
                $args->getEntityManager()->getClassMetadata(get_class($entity)),
                $entity
            );
    }
}
```

### L'optimisation de requête

```php
$results = $this->createQueryBuilder('p')
    ->getQuery()
    ->getResult();

foreach ($results as $result) {
    $result->setPretaxPrice(
        $result->getPretaxPrice() - $this->getProductDiscount($result->getId())
    );
}
$this->getEntityManager()->flush();
```

Pb si il y a 30 000 produit cette requête prends 23 secondes et 218 Mo

La version optimisé

```php
public function importPrice()
{
    $em = $this->getEntityManager();
    $connection = $em->getConnection();
    // on desactive le logger doctrine
    $connection->getConfiguration()->setSQLLogger(null);
    $results = $this->createQueryBuilder('p')->getQuery();

    //on démarre une transaction pour tout faire en une Query
    $connection->beginTransaction();
    //on utilise ->iterate() qui optimise la mémoire
    foreach ($results->iterate() as $result) {
        //on fait la MAJ en SQL
        $connection->update(
            $em->getClassMetadata('AppBundle:Product')->getTableName(),
            array(
                'pretax_price' => $result[0]->getPretaxPrice() –
                $this->getProductDiscount($result->getId())
            ),
            array('id' => $result[0]->getId())
        );
        $em->detach($result[0]);
    }
    $connection->commit();
}
```
Après cela on passe en 6s et 8.25Mo

### Requete partiel

Les partial c'est la possibilité d'hydraté partiellement un objet

```php
public function getAllProductsNoPartial()
{
    return $this->createQueryBuilder('p')
        ->select('p, c')
        ->leftJoin('p.category', 'c')
        ->getQuery()
        ->getResult();
}
```

```php
public function getAllProductsPartial()
{
    return $this->createQueryBuilder('p')
        ->select('partial p.{id, title, vatRate, pretaxPrice}')
        ->addSelect('partial c.{id, title}')
        ->leftJoin('p.category', 'c')
        ->getQuery()
        ->getResult();
}
```

**Attention avec cela, surtout si l'objet est transmis à d'autre service,
les attributs non importé dans le partial sont null et non lazy-loadable**

### Filters

Un outil bien pratique, un filter est un fragment de query qui pourra être automatiquement
appliqué à **TOUTES** les requêtes

Cas d'usage classique: le soft-delete : quand on "supprime" un objet il n'est pas
réellement supprimé mais un flag `deleted` est assigné à true et le filtre ajoute
automatiquement un where `deleted = false`

Cas d'usage - classique: filtrer par les roles utilisateurs
  * centralisation des regles de visibilité
  * le développeurs n'a pas à penser à appliquer ces regles dans ses requêtes


Présentation assez dense, plein de code, et assez technique
