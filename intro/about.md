# Cycle ORM
[![Latest Stable Version](https://poser.pugx.org/cycle/orm/version)](https://packagist.org/packages/cycle/orm)
[![Build Status](https://travis-ci.org/cycle/orm.svg?branch=master)](https://travis-ci.org/cycle/orm)
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/cycle/orm/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/cycle/orm/?branch=master)
[![Codecov](https://codecov.io/gh/cycle/orm/graph/badge.svg)](https://codecov.io/gh/cycle/orm)
<a href="https://discord.gg/TFeEmCs"><img src="https://img.shields.io/badge/discord-chat-magenta.svg"></a>

Cycle is PHP DataMapper ORM and Data Modelling engine designed to safely work in classic and long-running PHP applications (like [RoadRunner](https://github.com/spiral/roadrunner)). The ORM provides flexible configuration options to model datasets and support dynamic schema configuration. ORM can work with plain PHP objects and support annotation declarations.

Features:
---------
- ORM with many-to-many, many-thought-many and polymorphic relations
- bare PHP objects, ActiveRecord-like objects, [same object type for all entities](tests/ORM/Classless)
- query builder with automatic relation resolution
- eager and lazy loading, proxies support, references support
- runtime configuration with/without code-generation
- column-to-field mapping, value objects support
- single table inheritance
- works with directed graphs and cyclic graphs using IDDFS over command chains
- designed to work in long-running applications, immutable core
- dirty state, sync exceptions do not break entity map state
- supports MySQL, MariaDB, PostgresSQL, SQLServer, SQLite (full mock capability)
- supports global query constraints, UUIDs as PK, soft deletes, auto timestamps
- compatible with Doctrine Collections and Zend Hydrator

Extensions:
---------
| Component | Current Status        
| ---       | ---
cycle/schema-builder | [![Latest Stable Version](https://poser.pugx.org/cycle/schema-builder/version)](https://packagist.org/packages/cycle/schema-builder) [![Build Status](https://travis-ci.org/cycle/schema-builder.svg?branch=master)](https://travis-ci.org/cycle/schema-builder) [![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/cycle/schema-builder/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/cycle/schema-builder/?branch=master) [![Codecov](https://codecov.io/gh/cycle/schema-builder/graph/badge.svg)](https://codecov.io/gh/cycle/schema-builder)
cycle/annotated | [![Latest Stable Version](https://poser.pugx.org/cycle/annotated/version)](https://packagist.org/packages/cycle/annotated) [![Build Status](https://travis-ci.org/cycle/annotated.svg?branch=master)](https://travis-ci.org/cycle/annotated) [![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/cycle/annotated/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/cycle/annotated/?branch=master) [![Codecov](https://codecov.io/gh/cycle/annotated/graph/badge.svg)](https://codecov.io/gh/cycle/annotated)
cycle/migrations | [![Latest Stable Version](https://poser.pugx.org/cycle/migrations/version)](https://packagist.org/packages/cycle/migrations) [![Build Status](https://travis-ci.org/cycle/migrations.svg?branch=master)](https://travis-ci.org/cycle/migrations) [![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/cycle/migrations/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/cycle/migrations/?branch=master) [![Codecov](https://codecov.io/gh/cycle/migrations/graph/badge.svg)](https://codecov.io/gh/cycle/migrations)

