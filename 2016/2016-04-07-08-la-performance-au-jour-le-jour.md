# Symfony Live Paris 2016

## Performance au quotidien dans un environnement Symfony
Xavier Leune @beoneself (CCM Benchmark Group/Groupe Figaro)
Slides : http://fr.slideshare.net/xavierleune/performance-au-quotidien-dans-un-environnement-symfony

Les chiffres totaux: 700 Millions de pages vue par mois

Pour tenir cela il faut imposer des règles stricts:
  * Temps d'exécution php : 100ms max
  * Ram consomé : 8Mo max
  * Requêtes SQL / pages : 12 max

Pas facile à respecter avec un Framework

La stack technique:
  * Akamai
  * Varnish
  * php 5.6
  * mariadb
  * memcache
  * redis

Point de blocage : L'ORM Doctrine

CCM utilise une lib maison type no-RM TING http://tech.ccmbg.com/ting/

Au dela de cela il faut mettre en place une stricte politque de cache:
  * ESI Varnish
  * Faire du cache warmup
  * pas de build en prod
  * hot reloading

Dans l'intégration continue on peut constuire un scénario de perf et le profiler
avec https://blackfire.io/

Et attention tous cela c'est la perf back-end, il faut aussi penser au front-end
  * js
  * assets
  * optimisation des images

Dans le cadre CCM, ces containtes peuvent affecter le design.
