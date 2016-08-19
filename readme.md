# Adonis Generators

This repo contains generators for Adonis Models and Migrations ala Rails.

## Installation

To install this package run:

```bash
npm install --save adonis-generators
```

Then in `bootstrap/app.js` make the following changes:

* In the `aceProviders` array add: `'adonis-generators/providers/GeneratorsProvider',`
* In the `commands` array add: `'AdonisGenerators/Generate:Migration',`
* In the `commands` array add: `'AdonisGenerators/Generate:Model',`
* In the `commands` array add: `'AdonisGenerators/Generate:Controller',`

## Use - Migrations

To generate a new migration use the following command:

```bash
./ace g:migration User email:string password:string
```

This will create a migration for a `User` model (`users` table) with string columns for `email` and `password`.

To see all of the available field types and relations, check out the [field types table](#field-types).

## Use - Models

To generate a new model use the following command:

```bash
./ace g:migration User email:string password:string profile:hasOne:Profile
```

This will generate a `User` model in the `app/Model` directory.
It will also include the indicated `profile` relation:

```js
profile() {
  return this.hasOne('App/Model/Profile', 'user_id');
}
```

> **NOTE** All relations generated with this pacakge will include the full foreign key.

To see all of the available field types and relations, check out the [field types table](#field-types).

Using the `-m` flag will also generate a migration to match the specified model.

## Use - Controllers

To generate a new controller use the following command (note the `-a` which will create an API controller):

```bash
./ace g:controller User -a email:string password:string profile:hasOne
```

This will create a full RESTful controller:

```js
'use strict'

const User = require('App/Model/User');

class UserController {

  * index(request, response) {
    const users = yield User.with('profile').fetch();

    response.send(users);
  }

  * store(request, response) {
    const input = request.only('email', 'password', 'user_id');
    const user = yield User.create(input);

    response.send(user);
  }

  * show(request, response) {
    const id = request.param('id');
    const user = yield User.with('profile').where({ id }).firstOrFail();

    response.send(user);
  }

  * update(request, response) {
    const input = request.only('email', 'password', 'user_id');
    const id = request.param('id');

    const user = yield User.with('profile').where({ id }).firstOrFail();
    yield user.update(input);

    response.send(user);
  }

  * destroy(request, response) {
    const id = request.param('id');
    const user = yield User.query().where({ id }).firstOrFail();
    yield user.delete();

    response.status(204).send();
  }

}

module.exports = UserController;
```


## Field Types

| Structure                                          | Migration Output                                          | Model Output                                                  |
|----------------------------------------------------|-----------------------------------------------------------|---------------------------------------------------------------|
| field_name:integer                                 | table.integer('field_name')                               | null                                                          |
| field_name:bigInteger                              | table.bigInteger('field_name')                            | null                                                          |
| field_name:text                                    | table.text('field_name')                                  | null                                                          |
| field_name:string                                  | table.string('field_name')                                | null                                                          |
| field_name:boolean                                 | table.boolean('field_name')                               | null                                                          |
| field_name:date                                    | table.date('field_name')                                  | null                                                          |
| field_name:dateTime                                | table.dateTime('field_name')                              | null                                                          |
| field_name:time                                    | table.time('field_name')                                  | null                                                          |
| field_name:timestamp                               | table.timestamp('field_name')                             | null                                                          |
| field_name:binary                                  | table.binary('field_name')                                | null                                                          |
| field_name:json                                    | table.json('field_name')                                  | null                                                          |
| field_name:jsonb                                   | table.jsonb('field_name')                                 | null                                                          |
| field_name:uuid                                    | table.uuid('field_name')                                  | null                                                          |
| field_name:float                                   | table.float('field_name')                                 | null                                                          |
| field_name:decimal                                 | table.decimal('field_name')                               | null                                                          |
| relationName:belongsTo[:ParentModel][:foreign_key] | table.integer('foreign_key').reference('parent_model.id') | return this.belongsTo('App/Model/ParentModel', 'foreign_key') |
| relationName:hasMany[:ChildModel][:foreign_key]    | null                                                      | return this.hasMany('App/Model/ChildModel', 'foreign_key')   |
| relationName:hasOne[:ChildModel][:foreign_key]     | null                                                      | return this.hasOne('App/Model/ChildModel', 'foreign_key')    |
