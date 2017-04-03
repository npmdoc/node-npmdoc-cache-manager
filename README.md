# api documentation for  [cache-manager (v2.4.0)](https://github.com/BryanDonovan/node-cache-manager#readme)  [![npm package](https://img.shields.io/npm/v/npmdoc-cache-manager.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-cache-manager) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-cache-manager.svg)](https://travis-ci.org/npmdoc/node-npmdoc-cache-manager)
#### Cache module for Node.js

[![NPM](https://nodei.co/npm/cache-manager.png?downloads=true)](https://www.npmjs.com/package/cache-manager)

[![apidoc](https://npmdoc.github.io/node-npmdoc-cache-manager/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-cache-manager_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-cache-manager/build..beta..travis-ci.org/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-cache-manager/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-cache-manager/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Bryan Donovan"
    },
    "bugs": {
        "url": "https://github.com/BryanDonovan/node-cache-manager/issues"
    },
    "dependencies": {
        "async": "1.5.2",
        "lru-cache": "4.0.0"
    },
    "description": "Cache module for Node.js",
    "devDependencies": {
        "coveralls": "^2.3.0",
        "es6-promise": "^3.0.2",
        "istanbul": "0.4.2",
        "jscs": "2.11.0",
        "jsdoc": "3.3.0",
        "jshint": "2.9.1",
        "mocha": "2.4.5",
        "optimist": "0.6.1",
        "sinon": "1.17.3"
    },
    "directories": {},
    "dist": {
        "shasum": "272e4eee3a4ecebb281fcb086499804e59a6a251",
        "tarball": "https://registry.npmjs.org/cache-manager/-/cache-manager-2.4.0.tgz"
    },
    "gitHead": "ed0a42e5d0b69a39012cb3a58ef1e1bf56d7f33b",
    "homepage": "https://github.com/BryanDonovan/node-cache-manager#readme",
    "keywords": [
        "cache",
        "redis",
        "lru-cache",
        "memory cache",
        "multiple cache"
    ],
    "license": "MIT",
    "main": "index.js",
    "maintainers": [
        {
            "name": "bryandonovan",
            "email": "bdondo@gmail.com"
        }
    ],
    "name": "cache-manager",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git+https://github.com/BryanDonovan/node-cache-manager.git"
    },
    "scripts": {
        "test": "make"
    },
    "version": "2.4.0"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module cache-manager](#apidoc.module.cache-manager)
1.  [function <span class="apidocSignatureSpan">cache-manager.</span>caching (args)](#apidoc.element.cache-manager.caching)
1.  [function <span class="apidocSignatureSpan">cache-manager.</span>callback_filler ()](#apidoc.element.cache-manager.callback_filler)
1.  [function <span class="apidocSignatureSpan">cache-manager.</span>multiCaching (caches, options)](#apidoc.element.cache-manager.multiCaching)
1.  object <span class="apidocSignatureSpan">cache-manager.</span>callback_filler.prototype

#### [module cache-manager.callback_filler](#apidoc.module.cache-manager.callback_filler)
1.  [function <span class="apidocSignatureSpan">cache-manager.</span>callback_filler ()](#apidoc.element.cache-manager.callback_filler.callback_filler)

#### [module cache-manager.callback_filler.prototype](#apidoc.module.cache-manager.callback_filler.prototype)
1.  [function <span class="apidocSignatureSpan">cache-manager.callback_filler.prototype.</span>add (key, funcObj)](#apidoc.element.cache-manager.callback_filler.prototype.add)
1.  [function <span class="apidocSignatureSpan">cache-manager.callback_filler.prototype.</span>fill (key, err, data)](#apidoc.element.cache-manager.callback_filler.prototype.fill)
1.  [function <span class="apidocSignatureSpan">cache-manager.callback_filler.prototype.</span>has (key)](#apidoc.element.cache-manager.callback_filler.prototype.has)



# <a name="apidoc.module.cache-manager"></a>[module cache-manager](#apidoc.module.cache-manager)

#### <a name="apidoc.element.cache-manager.caching"></a>[function <span class="apidocSignatureSpan">cache-manager.</span>caching (args)](#apidoc.element.cache-manager.caching)
- description and source-code
```javascript
caching = function (args) {
    args = args || {};
    var self = {};
    if (typeof args.store === 'object') {
        if (args.store.create) {
            self.store = args.store.create(args);
        } else {
            self.store = args.store;
        }
    } else {
        var storeName = args.store || 'memory';
        self.store = require('./stores/' + storeName).create(args);
    }

    // do we handle a cache error the same as a cache miss?
    self.ignoreCacheErrors = args.ignoreCacheErrors || false;

    var Promise = args.promiseDependency || global.Promise;

    var callbackFiller = new CallbackFiller();

    if (typeof args.isCacheableValue === 'function') {
        self._isCacheableValue = args.isCacheableValue;
    } else if (typeof self.store.isCacheableValue === 'function') {
        self._isCacheableValue = self.store.isCacheableValue;
    } else {
        self._isCacheableValue = function(value) {
            return value !== undefined;
        };
    }

    function wrapPromise(key, promise, options) {
        return new Promise(function(resolve, reject) {
            self.wrap(key, function(cb) {
                Promise.resolve()
                .then(promise)
                .then(function(result) {
                    cb(null, result);
                })
                .catch(cb);
            }, options, function(err, result) {
                if (err) {
                    return reject(err);
                }
                resolve(result);
            });
        });
    }

<span class="apidocCodeCommentSpan">    /**
     * Wraps a function in cache. I.e., the first time the function is run,
     * its results are stored in cache so subsequent calls retrieve from cache
     * instead of calling the function.
     *
     * @function
     * @name wrap
     *
     * @param {string} key - The cache key to use in cache operations
     * @param {function} work - The function to wrap
     * @param {object} [options] - options passed to 'set' function
     * @param {function} cb
     *
     * @example
     *   var key = 'user_' + userId;
     *   cache.wrap(key, function(cb) {
     *       User.get(userId, cb);
     *   }, function(err, user) {
     *       console.log(user);
     *   });
     */
</span>    self.wrap = function(key, work, options, cb) {
        if (typeof options === 'function') {
            cb = options;
            options = {};
        }

        if (!cb) {
            return wrapPromise(key, work, options);
        }

        var hasKey = callbackFiller.has(key);
        callbackFiller.add(key, {cb: cb});
        if (hasKey) { return; }

        self.store.get(key, options, function(err, result) {
            if (err && (!self.ignoreCacheErrors)) {
                callbackFiller.fill(key, err);
            } else if (self._isCacheableValue(result)) {
                callbackFiller.fill(key, null, result);
            } else {
                work(function(err, data) {
                    if (err) {
                        callbackFiller.fill(key, err);
                        return;
                    }

                    if (!self._isCacheableValue(data)) {
                        callbackFiller.fill(key, null, data);
                        return;
                    }

                    if (options && typeof options.ttl === 'function') {
                        options.ttl = options.ttl(data);
                    }

                    self.store.set(key, data, options, function(err) {
                        if (err && (!self.ignoreCacheErrors)) {
                            callbackFiller.fill(key, err);
                        } else {
                            callbackFiller.fill(key, null, data);
                        }
                    });
                });
            }
        });
    };

    /**
     * Binds to the underlying store's 'get' function.
     * @function
     * @name get
     */
    self.get = self.store.get.bind(self.store);

    /**
     * Binds to the underlying store's 'set' function.
     * @function
     * @name set
     */
    self.set = self.store.set.bind(self.store);

    /** ...
```
- example usage
```shell
...
See examples below and in the examples directory.  See ''examples/redis_example'' for an example of how to implement a
Redis cache store with connection pooling.

### Single Store

'''javascript
var cacheManager = require('cache-manager');
var memoryCache = cacheManager.caching({store: 'memory', max: 100, ttl: 10/*seconds*/});
var ttl = 5;
// Note: callback is optional in set() and del().

memoryCache.set('foo', 'bar', {ttl: ttl}, function(err) {
if (err) { throw err; }

memoryCache.get('foo', function(err, result) {
...
```

#### <a name="apidoc.element.cache-manager.callback_filler"></a>[function <span class="apidocSignatureSpan">cache-manager.</span>callback_filler ()](#apidoc.element.cache-manager.callback_filler)
- description and source-code
```javascript
function CallbackFiller() {
    this.queues = {};
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.cache-manager.multiCaching"></a>[function <span class="apidocSignatureSpan">cache-manager.</span>multiCaching (caches, options)](#apidoc.element.cache-manager.multiCaching)
- description and source-code
```javascript
multiCaching = function (caches, options) {
    var self = {};
    options = options || {};

    var Promise = options.promiseDependency || global.Promise;

    if (!Array.isArray(caches)) {
        throw new Error('multiCaching requires an array of caches');
    }

    var callbackFiller = new CallbackFiller();

    if (typeof options.isCacheableValue === 'function') {
        self._isCacheableValue = options.isCacheableValue;
    } else {
        self._isCacheableValue = function(value) {
            return value !== undefined;
        };
    }

<span class="apidocCodeCommentSpan">    /**
     * If the underlying cache specifies its own isCacheableValue function (such
     * as how node-cache-manager-redis does), use that function, otherwise use
     * self._isCacheableValue function.
     */
</span>    function getIsCacheableValueFunction(cache) {
        if (cache.store && typeof cache.store.isCacheableValue === 'function') {
            return cache.store.isCacheableValue;
        } else {
            return self._isCacheableValue;
        }
    }

    function getFromHighestPriorityCachePromise(key, options) {
        return new Promise(function(resolve, reject) {
            getFromHighestPriorityCache(key, options, function(err, result) {
                if (err) {
                    return reject(err);
                }
                resolve(result);
            });
        });
    }

    function getFromHighestPriorityCache(key, options, cb) {
        if (typeof options === 'function') {
            cb = options;
            options = {};
        }

        if (!cb) {
            return getFromHighestPriorityCachePromise(key, options);
        }

        var i = 0;
        async.eachSeries(caches, function(cache, next) {
            var callback = function(err, result) {
                if (err) {
                    return next(err);
                }

                var _isCacheableValue = getIsCacheableValueFunction(cache);

                if (_isCacheableValue(result)) {
                    // break out of async loop.
                    return cb(err, result, i);
                }

                i += 1;
                next();
            };

            cache.store.get(key, options, callback);
        }, function(err, result) {
            return cb(err, result);
        });
    }

    function setInMultipleCachesPromise(caches, opts) {
        return new Promise(function(resolve, reject) {
            setInMultipleCaches(caches, opts, function(err, result) {
                if (err) {
                    return reject(err);
                }
                resolve(result);
            });
        });
    }

    function setInMultipleCaches(caches, opts, cb) {
        opts.options = opts.options || {};

        if (!cb) {
            return setInMultipleCachesPromise(caches, opts);
        }

        async.each(caches, function(cache, next) {
            var _isCacheableValue = getIsCacheableValueFunction(cache);

            if (_isCacheableValue(opts.value)) {
                cache.store.set(opts.key, opts.value, opts.options, next);
            } else {
                next();
            }
        }, function(err, result) {
            cb(err, result);
        });
    }

    function getAndPassUpPromise(key) {
        return new Promise(function(resolve, reject) {
            self.getAndPassUp(key, function(err, result) {
                if (err) {
                    return reject(err);
                }
                resolve(result);
            });
        });
    }

    /**
     * Looks for an item in cache tiers.
     * When a key is found in a lower cache, all higher levels are updated.
     *
     * @param {string} key
     * @param {function} cb
     */
    self.getAndPassUp = function(key, cb) {
        if (!cb) {
            return getAndPassUpPromise(key);
        }

        getFromHighestPriorityCache(key, function(err, result, index) {
            if (err) {
                return cb(err);
            }

            if (index) {
                var cachesToUpdate = caches.slice(0, index);
                async.each(cachesToUpdate, func ...
```
- example usage
```shell
...
var myStore = require('your-homemade-store');
var cache = cacheManager.caching({store: myStore});
'''

### Multi-Store

'''javascript
var multiCache = cacheManager.multiCaching([memoryCache, someOtherCache]);
userId2 = 456;
key2 = 'user_' + userId;
ttl = 5;

// Sets in all caches.
multiCache.set('foo2', 'bar2', {ttl: ttl}, function(err) {
if (err) { throw err; }
...
```



# <a name="apidoc.module.cache-manager.callback_filler"></a>[module cache-manager.callback_filler](#apidoc.module.cache-manager.callback_filler)

#### <a name="apidoc.element.cache-manager.callback_filler.callback_filler"></a>[function <span class="apidocSignatureSpan">cache-manager.</span>callback_filler ()](#apidoc.element.cache-manager.callback_filler.callback_filler)
- description and source-code
```javascript
function CallbackFiller() {
    this.queues = {};
}
```
- example usage
```shell
n/a
```



# <a name="apidoc.module.cache-manager.callback_filler.prototype"></a>[module cache-manager.callback_filler.prototype](#apidoc.module.cache-manager.callback_filler.prototype)

#### <a name="apidoc.element.cache-manager.callback_filler.prototype.add"></a>[function <span class="apidocSignatureSpan">cache-manager.callback_filler.prototype.</span>add (key, funcObj)](#apidoc.element.cache-manager.callback_filler.prototype.add)
- description and source-code
```javascript
add = function (key, funcObj) {
    if (this.queues[key]) {
        this.queues[key].push(funcObj);
    } else {
        this.queues[key] = [funcObj];
    }
}
```
- example usage
```shell
...
}

if (!cb) {
    return wrapPromise(key, work, options);
}

var hasKey = callbackFiller.has(key);
callbackFiller.add(key, {cb: cb});
if (hasKey) { return; }

self.store.get(key, options, function(err, result) {
    if (err && (!self.ignoreCacheErrors)) {
        callbackFiller.fill(key, err);
    } else if (self._isCacheableValue(result)) {
        callbackFiller.fill(key, null, result);
...
```

#### <a name="apidoc.element.cache-manager.callback_filler.prototype.fill"></a>[function <span class="apidocSignatureSpan">cache-manager.callback_filler.prototype.</span>fill (key, err, data)](#apidoc.element.cache-manager.callback_filler.prototype.fill)
- description and source-code
```javascript
fill = function (key, err, data) {
    var waiting = this.queues[key];
    delete this.queues[key];

    if (waiting && waiting.length) {
        waiting.forEach(function(task) {
            (task.cb)(err, data);
        });
    }
}
```
- example usage
```shell
...

var hasKey = callbackFiller.has(key);
callbackFiller.add(key, {cb: cb});
if (hasKey) { return; }

self.store.get(key, options, function(err, result) {
    if (err && (!self.ignoreCacheErrors)) {
        callbackFiller.fill(key, err);
    } else if (self._isCacheableValue(result)) {
        callbackFiller.fill(key, null, result);
    } else {
        work(function(err, data) {
            if (err) {
                callbackFiller.fill(key, err);
                return;
...
```

#### <a name="apidoc.element.cache-manager.callback_filler.prototype.has"></a>[function <span class="apidocSignatureSpan">cache-manager.callback_filler.prototype.</span>has (key)](#apidoc.element.cache-manager.callback_filler.prototype.has)
- description and source-code
```javascript
has = function (key) {
    return this.queues[key];
}
```
- example usage
```shell
...
    options = {};
}

if (!cb) {
    return wrapPromise(key, work, options);
}

var hasKey = callbackFiller.has(key);
callbackFiller.add(key, {cb: cb});
if (hasKey) { return; }

self.store.get(key, options, function(err, result) {
    if (err && (!self.ignoreCacheErrors)) {
        callbackFiller.fill(key, err);
    } else if (self._isCacheableValue(result)) {
...
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
