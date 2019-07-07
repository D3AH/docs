# Select Entity
Cycle ORM provides multiple options to select entity data from the database. 
The most common and recommended method to use the associated entity repository.

## Using Repository
To access repository associated with specific entity use method `getRepository` of orm service:

```php
$r = $orm->getRepository(User::class);
```

You can request repository instance using entity class name or it's role name:

```php
$r = $orm->getRepository("user");
```

The Repository provides multiple methods to select the entity.

To find the entity using it's primary key:

```php
$entity = $repository->findByPK(1);
```

> Note, the method will return `null` if no entity found.

To find enity by any of it's field(s) use:

```php
$entity = $repository->findOne([
  'name' => 'Antony'
]);
```

> Field names will be automatically mapped to appropriate column names.

You can use any amount of fields in request:

```php
$entity = $repository->findOne([
  'name'    => 'Antony',
  'balance' => 100 
]);
```

If you repository an instance of `Cycle\ORM\Select\Repository` (SQL) you can also use combined expressions:

```php
$entity = $repository->findOne([
  'name'    => 'Antony',
  'balance' => ['>=' => 100]
]);
```

> You can read more about compound expressions [here](/query-builder/complex.md).

To find multiple entitied use:

```php
foreach($repository->findAll(['status' => 'active']) as $e) {
  // ...
}
```

## Working with SelectQuery
If repository entity is an instance of `Cycle\ORM\Select\Repository` (default SQL repository) you are also albe to get access
to low level method `select` which gives you ability to compile more complex queries or pre-load related entities:

```php
$result = $repository->select()->where('balance', '>', 1)->load('address')->fetchAll();
```

> It's recommended to avoid usage of `select` method outside of repository classes and instead expose [custom](/basic/respository.md) find methods. 

> You can read more about methods available in select queries [here](https://spiral-framework.com/guide/database-builders).

## The repository Scope
Please note, in Cycle ORM the Repository object is only responsible for entity retrieval, all persist operations must be handled by transaction, entity mappers and command chains.
