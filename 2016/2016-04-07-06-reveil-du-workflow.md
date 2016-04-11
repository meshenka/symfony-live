# Symfony Live Paris 2016

## Le reveil du Workflow
Par Grégoire Pineau, @lyrixx (Blackfire.io)
Slides : https://speakerdeck.com/lyrixx/le-reveil-du-workflow

  * Demo: http://symfony-workflow-demo.herokuapp.com/show/1
  * app de démo ici https://github.com/lyrixx/SFLive-Paris2016-Workflow
  * la PR qui donne tous les détails https://github.com/symfony/symfony/pull/11882#discussion_r58851472

Globalement le composant implémente les Workflows sous forme de réseaux de Petri (graph orienté)

Le workflow est décrit à travers les notions suivantes:
  * Place : un état possible de Workflow
  * Transition : Représente le passage de Places entrantes vers places sortante (au pluriels car cela supporte des états paralleles)
  * Token : Information qui indique si l'objet sujet est "dans" une ou plusieurs places (c'est une collection de place)

Le composant founi des outils:
  * export sous forme d'un graph de la définition d'un Workflow

Le composant fourni le service 'workflow' qui permet:
  * `getAvailableTransitions()` : liste les transitions qui peuvent être appliqués
  * `can()` : indique si une transition précise peut être appliqué
  * `apply()` : applique une transition et met à jour les tokens

Le composant fourni des events pour implémenter la logique métier:
  * `WorkflowEvent:onExit()` - on sort d'une place
  * `WorkflowEvent:onEnter()` - on entre dans une place
  * `WorkflowEvent:onTransition()` - on applique une transition
  * `GuardEvent` -- qui est utilisé dans can pour appliquer de la logique métier au passage de transitions

Le composant fourni enfin une intégration dans Symfony FullStack via une configuration
du workflow en XML/YML
