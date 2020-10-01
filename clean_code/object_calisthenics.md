# Object Calisthenics
Object Calisthenics are programming exercises, formalized as a set of 9 rules invented by Jeff Bay in his book The ThoughtWorks Anthology.
|            | Table of Content                                                                                                           |
| ---        | ------------------                                                                                                         |
| **RULE 1** | [One level of indentation per method](#one-level-of-indentation-per-method)                                                |
| **RULE 2** | [Don't use the ELSE keyword](#don't-use-the-ELSE-keyword)                                                                  |
| **RULE 3** | [Wrap all primitives and Strings in classes](#wrap-all-primitives-and-Strings-in-classes)                                  |
| **RULE 4** | [First class collections](#first-class-collections-a-class-holding-an-array-attribute-should-not-hold-any-other-attribute) |
| **RULE 5** | [One dot per line](#one-dot-per-line)                                                                                      |
| **RULE 6** | [Don't abbreviate](#don't-abbreviate)                                                                                      |
| **RULE 7** | [Keep all classes less than 50 lines](#keep-all-classes-less-than-50-lines)                                                |
| **RULE 8** | [No classes with more than two instance variables](#no-classes-with-more-than-two-instance-variables)                      |
| **RULE 9** | [No getters or setters](#no-getters-or-setters)                                                                            |

## One level of indentation per method.

<div style="-webkit-column-count: 2; -moz-column-count: 2; column-count: 2; -webkit-column-rule: 1px dotted #e0e0e0; -moz-column-rule: 1px dotted #e0e0e0; column-rule: 1px dotted #e0e0e0;">
<div style="display:inline-block; vertical-align: top;"><h2>Bad</h2><div>

```php
// 🚫 Cette méthode contient deux niveaux d'indentation
public function notify(array $contacts)
{
    foreach ($contacts as $contact) {
        if ($contact->isEnabled()) {
            $this->mailer->send($contact);
        }
    }
}
```
</div></div>
<div style="display:inline-block; vertical-align: top;"><h2>Good</h2><div>

```php
// ✅Extraction du code indenté dans une autre méthode
public function notify(array $contacts)
{
    foreach ($contacts as $contact) {
        $this->notifyContact()
    }
}

private function notifyContact(Contact $contact)
{
    if ($contact->isEnabled()) {
        $this->mailer->send($contact);
    }
}
```

```php
// ✅ On filtre en amont de la boucle la liste des contacts
public function notify(array $contacts)
{
    $enabledContacts = array_filter(
      $contacts,
      fn($contact) => $contact->isEnabled()
    );

    foreach ($enabledContacts as $contact) {
        $this->mailer->send($contact);
    }
}
```

```php
// ✅ On demande à l'appelant de nous envoyer directement
// la liste des contacts activés
public function notify(array $enabledContacts)
{
    foreach ($enabledContacts as $contact) {
        $this->mailer->send($contact);
    }
}
```
</div></div>
</div>

## Don't use the ELSE keyword.

<div style="-webkit-column-count: 2; -moz-column-count: 2; column-count: 2; -webkit-column-rule: 1px dotted #e0e0e0; -moz-column-rule: 1px dotted #e0e0e0; column-rule: 1px dotted #e0e0e0;">
<div style="display:inline-block; vertical-align: top;"><h2>Bad</h2><div>

```php
class ItemManager
{
    private $cache;
    private $repository;

    public function __construct($cache, $repository)
    {
        $this->cache = $cache;
        $this->repository = $repository;
    }

    public function get($id)
    {
        if (!$this->cache->has($id)) {
            $item = $this->repository->get($id);
            $this->cache->set($id, $item);
        } else {
            $item = $this->cache->get($id);
        }

        return $item;
    }
}
```
</div></div>
<div style="display:inline-block; vertical-align: top;"><h2>Good</h2><div>

```php
class ItemManager
{
    private $cache;
    private $repository;

    public function __construct($cache, $repository)
    {
        $this->cache = $cache;
        $this->repository = $repository;
    }

    // ✅Utilisation d'early returns
    public function get($id)
    {
        if ($this->cache->has($id)) {
            return $this->cache->get($id);
        }

        $item = $this->repository->get($id);
        $this->cache->set($id, $item);

        return $item;
    }
}
```
</div></div>
</div>

## Wrap all primitives and Strings in classes.

<div style="-webkit-column-count: 2; -moz-column-count: 2; column-count: 2; -webkit-column-rule: 1px dotted #e0e0e0; -moz-column-rule: 1px dotted #e0e0e0; column-rule: 1px dotted #e0e0e0;">
<div style="display:inline-block; vertical-align: top;"><h2>Bad</h2><div>

```php
public function fizzBuzz(int $integer)
{
    if ($integer <= 0) {
        throw new \Exception('Only positive integer is handled');
    }

    if ($integer%15 === 0) {
        return 'FizzBuzz';
    }

    //...
}
```
</div></div>
<div style="display:inline-block; vertical-align: top;"><h2>Good</h2><div>

```php
// Remplacement du int par un objet PositiveInteger
public function fizzBuzz(PositiveInteger $integer)
{
    // ✅Plus de test de validation du paramètre en entrée
    if ($integer->isMultipleOf(15)) {
        return 'FizzBuzz';
    }

    // ...
}

// Utilisation d'un Value Object
class PositiveInteger
{
    private $value;

    public function __construct(int $integer)
    {
      	// ✅Le test de validation de l'entier se fait directement ici
        if ($integer <= 0) {
            throw new \Exception('Only positive integer is handled');
        }

        $this->value = $integer;
    }

    // ✅On peut même ajouter des fonctions liés à cet objet
    public function isMultipleOf(int $multiple)
    {
        return $this->valueinteger%$multiple === 0;
    }
}
```
</div></div>
</div>

<div style="-webkit-column-count: 2; -moz-column-count: 2; column-count: 2; -webkit-column-rule: 1px dotted #e0e0e0; -moz-column-rule: 1px dotted #e0e0e0; column-rule: 1px dotted #e0e0e0;">
<div style="display:inline-block; vertical-align: top;"><h2>Bad</h2><div>

```php
// Remplacement du int par un objet PositiveInteger
public function fizzBuzz(PositiveInteger $integer)
{
    // ✅Plus de test de validation du paramètre en entrée
    if ($integer->isMultipleOf(15)) {
        return 'FizzBuzz';
    }

    // ...
}

// Utilisation d'un Value Object
class PositiveInteger
{
    private $value;

    public function __construct(int $integer)
    {
      	// ✅Le test de validation de l'entier se fait directement ici
        if ($integer <= 0) {
            throw new \Exception('Only positive integer is handled');
        }

        $this->value = $integer;
    }

    // ✅On peut même ajouter des fonctions liés à cet objet
    public function isMultipleOf(int $multiple)
    {
        return $this->valueinteger%$multiple === 0;
    }
}
```
</div></div>
<div style="display:inline-block; vertical-align: top;"><h2>Good</h2><div>

```php
// ✅On passe ici directement un objet contenant uniquement
// des contacts activés.
// On est donc assuré de n'avoir que des contacts actifs
public function notify(EnabledContacts $enabledContacts)
{
    foreach ($enabledContacts as $contact) {
        $this->mailer->send($contact);
    }
}

class EnabledContacts implements \Iterator
{
    private $contacts;

    public function __construct(array $contacts)
    (
        // ✅On ne garde ici que les contacts actifs
        $this->contacts = array_filter(
          $contacts,
          fn(Contact $contact) => $contact->isEnabled()
        );
    )

    // ... définition des méthode de l'interface \Iterator
}
```
</div></div>
</div>

<div style="-webkit-column-count: 2; -moz-column-count: 2; column-count: 2; -webkit-column-rule: 1px dotted #e0e0e0; -moz-column-rule: 1px dotted #e0e0e0; column-rule: 1px dotted #e0e0e0;">
<div style="display:inline-block; vertical-align: top;"><h2>Bad</h2><div>

```php
// 🚫 Deux paramètres sont ici fortement liés
public function findAll(int $start, int $end)
{
  // récupération paginée des données en BDD
}
```
</div></div>
<div style="display:inline-block; vertical-align: top;"><h2>Good</h2><div>

```php
// ✅ On regroupe ici dans une seule classe deux attributs
// qui étaient liés
public function findAll(Pagination $pagination)
{
  $start = $pagination->getStart();
  $end = $pagination->getEnd();

  ...// récupération paginée des données en BDD
}
```
</div></div>
</div>

## First class collections: a class holding an array attribute should not hold any other attribute

<div style="-webkit-column-count: 2; -moz-column-count: 2; column-count: 2; -webkit-column-rule: 1px dotted #e0e0e0; -moz-column-rule: 1px dotted #e0e0e0; column-rule: 1px dotted #e0e0e0;">
<div style="display:inline-block; vertical-align: top;"><h2>Bad</h2><div>

```php
class Newsletter
{
  	private int $id;
    private string $title;

    // 🚫L'objet contient déjà deux attributs, il ne peut
    //   donc pas contenir un array. Il faut l'encapsuler
    //   dans un objet
    private array $subscriberCollection;

    public function getNumberOfSubscriberWhoOpen()
    {
        $subscribersWhoOpen = array_filter(
          $this->subscriberCollection,
          fn($subscriber) => $subscriber->hasOpenNewsletter()
        );

        return \count($subscriberWhoOpen);
    }

    // ....
}
```
</div></div>
<div style="display:inline-block; vertical-align: top;"><h2>Good</h2><div>

```php
class Newsletter
{
    private int $id;
    private string $title;

    // ✅Le tableau est désormais encapsulé dans sa propre classe
    private SubscriberCollection $subscriberCollection;

    public function getNumberOfSubscriberWhoOpen()
    {
        return $this->subscriberCollection
            ->getSubscriberWhoOpen()
            ->count();
    }

    // ....
}

class SubscriberCollection
{
    private array $subscriberCollection;

    // ✅On peut déclarer ici des méthodes "métiers"
    //   liées aux subscribers
    public function getSubscriberWhoOpen()
    {
      	$subscribersWhoOpen = array_filter(
          $this->subscriberCollection,
          fn($subscriber) => $subscriber->hasOpenNewsletter()
        );

        return new static($subscribersWhoOpen);
    }

    // ...
}
```
</div></div>
</div>

## One dot per line.

<div style="-webkit-column-count: 2; -moz-column-count: 2; column-count: 2; -webkit-column-rule: 1px dotted #e0e0e0; -moz-column-rule: 1px dotted #e0e0e0; column-rule: 1px dotted #e0e0e0;">
<div style="display:inline-block; vertical-align: top;"><h2>Bad</h2><div>

```php
class User
{
    private ?Identity $identity;

    public function getIdentity(): ?Identity
    {
        return $this->identity;
    }
}

class Identity
{
    private string $firstName;
    private string $lastName;

    public function getFirstName(): string
    {
        return $this->firstName;
    }

    public function getLastName(): string
    {
        return $this->lastName;
    }
}

$user = new User();
$fullName = sprintf(
  '%s %s',
  // 🚫 Non respect de la loi de demeter
  // 🚫 getIdentity() pourrait très bien retourner null
  //    et cela générerait une erreur
  $user->getIdentity()->getFirstName(),
  $user->getIdentity()->getLastName()
);
```
</div></div>
<div style="display:inline-block; vertical-align: top;"><h2>Good</h2><div>

```php
class User
{
    private ?Identity $identity;

    public function getFullName(): string
    {
      if ($this->identity === null) {
      	return 'John Doe';
      }

      return sprintf(
        '%s %s',
        // La règle d’origine s’applique par exemple au java ou le mot clé « this »
        // n’a pas besoin d’être spécifié dans les classes.
        // On ne compte donc pas ici la première ->
        // car en PHP $this est obligatoire dans les classes
        // pour utiliser un attribut
        $this->identity->getFirstName(),
        $this->identity->getLastName()
      );
    }
}

class Identity
{
    private string $firstName;
    private string $lastName;

    public function getFirstName(): string
    {
        return $this->firstName;
    }

    public function getLastName(): string
    {
        return $this->lastName;
    }
}

$user = new User();
// ✅Respect de la loi de Demeter
// ✅Plus de gestion d'erreur ici
$fullName = $user->getFullName();
```
</div></div>
</div>

## Don't abbreviate.

<div style="-webkit-column-count: 2; -moz-column-count: 2; column-count: 2; -webkit-column-rule: 1px dotted #e0e0e0; -moz-column-rule: 1px dotted #e0e0e0; column-rule: 1px dotted #e0e0e0;">
<div style="display:inline-block; vertical-align: top;"><h2>Bad</h2><div>

```php
🚫 $m->send($contact);

🚫 $cmpMng->getById($id);

🚫 $translator->trans($input);
```
</div></div>
<div style="display:inline-block; vertical-align: top;"><h2>Good</h2><div>

```php
✅ $mailer->send($contact)

✅ $companyManager->getById($contact)

✅ $translator->translate($input)
```
</div></div>
</div>

## Keep all classes less than 50 lines.
## No classes with more than two instance variables.

<div style="-webkit-column-count: 2; -moz-column-count: 2; column-count: 2; -webkit-column-rule: 1px dotted #e0e0e0; -moz-column-rule: 1px dotted #e0e0e0; column-rule: 1px dotted #e0e0e0;">
<div style="display:inline-block; vertical-align: top;"><h2>Bad</h2><div>

```php
class EntityManager
{
    // 🚫 4 attributs
    private EntityRepository $entityRepository;
    private LoggerInterface $logger;
    private MiddlewareInterface $middleware;
    private NotificationService $notificationService;

    public function update(Entity $entity)
    {
        $this->entityRepository->update($entity);

        // 🚫Ces trois traitements pourraient très bien être délocalisés
        //   afin d'éviter de surcharger cette méthode
        //   et pour faciliter l'ajout d'autres traitements plus tard
        $this->logger->debug($entity);
        $this->middleware->sendMessage($entity);
        $this->notificationService->notify($entity);
    }
}
```
</div></div>
<div style="display:inline-block; vertical-align: top;"><h2>Good</h2><div>

```php
class EntityManager
{
    // ✅Moins de dépendances
    // ✅Donc plus facile à mocker pour les tests unitaires
    private EntityRepository $entityRepository;
    private EventDispatcher $eventDispatcher;

    public function update(Entity $entity)
    {
        $this->entityRepository->update($entity);

        // ✅Il sera très facile d'ajouter un autre traitement
        // en ajoutant un listener sur cet événement
        $this->eventDispatcher->dispatch(Events::ENTITY_UPDATE, $entity);
    }
}

// ✅Les traitements ont été délocalisés dans 3 listener distincts
// ✅Classes petites et facilement testables
class EntityToLog
{
    private LoggerInterface $logger;

    public function onUpdate(Entity $entity)
    {
        $this->logger->debug($entity);
    }
}

class EntityToMiddleware
{
    private MiddlewareInterface $middleware;

    public function onUpdate(Entity $entity)
    {
        $this->middleware->sendMessage($entity);
    }
}

class EntityNotification
{
    private NotificationService $notificationService;

    public function onUpdate(Entity $entity)
    {
        $this->notificationService->notify($entity);
    }
}
```
</div></div>
</div>

## No getters or setters.

<div style="-webkit-column-count: 2; -moz-column-count: 2; column-count: 2; -webkit-column-rule: 1px dotted #e0e0e0; -moz-column-rule: 1px dotted #e0e0e0; column-rule: 1px dotted #e0e0e0;">
<div style="display:inline-block; vertical-align: top;"><h2>Bad</h2><div>

```php
class Game
{
    private Score $score;

    public function diceRoll(int $score): void
    {
        $actualScore = $this->score->getScore();
        // 🚫 On modifie en dehors de l'objet sa valeur pour ensuite lui "forcer" le résultat
        $newScore = $actualScore + $score;
        $this->score->setScore($newScore);
    }
}

class Score
{
    private int $score;

    public function getScore(): int
    {
        return $this->score;
    }

    public function setScore(int $score): void
    {
        $this->score = $score;
    }
}
```
</div></div>
<div style="display:inline-block; vertical-align: top;"><h2>Good</h2><div>

```php
class Game
{
    private Score $score;

    public function diceRoll(Score $score): void
    {
        $this->score->addScore($score);
    }
}


class Score
{
    private int $score;

    public function addScore(Score $score): void
    {
      	// ✅On définit ici la logique
        //   d'addition de score
        $this->score += $score->score;
    }
}
```
</div></div>
</div>

