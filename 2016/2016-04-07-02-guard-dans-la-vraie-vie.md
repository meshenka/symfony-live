# Symfony Live Paris 2016

## Guard dans la vraie vie
Par Jeremy Romey @jeremyfreeagent (SensioLabs)

Guard est un nouveau composant de sécurité dans Symfony2.8+
  * see https://github.com/symfony/security-guard

Il n'apporte aucune nouvelle feature, mais c'est une facade qui simplifie/unifie
le processus d'authentification.

Avec Guard connecter un nouveau mode d'authentification c'est juste une classe à implémenter,
avec 6 méthodes, et c'est fini, pas de listener, d'authentication provider et autre joyeuseté

Il encapsule les 4 étapes de l'authentication à un seul endroit:

```php
interface GuardAuthenticatorInterface extends AuthenticationEntryPointInterface
{
    public function getCredentials(Request $request);
    public function getUser($credentials, UserProviderInterface $userProvider);
    public function checkCredentials($credentials, UserInterface $user);
    public function createAuthenticatedToken(UserInterface $user, $providerKey);
    public function onAuthenticationFailure(Request $request, AuthenticationException $exception);
    public function onAuthenticationSuccess(Request $request, TokenInterface $token, $providerKey);
    public function supportsRememberMe();
}
```

## R2D2 to BB8
Par Vincent CHALAMON (Les-Tilleuls.coop) @vincentchalamon

Présentation retour d'expérience sur la refonte de The Fork Manager, un outil de Lafourchette
  * https://www.theforkmanager.com

Etat de départ:
  * Une très grosse codebase, en Symfony 2.0
  * Un très gros volume de donnée
  * Dette technique estimé à 38 ans !

Les containtes:
  * Impossible de perdre de la donnée,
  * Pas de down-time accepté pour migrer les données (migration en one shot estimé à 15j de traitement)
  * On va vers Symfony3

Solution :
  * La migration progressive
    * La codebase legacy reste tel qu'elle est
    * on crée une API REST pont entre la nouvelle monture et l'ancienne
    * les données sont migrés vers le nouveau schéma pendant les INSERT/UPDATE

Implémentation :
  * Loader : Le loader à la charge de migrer une entité de l'ancien schéma vers le nouveau
  * Transformer : Quand un objet est écrit dans la nouvelle bdd, on met aussi a jour l'ancienne
    * l'ancien record se présente comme un proxy vers le nouveau pour que l'ancienne app reste fonctionnel

 C'est les bases d'un ETL (Extract/Transform/Load) maison,
