# Symfony Live Paris 2016

## PHP Meminfo ou la chasse aux memory leak
Par Benoit Jaquemont @BJacquemont DT chez Akeneo (ex DT Smile Group)
Slides : https://speakerdeck.com/bitone/hunting-down-memory-leaks-with-php-meminfo

### Présentation du memory leak

Un memory leak est un emplacement mémoire qui n'est jamais libéré par PHP

Symptomes:
  * le traitement est de plus en plus lent avec le temps
  * le traitement prends énormément de mémoire

Pourquoi?
  * en PHP on ne gére pas la libération de la mémoire (par comme en C)
  * le mécanisme de libération de mémoire de php à des bugs


Libération de la mémoire comment ca marche?

Il y a deux logiques

###  Libération de la mémoire: Le RefCount
php garde un compteur de chaque utilisation d'une valeur (zval dans php), quand ce compteur tombe a zero, la mémoire est immédiatement libéré.

```php
function printText(){
    $text = 'hello'; // zval : { name: text, refcount: 1 }
    echo $text; // zval : { name: text, refcount: 1 }
}

printText();
//après l'execution zval : { name: text, refcount: 0 }
```

cependent parfois il n'arrive pas à reduire le refcount

```php
class Leaker {
    private $attr;

    public function leak(){
        $a = new \DateTime(); // zval : { name: a, refcount: 1 }
        $b = new \DateTime(); // zval : { name: b, refcount: 1 }

        $this->attr = $a;  // zval : { name: a, refcount: 2 }
        $this->attr = $b;  // zval : { name: a, refcount: 2 } { name: b, refcount: 2 } php n'a pas détecté la réaffectation

    }

}

new Leaker()->leak();
//après l'execution
// zval : { name: a refcount: 1 }
// zval : { name: b refcount: 1 }
// ces deux zval ne seront jamais libéré
```

### Libération de la mémoire: Le Circular Reference Garbage Collector

Ce processus dispose d'un buffer de 10000 objet, quand une zval est créer elle est
référencé dans ce buffer, quand le buffer est plein le processus se lance:
  * il va inspecter le buffer et chercher une zval en mémoire référence celle ci, si pas le cas: libération

Probleme:
  * ce processus est lourd, et plus ca va et plus il prends de temps jusqu'au moment ou le CPU passe plus de temps dans le GC qu'a traiter votre code
  * double punission quand le buffer est plein et que rien n'est libéré, chaque nouvelle zval relance le GC!!

### Solution
  * Monitorer vos processus long / infini
  * Jamais de set_memory_limit -1
  * Logger la vitesse de traitement de vos processus (cas des imports, des exports)
  * Eviter les composants statefull

https://github.com/BitOne/php-meminfo (écrit par Benoit Jaquemont)

  * C'est une extension de php qui une fois installé permet de dumper l'état de la mémoire du processus php
  * c'est des commande bash qui permettent d'analyser le dump pour comprendre ce qu'il se passe, voir ne nb d'objet de chaque type en mémoire et comment est organisé la compositions des objets
