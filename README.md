[![view on npm](http://img.shields.io/npm/v/lws.svg)](https://www.npmjs.org/package/lws)
[![npm module downloads](http://img.shields.io/npm/dt/lws.svg)](https://www.npmjs.org/package/lws)
[![Build Status](https://travis-ci.org/lwsjs/lws.svg?branch=master)](https://travis-ci.org/lwsjs/lws)
[![Dependency Status](https://david-dm.org/lwsjs/lws.svg)](https://david-dm.org/lwsjs/lws)
[![Coverage Status](https://coveralls.io/repos/github/lwsjs/lws/badge.svg?branch=master)](https://coveralls.io/github/lwsjs/lws?branch=master)
[![js-standard-style](https://img.shields.io/badge/code%20style-standard-brightgreen.svg)](https://github.com/feross/standard)

# lws

A modular server application shell for creating a personalised local web server to support productive, full-stack Javascript development.

# Why is it suitable for development

Because it's something you can run quickly from the command line. No code required. Create and share features, run your own bespoke feature stack suiting your requirements at that particular moment.

## Command line usage

`lws` is a command-line tool, install it by running `npm install -g lws`. Here's a quick look at the usage:

```
$ lws --help

lws

  A modular server application shell for creating a personalised local web server to support productive, full-stack Javascript development.

Synopsis

  $ ws [--verbose] [--config-file file] [<server options>] [<middleware options>]
  $ ws --config
  $ ws --help
  $ ws --version

General

  -h, --help               Print these usage instructions.
  --config                 Print the active config.
  -c, --config-file file   Config filename to use, defaults to "lws.config.js".
  -v, --verbose            Verbose output.
  --version                Print the version number.

Server

  -p, --port number     Web server port.
  --hostname string     The hostname (or IP address) to listen on. Defaults to 0.0.0.0.
  --stack feature ...   Feature stack.
  --key file            SSL key. Supply along with --cert to launch a https server.
  --cert file           SSL cert. Supply along with --key to launch a https server.
  --https               Enable HTTPS using a built-in key and cert, registered to the domain
                        127.0.0.1.

Middleware

  No middleware specified.

  Project home: https://github.com/lwsjs/lws
```

Running `lws` launches a web server.

```
$ lws
Serving at http://mbp.local:8000, http://127.0.0.1:8000, http://192.168.0.32:8000
```

However, by default it's empty. Any resource requested will return `404 Not Found`.

```
$ curl -I http://127.0.0.1:8000/README.md

HTTP/1.1 404 Not Found
Date: Wed, 22 Mar 2017 20:41:07 GMT
Connection: keep-alive
```

So, install one or more features and pass their names to `--stack`. Notice the "Middleware" section of the usage guide now contains additional options added by the `lws-static` feature:

```
$ npm install lws-static

$ npm --stack lws-static --help

...

Middleware

  -d, --directory path     Root directory, defaults to the current directory.
  --static.maxage number   Browser cache max-age in milliseconds.
  --static.defer           If true, serves after `yield next`, allowing any downstream middleware to
                           respond first.
  --static.index path      Default file name, defaults to `index.html`.

```

By launching `lws` with this feature we're now able to serve static files.

```
$ lws --stack lws-static
Serving at http://mbp.local:8000, http://127.0.0.1:8000, http://192.168.0.32:8000

$ curl -I http://127.0.0.1:8000/README.md

HTTP/1.1 200 OK
Content-Length: 3286
Last-Modified: Wed, 22 Mar 2017 00:22:21 GMT
Cache-Control: max-age=0
Content-Type: text/x-markdown; charset=utf-8
Date: Wed, 22 Mar 2017 20:39:18 GMT
Connection: keep-alive
```

You can use pre-built features or make your own.

```js
class Feature {
  optionDefinitions () {
    return [
      { name: 'team', type: String }
    ]
  }
  middleware (options) {
    return (ctx, next) => {
      ctx.body = `Your team: ${options.team}`
      await next()
    }
  }
}

module.exports = Feature
```

If you want a server with all the common features pre-installed, look at [local-web-server](https://github.com/75lb/local-web-server).

## Install

Command-line tool:

```
$ npm install -g lws
```

Install the API for use in nodejs:

```
$ npm install lws --save
```

# API Reference

<a name="module_lws"></a>

## lws
Creating command-line web servers suitable for full-stack javascript development.


* [lws](#module_lws)
    * [Lws](#exp_module_lws--Lws) ⏏
        * [new Lws([options])](#new_module_lws--Lws_new)
        * [.app](#module_lws--Lws.Lws+app) : <code>Koa</code>
        * [.server](#module_lws--Lws.Lws+server) : <code>http.Server</code> \| <code>https.Server</code>
        * [.features](#module_lws--Lws.Lws+features) : <code>Array.&lt;Feature&gt;</code>
        * [.start()](#module_lws--Lws+start)

<a name="exp_module_lws--Lws"></a>

### Lws ⏏
**Kind**: Exported class  
<a name="new_module_lws--Lws_new"></a>

#### new Lws([options])

| Param | Type | Description |
| --- | --- | --- |
| [options] | <code>object</code> | Server options |
| [options.port] | <code>number</code> | Port |
| [options.hostname] | <code>string</code> | The hostname (or IP address) to listen on. Defaults to 0.0.0.0. |
| [options.config-file] | <code>string</code> | Config file, defaults to 'lws.config.js'. |
| [options.stack] | <code>Array.&lt;string&gt;</code> \| <code>Array.&lt;Features&gt;</code> | Array of feature classes, or filenames of modules exporting a feature class. |
| [options.https] | <code>boolean</code> | Enable HTTPS using a built-in key and cert, registered to the domain 127.0.0.1. |
| [options.key] | <code>string</code> | SSL key. Supply along with --cert to launch a https server. |
| [options.cert] | <code>string</code> | SSL cert. Supply along with --key to launch a https server. |

**Example**  
```js
const Lws = require('lws')
const lws = new Lws()
lws.start({ https: true})
```
<a name="module_lws--Lws.Lws+app"></a>

#### lws.app : <code>Koa</code>
The [Koa application](https://github.com/koajs/koa/blob/master/docs/api/index.md#application) instance in use.

**Kind**: instance property of [<code>Lws</code>](#exp_module_lws--Lws)  
<a name="module_lws--Lws.Lws+server"></a>

#### lws.server : <code>http.Server</code> \| <code>https.Server</code>
The node server in use, an instance of either [http.Server](https://nodejs.org/dist/latest-v7.x/docs/api/http.html#http_class_http_server) or [https.Server](https://nodejs.org/dist/latest-v7.x/docs/api/https.html#https_class_https_server).

**Kind**: instance property of [<code>Lws</code>](#exp_module_lws--Lws)  
<a name="module_lws--Lws.Lws+features"></a>

#### lws.features : <code>Array.&lt;Feature&gt;</code>
Feature instances

**Kind**: instance property of [<code>Lws</code>](#exp_module_lws--Lws)  
<a name="module_lws--Lws+start"></a>

#### lws.start()
Start the app.

**Kind**: instance method of [<code>Lws</code>](#exp_module_lws--Lws)  


# Feature interface

<a name="module_feature"></a>

## feature

* [feature](#module_feature)
    * [Feature](#exp_module_feature--Feature) ⏏
        * [new Feature(lws)](#new_module_feature--Feature_new)
        * [.optionDefinitions()](#module_feature--Feature+optionDefinitions) ⇒ <code>OptionDefinition</code> \| <code>Array.&lt;OptionDefinition&gt;</code>
        * [.middleware()](#module_feature--Feature+middleware) ⇒ <code>KoaMiddleware</code>
        * [.ready(lws)](#module_feature--Feature+ready)

<a name="exp_module_feature--Feature"></a>

### Feature ⏏
Feature interface.

**Kind**: Exported class  
<a name="new_module_feature--Feature_new"></a>

#### new Feature(lws)
localWebServer instance passed to constructor in case feature needs access to http server instance.


| Param | Type |
| --- | --- |
| lws | <code>Lws</code> | 

<a name="module_feature--Feature+optionDefinitions"></a>

#### feature.optionDefinitions() ⇒ <code>OptionDefinition</code> \| <code>Array.&lt;OptionDefinition&gt;</code>
Return one or more options definitions to collect command-line input

**Kind**: instance method of [<code>Feature</code>](#exp_module_feature--Feature)  
<a name="module_feature--Feature+middleware"></a>

#### feature.middleware() ⇒ <code>KoaMiddleware</code>
Return one of more middleware functions with three args (req, res and next). Can be created by express, Koa or hand-rolled.

**Kind**: instance method of [<code>Feature</code>](#exp_module_feature--Feature)  
<a name="module_feature--Feature+ready"></a>

#### feature.ready(lws)
Called once the server is launched and ready to accept connections.

**Kind**: instance method of [<code>Feature</code>](#exp_module_feature--Feature)  

| Param | Type |
| --- | --- |
| lws | <code>Lws</code> | 



* * *

&copy; 2016-17 Lloyd Brookes \<75pound@gmail.com\>. Documented by [jsdoc-to-markdown](https://github.com/jsdoc2md/jsdoc-to-markdown).
