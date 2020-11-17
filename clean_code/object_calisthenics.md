# Object Calisthenics
Object Calisthenics are programming exercises, formalized as a set of 9 rules invented by Jeff Bay in his book The ThoughtWorks Anthology.

Credits to [jimmy klein](https://www.jimmyklein.fr/) for the code examples.

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
// 🚫 This method has 2 levels of indentation
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
// ✅ Indented code was extracted into a method
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
// ✅ Filtering first, looping after
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
// ✅ Asking the caller to send us directly
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

    // ✅ Using guard clause
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
// Subtituting the int with a PositiveInteger object
public function fizzBuzz(PositiveInteger $integer)
{
    // ✅ No more validation upon the entry parameter
    if ($integer->isMultipleOf(15)) {
        return 'FizzBuzz';
    }

    // ...
}

// Using a Value Object
class PositiveInteger
{
    private $value;

    public function __construct(int $integer)
    {
      	// ✅ Validation upon the integer is written directly here
        if ($integer <= 0) {
            throw new \Exception('Only positive integer is handled');
        }

        $this->value = $integer;
    }

    // ✅ You can also add functions tied to that object
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
// 🚫 The two parameters here are tightly coupled
public function findAll(int $start, int $end)
{
  // paginated recovery of data from the database
}
```
</div></div>
<div style="display:inline-block; vertical-align: top;"><h2>Good</h2><div>

```php
// ✅ Regrouping them inside one class
public function findAll(Pagination $pagination)
{
  $start = $pagination->getStart();
  $end = $pagination->getEnd();

  ...// paginated recovery of data from the database
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

    // 🚫 The object has alreadyy two attributes, hence he 
    //    cannot hold an array. It should be encapsulated into an object
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

    // ✅ The array is now encapsulated in its own class
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

    // ✅ We can declare here the "business" methods
    //   tied to the subscribers
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
  // 🚫 Law of Demeter not respected
  // 🚫 getIdentity() may return null one day
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
        // The keyword « this » is an exception
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
// ✅ The Law of Demeter is respected
// ✅ No more error handling at this level
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
    // 🚫 4 attributes
    private EntityRepository $entityRepository;
    private LoggerInterface $logger;
    private MiddlewareInterface $middleware;
    private NotificationService $notificationService;

    public function update(Entity $entity)
    {
        $this->entityRepository->update($entity);

        // 🚫 These 3 treatments could be moved somewhere else
        //   in order to not overload this method
        //   and to make it easier to add other treatments in the future
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
    // ✅ Fewer dependencies
    // ✅ Hence easier to mock for unit tests
    private EntityRepository $entityRepository;
    private EventDispatcher $eventDispatcher;

    public function update(Entity $entity)
    {
        $this->entityRepository->update($entity);

        // ✅ It'll be quite easy to add another treatment
        // Just add a listener on this event
        $this->eventDispatcher->dispatch(Events::ENTITY_UPDATE, $entity);
    }
}

// ✅ The treatment have moved away in 3 dedicated listeners
// ✅ Small classes that are easy to test
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
        // 🚫 The object is not responsible of changing its own attribute value
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
      	// ✅ The addition logic belongs here,
        //    Inside the object itself
        $this->score += $score->score;
    }
}
```
</div></div>
</div>

