# Symfony Live Paris 2016

## Retour d'expérience Reactive Architecture et Microservices : Comment découpler mes applications ?
Par Fabien Meurillon, @FabienM Architect/Technical Expert, Smile
Slides : http://fabien.meurillon.org/sfLive2016/#1

Architecture Reactive:
  * Responsive (rapide)
  * Resilient (no spoof)
  * Elastic (scalable)
  * Message Driven (Asynchrone)

Une achitecture à base de Microservices est une forme d'Architecture Reactive

Architecture Microservices:
  * décomposition d'une application en sous app indépendentes,
  * spécialisé
  * petite (souvent 5 ou - structures de données)

Avantages:
  * Plus simple pour des développeurs de rentrer dans un Microservice, que dans une grosse application monolithique
  * - d'effets de bords, car la frontière du Microservice est explicite
  * scalabilité: On peut augmenter le cluster d'un unique Microservice. Maitrise plus fine

Inconvénients:
  * infrastructure plus lourde
  * déploiement plus complexe

Présentation de la solution déployé chez Group Auchan, avec Symfony2
  * Canonical Data Model: Tout objet métier se présente sous une forme canonique: JSON avec JMSSerializer
  * Protocol de Transfert HTTP/REST
  * Chaque Microservice est composé de deux Bundles et d'une App dit Provider:
    * Un ApiBundle: ex StockApiBundle pour un Microservice de gestion de stock
      * pas de logique métier
      * par de persistance, mais le Canonical Data Model
      * Des controlleur qui représente l'API externe (REST) du Microservice (chaque action dispatch un event)
      * Un service 'client' qui donne une classe d'API php pour communiquer dans une appli cliente du Microservice
        * client utilise le client http guzzle
        * il appel l'api REST de la provider app
    * Un StockProviderBundle qui dépend de l'API Bundle
      * implémente des listeners pour chaque action : cablage de la logique métier
      * implémente la persistance/ORM du Canonical Data Model
    * Une application StockProviderApplication (appli full stack Symfony qui est composé de StockProviderBundle et StockApiBundle)

Tout autre service (ex OrderProviderApplication) qui aurai besoin de solicité la micro app stock
  * doit installer la dépendence composer StockApiBundle
  * doit utilise le service client StockApiBundle/Api/StockClient

Enfin pour agglomérer tous cela il faut mettre en place un ESB et un Service Locator
  * l'ESB Dispatch tous les messages vers un RabbitMQ
  * le Service Locator fourni la configuration des divers Microservices (url de base de l'api)
