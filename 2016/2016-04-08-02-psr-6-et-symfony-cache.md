# Symfony Live Paris 2016

## PSR-6 & Symfony Cache : de la perf en standard
Par Nicolas Grekas @nicolasgrekas (CTO blackfire.io)

On nous a présenter la PSR-6 (Interoprable Cache API) et son implémentation
strict dans le composant symfony/cache https://github.com/symfony/cache

L'API est plutot simple
  * Une Interface CacheItemPoolInterface pour accéder au cache (le pool)
  * Une interface CacheItemInterface qui décrit un item de cache (item)

L'implémentation symfony respect strictement le standard et propose divers
adapteurs de pool
  * AcpuAdapter
  * ArrayAdapter (memory cache)
  * DoctrineAdapter (un wrapper sur l'api de cache de Doctrine)
  * FileSystemAdapter (cache on-disk)
  * RedisAdapter
  * ChainAdapter pour cabler une cascade de caches

Il y a aussi un Bundle pour l'intégration dans Symfony qui fourni
  * deux cache en standard `cache.local` et `cache.shared`
  * les futures version de Symfony utiliserons des cache pool privé/interne pour cacher certaines choses (metadata, compiled container etc)
