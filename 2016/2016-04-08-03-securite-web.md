# Symfony Live Paris 2016

## Sécurité web: pirater pour mieux protéger
Par Alain Tiemblo @ninsuo (Resp securité chez BlaBlaCar)
Slides https://github.com/ninsuo/slides

Il nous a présenté certaines technique de hacking
  * SQL Injection,
  * XSS,
  * Framability,
  * CSRF
  * password bruteforce

Du classique, mais aussi des choses plus pointu comme le SMS jacking

Quelques tricks TWIG
voir http://twig.sensiolabs.org/doc/filters/escape.html
```twig
{{ variable }} //standard html escape
{#
mais il faut changer la politique d'echappement en fonction des cas
#}

{# escape des attributs html #}
<a class="{{ class | e('html_attr')}}" src="#">test</a>

{# escape des valeurs js #}
<script type="text/javascript">
    var PUB_KEY = '{{ api_key | e('js')}}';
</script>

{# escape des valeurs css #}
<style>
body {
    background-color: lightblue;
}

h1 {
    color: {{ color.h1 | e('css')}};
    margin-left: 20px;
}
</code>
```

Ou encore de l'utilisation du composant HTMLPurifier pour echappé correctement
du HTML fourni par un editeur WYSIWYG

Une recommandation: **Utiliser les token CSRF sur vos form de login et vos boutons
 de logout.**
