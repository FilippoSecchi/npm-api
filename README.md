# npm-api [![NPM version](https://img.shields.io/npm/v/npm-api.svg?style=flat)](https://www.npmjs.com/package/npm-api) [![NPM monthly downloads](https://img.shields.io/npm/dm/npm-api.svg?style=flat)](https://npmjs.org/package/npm-api)  [![NPM total downloads](https://img.shields.io/npm/dt/npm-api.svg?style=flat)](https://npmjs.org/package/npm-api) [![Linux Build Status](https://img.shields.io/travis/doowb/npm-api.svg?style=flat&label=Travis)](https://travis-ci.org/doowb/npm-api)

> Base class for retrieving data from the npm registry.

## Install

Install with [npm](https://www.npmjs.com/):

```sh
$ npm install --save npm-api
```

## Usage

```js
var NpmApi = require('npm-api');
```

## API

### [NpmApi](index.js#L27)

NpmApi constructor. Create an instance to work with maintainer and repository information.

**Example**

```js
var npm = new NpmApi();
```

### [.view](index.js#L65)

Create a new instance of `View` or get an existing instance to work with npm couchdb views.

**Params**

* `name` **{String}**: Name of the couchdb view to work with.
* `returns` **{Object}** `View`: instance

**Example**

```js
var view = npm.view('byUser');
```

### [.list](index.js#L88)

Create a new instance of `List` or get an existing instance to work with npm couchdb list.

**Params**

* `name` **{String}**: Name of the couchdb list to work with.
* `view` **{String|Object}**: Name or instance of a `view` to work with.
* `returns` **{Object}** `List`: instance

**Example**

```js
var list = npm.list('sortCount', 'byUser');
```

### [.repo](index.js#L119)

Create an instance of a `repo` to work with.

**Params**

* `name` **{String}**: Name of the repo as it's published to npm.
* `returns` **{Object}**: Instance of a `Repo` model to work with.

**Example**

```js
var repo =  npm.repo('micromatch');
```

### [.maintainer](index.js#L142)

Create an instance of a `maintainer` to work with.

**Params**

* `name` **{String}**: Npm username of the maintainer.
* `returns` **{Object}**: Instance of a `Maintainer` model to work with.

**Example**

```js
var maintainer =  npm.maintainer('doowb');
```

## Models

### [BaseModel](lib/models/base.js#L24)

Base model to include common plugins.

**Params**

* `store` **{Object}**: Cache store intance to use.

### [Maintainer](lib/models/maintainer.js#L25)

Maintainer constructor. Create an instance of an npm maintainer by maintainer name.

**Params**

* `name` **{String}**: Name of the npm maintainer to get information about.
* `store` **{Object}**: Optional cache store instance for caching results. Defaults to a memory store.

**Example**

```js
var maintainer = new Maintainer('doowb');
```

### [.repos](lib/models/maintainer.js#L56)

Get the repositories owned by this maintainer.

* `returns` **{Promise}**: Returns array of repository names when promise resolves.

**Example**

```js
maintainer.repos()
  .then(function(repos) {
    console.log(repos);
  }, function(err) {
    console.error(err);
  });
```

### [Repo](lib/models/repo.js#L26)

Repo constructor. Create an instance of an npm repo by repo name.

**Params**

* `name` **{String}**: Name of the npm repo to get information about.
* `store` **{Object}**: Optional cache store instance for caching results. Defaults to a memory store.

**Example**

```js
var repo = new Repo('micromatch');
```

### [.package](lib/models/repo.js#L57)

Get the repo's published package.json.

* `returns` **{Promise}**: Returns the package.json object when promise resolves.

**Example**

```js
repo.package()
  .then(function(pkg) {
    console.log(pkg);
  }, function(err) {
    console.error(err);
  });
```

### [.version](lib/models/repo.js#L89)

Get the repo's published package.json value for the specified version.

**Params**

* `version` **{String}**: Specific version to retrieve.
* `returns` **{Promise}**: Returns the package.json object for the specified version when promise resolves.

**Example**

```js
repo.version('0.2.0')
  .then(function(pkg) {
    console.log(pkg);
  }, function(err) {
    console.error(err);
  });
```

### [.dependencies](lib/models/repo.js#L118)

Get the repo's dependencies for the specified version.

**Params**

* `version` **{String}**: Specific version to retrieve. Defaults to `latest`.
* `returns` **{Promise}**: Returns the dependencies object for the specified version when promise resolves.

**Example**

```js
repo.dependencies()
  .then(function(dependencies) {
    console.log(dependencies);
  }, function(err) {
    console.error(err);
  });
```

### [.devDependencies](lib/models/repo.js#L138)

Get the repo's devDependencies for the specified version.

**Params**

* `version` **{String}**: Specific version to retrieve. Defaults to `latest`.
* `returns` **{Promise}**: Returns the devDependencies object for the specified version when promise resolves.

**Example**

```js
repo.devDependencies()
  .then(function(devDependencies) {
    console.log(devDependencies);
  }, function(err) {
    console.error(err);
  });
```

### [.prop](lib/models/repo.js#L159)

Get the specified property from the repo's package.json for the specified version.

**Params**

* `prop` **{String}**: Name of the property to get.
* `version` **{String}**: Specific version to retrieve. Defaults to `latest`.
* `returns` **{Promise}**: Returns the property for the specified version when promise resolves.

**Example**

```js
repo.prop('author')
  .then(function(author) {
    console.log(author);
  }, function(err) {
    console.error(err);
  });
```

## Registry queries

### [View](lib/view.js#L26)

View constructor. Create an instance of a view associated with a couchdb view in the npm registry.

**Params**

* `name` **{String}**: Name of couchdb view to use.
* `returns` **{Object}**: instance of `View`

**Example**

```js
var view = new View('dependedUpon');
```

### [.query](lib/view.js#L51)

Query the couchdb view with the provided parameters.

**Params**

* `params` **{Object}**: URL query parameters to pass along to the couchdb view.
* `returns` **{Promise}**: Results of the query when promise is resolved.

**Example**

```js
view.query({ group_level: 2, startkey: JSON.stringify(['micromatch']), endkey: JSON.stringify(['micromatch', {}])})
  .then(function(results) {
    console.log(results);
  }, function(err) {
    console.log(err);
  });
```

### [.stream](lib/view.js#L91)

Query the couchdb view with the provided parameters and return a stream of results.

**Params**

* `params` **{Object}**: URL query parameters to pass along to the couchdb view.
* `returns` **{Stream}**: Streaming results of the query.

**Example**

```js
view.stream({ group_level: 2, startkey: JSON.stringify(['micromatch']), endkey: JSON.stringify(['micromatch', {}])})
  .on('data', function(data) {
    console.log(data);
  });
```

### [.url](lib/view.js#L105)

Build a formatted url with the provided parameters.

**Params**

* `params` **{Object}**: URL query parameters.
* `returns` **{String}**: formatted url string

### [List](lib/list.js#L27)

List constructor. Create an instance of a list associated with a couchdb list in the npm registry.

**Params**

* `name` **{String}**: Name of couchdb list to use.
* `view` **{Object}**: Instance of a View to use with the list.
* `returns` **{Object}**: instance of `List`

**Example**

```js
var list = new List('dependedUpon', view);
```

### [.query](lib/list.js#L53)

Query the couchdb list with the provided parameters.

**Params**

* `params` **{Object}**: URL query parameters to pass along to the couchdb list.
* `returns` **{Promise}**: Results of the query when promise is resolved.

**Example**

```js
list.query({ key: JSON.stringify(['micromatch']) })
  .then(function(results) {
    console.log(results);
  }, function(err) {
    console.log(err);
  });
```

### [.url](lib/list.js#L80)

Build a formatted url with the provided parameters.

**Params**

* `params` **{Object}**: URL query parameters.
* `returns` **{String}**: formatted url string

## About

### Related projects

* [base](https://www.npmjs.com/package/base): Framework for rapidly creating high quality, server-side node.js applications, using plugins like building blocks | [homepage](https://github.com/node-base/base "Framework for rapidly creating high quality, server-side node.js applications, using plugins like building blocks")
* [download-stats](https://www.npmjs.com/package/download-stats): Get and calculate npm download stats for npm modules. | [homepage](https://github.com/doowb/download-stats "Get and calculate npm download stats for npm modules.")

### Contributing

Pull requests and stars are always welcome. For bugs and feature requests, [please create an issue](../../issues/new).

### Building docs

_(This project's readme.md is generated by [verb](https://github.com/verbose/verb-generate-readme), please don't edit the readme directly. Any changes to the readme must be made in the [.verb.md](.verb.md) readme template.)_

To generate the readme, run the following command:

```sh
$ npm install -g verbose/verb#dev verb-generate-readme && verb
```

### Running tests

Running and reviewing unit tests is a great way to get familiarized with a library and its API. You can install dependencies and run tests with the following command:

```sh
$ npm install && npm test
```

### Author

**Brian Woodward**

* [github/doowb](https://github.com/doowb)
* [twitter/doowb](https://twitter.com/doowb)

### License

Copyright © 2017, [Brian Woodward](https://github.com/doowb).
Released under the [MIT License](LICENSE).

***

_This file was generated by [verb-generate-readme](https://github.com/verbose/verb-generate-readme), v0.6.0, on December 27, 2017._
