# Symfony Live Paris 2016

## Keynote d'ouverture
Par Fabien Potencier [@fabpot](https://twitter.com/fabpot) (CEO, Blackfire.io, Fondateur de Symfony1/2/3, +++)

Il a parlé du processus de contribution et de livraison de Symfony2

En partant du postulat suivant:
  * **Le processus de développement est totalement indépendant du processus de livraison**

Des lors deux choix s'offrent ave GIT
  * Un repository par projet aka Multi repository
  * Un repository unique qui porte tous les sous-projets aka mono-repository

_Note: quand il parle de mono-repo il parle de cela dans le cadre d'un projet ayant plusieurs composants, ex: smart-answers
et non pas de mettre tous le code de tous projets, même pour des clients différents, dans un unique repo_

Le mono-repository offre certains avantages pour le développement
  * refactoring simple, on peut tout faire en une Pull Request
  * Plan de tag simple ou chaque projet suit un versionning commun
  * gestion de dépendences internes simple
  * suite de test simple à jouer

Le multi-repository offre aussi certains avantages:
  * contrôle d'accès simple à chaque sous-projets
  * isolation des composants
  * versionning différents (on peut tagger x sans tagger y)

Symfony2 utilise les deux méthodes en même temps!!
  * 1 mono-repository qui prends tous les composants https://github.com/symfony/symfony/
  * Chaque composant à aussi son propre repository ex https://github.com/symfony/console

En tout 44 repository!!
  * Le mono-repository est le maitre. Toute évolution est commité dans ce repo (dans les 5 branches qui sont maintenus!)
  * A Chaque merge dans symfony/symfony avec `git subtree split` les 43 autres repos sont mis à jour, soit env 220 split à chaque merge request

Les développeurs travaillent soit dans sous-projets en 'read-only' soit dans symfony/symfony
puis les merge request sont appliqués systématiquement dans les X branches maintenus de symfony/symfony

Fabien à souligné l'importance de l'outillage, toutes ces opérations ne sont pas faite à la main,
mais encapsulé dans une commande maison `gh` qui pilote completement le processus

gh :
  * reroute une PR depuis un sub-repo vers le mono-repository
  * la pr est évaluer pour typo, commentaires, eval statique, présence de test unitaires
  * la pr passe dans l'intégration continu ou les suites de tests unitaires et d'intégration sont exécutés
  * Créer autant de PR que de branches maintenus
  * Aide à reformater le message de commit qui respecte une sémantique (qui sert à générer les changelog)
  * split le mono-repository pour mettre à jour les repo read-only
  * envoi un petit mail de remerciement au contributeur

**Passer du temps sur le tooling, c'est gagner du temps à long terme**, c'est rendre éfficace son workflow
