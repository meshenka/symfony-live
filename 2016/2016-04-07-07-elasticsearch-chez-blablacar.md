# Symfony Live Paris 2016

## ElasticSearch chez Blablacar
Olivier Dolbeau [@odolbeau](https://twitter.com/odolbeau) Blablacar

Les slides sont ici https://speakerdeck.com/odolbeau/elasticsearch-chez-blablacar

ElasticSearch
  * est une solution de recherche concurrente de Solr
  * dispose d'une API HTTP
  * un format de requête simple et puissant (syntax JSON)

ElasticSearch chez Blablacar:
  * Index de 250 Go
  * 2 millions de bulk par mois (un bulk est une série d'ajout/suppression/maj)

Features:
  * Query : recherche fulltext
  * Filters : ajout de conditions de recherche
  * Pagination : pagination
  * Partials : retourne juste certaines properties d'un document
  * Aggregats : une implémentations des facets
  * geosearch : recherche par proximité géographique
  * clustering, réplications, bulk updates

Les absents : pas de composition de documents, si vous voulez indexer un
commentaire et son auteur, le document doit porter tous les champs pour pouvoir
faire des requetes type : tous les commentaires de tel utilisateur sur tel voyage

**Il y a donc beaucoup de dénormalisation** ce qui implique qu'une modif sur le
profile utilisateur peut provoquer un gros bulk update

La présentation tournait autour d'une fonctionnalité avancé: le post_filter

Le post_filter c'est la possibilité de faire des aggegats en dehors des facets activés

ex: dans les facets dispo chez Blablacar il y a
  * prix : c'est un slider dont les bornes sont entre le prix mix et max du result set
  * comfort : c'est une facet sur le type de véhicule

Ce qu'on veux c'est que quand j'applique une facette de comfort luxe le slider
prix se mettre à jour, mais je veux aussi garder toutes les options de conforts
alors que la facet est activé
