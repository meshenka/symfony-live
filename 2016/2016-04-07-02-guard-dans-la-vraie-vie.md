# Symfony Live Paris 2016

## Guard dans la vraie vie
Par Jeremy Romey [@jeremyfreeagent](https://twitter.com/jeremyFreeAgent) (SensioLabs)

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
Il a montré par la suite plusieurs implémentations
  * authentification via token dans l'url
  * authentification via header http customs
  * authentification via oauth2
  * authentification via cas
  * Chainage de différentes méthodes
  
 
  

