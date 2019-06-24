# Simple Relation
An important part of any ORM engine is the ability to handle relations between objects. In order to do so, we will use 
`cycle/annotated` package to describe the relation.

> Deeper review of different relations, their options, and select methods will be given in further sections.

## Describe Entity
First we have to create two entities we want to relate:

```php
/**
 * @Entity
 */
class User
{
    /**
     * @Column(type=primary)
     * @var int
     */
    protected $id;

    /**
     * @Column(type=string)
     * @var string
     */
    protected $name;
    
    public function getId(): int
    {
        return $this->id;
    }

    public function getName(): string
    {
        return $this->name;
    }

    public function setName(string $name): void
    {
        $this->name = $name;
    }
}
```

And the entity we want to relate to:

```php
/**
 * @Entity
 */
class Address
{
    /**
     * @Column(type=primary)
     * @var int
     */
    protected $id;

    /**
     * @Column(type=string)
     * @var string
     */
    protected $city;

    public function getId(): int
    {
        return $this->id;
    }

    public function getCity(): string
    {
        return $this->city;
    }

    public function setCity(string $city): void
    {
        $this->city = $city;
    }
}
```

To relate our entities we have to add a new property to one of them and annotate it properly, we should also add getters and setters.

```php
/**
 * @Entity
 */
class User
{
    // ...

    /**
     * @HasOne(target=Address)
     * @var Address|null
     */
    protected $address;
    
    // ...

    public function getAddress(): ?Address
    {
        return $this->address;
    }

    public function setAddress(Address $address)
    {
        $this->address = $address;
    }
}
```

Once you will update the schema and sync your database schema (or run migration) you are ready to use this relation.

> ORM will automatically create FK on `address.user_id`. We will describe later how to alter this value.

## Store with related entity
To store related entity with it's parent simply `persist` the object which defines the relation:

```php
$user = new User();
$user->setName("Antony");

$address = new Address();
$address->setCity("New York");

$user->setAddress($address);

$t = new ORM\Transaction($orm);
$t->persist($user);
$t->run();
```

The following SQL commands will be produced:

```sql
INSERT INTO "users" ("name") VALUES ('Antony');
INSERT INTO "addresses" ("city", "user_id") VALUES ('New York', 15);
```

You can also store objects separatelly, ORM will automatically link them together:

```php
$t = new ORM\Transaction($orm);
$t->persist($address);
$t->persist($user);
$t->run();
```

Generated command chain will be automatically sorted to keep the proper order of SQL operations.

## Retrieve the related entity
Though Cycle ORM support lazy loading using proxies (see extension `cycle/proxy-factory`) it is recommended to pre-load needed
relations using custom repository methods.

To load related object use `load` method of `Cycle\ORM\Select`. The relation can be loaded using property name:

```php
$result = $orm->getRepository(User::class)
    ->select()
    ->load('address')
    ->fetchAll();

foreach ($result as $user) {
    print_r($user);
}
```

The following construction will produce the SQL similar to:

```sql
SELECT
    "user"."id" AS "c0", 
    "user"."name" AS "c1", 
    "l_user_address"."id" AS "c2", 
    "l_user_address"."city" AS "c3", 
    "l_user_address"."user_id" AS "c4"
FROM "users" AS "user"
LEFT JOIN "addresses" AS "l_user_address"
    ON "l_user_address"."user_id" = "user"."id";
```

Please note, by default ORM will try to load `hasOne` relation using `LEFT JOIN`. You can alter this behaviour and force loading using
external query (post load) by modifying load method:

```php
$result = $orm->getRepository(User::class)
    ->select()
    ->load('address', ['method' => ORM\Select\JoinableLoader::POSTLOAD])
    ->fetchAll();

foreach ($result as $user) {
    print_r($user);
}
```

In this case, the resulted SQL will look like:

```sql
SELECT
    "user"."id" AS "c0", 
    "user"."name" AS "c1"
FROM "users" AS "user";
SELECT
    "user_address"."id" AS "c0", 
    "user_address"."city" AS "c1", 
    "user_address"."user_id" AS "c2"
FROM "addresses" AS "user_address"
WHERE "user_address"."user_id" IN (2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16);
```

> You can load relation level using dot notation `$select->load('post.comments.author')`

## Filter by relation
To filter the selection using related data use method `with`, once such method is invoked you can address to relation fields
in where method using relation name as prefix:

```php
$result = $orm->getRepository(User::class)
    ->select()
    ->with('address')->where('address.city', 'New York')
    ->fetchAll();
```

The SQL:

```sql
SELECT
    "user"."id" AS "c0", 
    "user"."name" AS "c1", 
    "user_address"."id" AS "c2", 
    "user_address"."city" AS "c3", 
    "user_address"."user_id" AS "c4"
FROM "users" AS "user"
INNER JOIN "addresses" AS "user_address"
    ON "user_address"."user_id" = "user"."id"
WHERE "user_address"."city" = 'New York';
```

You can freely combine `load` and `with` method, ORM will help you to avoid collisions:

```php
$result = $orm->getRepository(User::class)
    ->select()
    ->with('address')->where('address.city', 'New York')
    ->load('address')
    ->fetchAll();
```

And the resulted SQL:

```sql
SELECT
    "user"."id" AS "c0", 
    "user"."name" AS "c1", 
    "l_user_address"."id" AS "c2", 
    "l_user_address"."city" AS "c3", 
    "l_user_address"."user_id" AS "c4", 
    "user_address"."id" AS "c5", 
    "user_address"."city" AS "c6", 
    "user_address"."user_id" AS "c7"
FROM "users" AS "user"
LEFT JOIN "addresses" AS "l_user_address"
    ON "l_user_address"."user_id" = "user"."id"
INNER JOIN "addresses" AS "user_address"
    ON "user_address"."user_id" = "user"."id"
WHERE "user_address"."city" = 'New York';
```

You can also force select to use one `JOIN` for both `load` and `with` methods via `using` option:

```php
$result = $orm->getRepository(User::class)
    ->select()
    ->with('address', ['as' => 'user_address'])->where('address.city', 'New York')
    ->load('address', ['using' => 'user_address'])
    ->fetchAll();
```

In this case, only one JOIN will be produced:

```sql
SELECT
    "user"."id" AS "c0", 
    "user"."name" AS "c1", 
    "user_address"."id" AS "c2", 
    "user_address"."city" AS "c3", 
    "user_address"."user_id" AS "c4",
    "user_address"."id" AS "c5",
    "user_address"."city" AS "c6", 
    "user_address"."user_id" AS "c7"
FROM "users" AS "user"
INNER JOIN "addresses" AS "user_address"
    ON "user_address"."user_id" = "user"."id"
WHERE "user_address"."city" = 'New York';
```

## Combined Selections
The strict separation between `load` and `with` methods grants you the ability to control filter and load scope separately. For example, to find a user with any published post and load all user posts with all visible comments:

```php
$users = $orm->getRepository(User::class)
    ->distinct() // required due join of posts
    ->with('posts')->where('posts.published', true)
    ->load('posts.comments', ['where' => ['visible' => true]])
    ->fetchAll();
```
