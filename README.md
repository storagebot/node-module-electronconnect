# electron-connect [![Build Status](https://travis-ci.org/Quramy/electron-connect.svg?branch=master)](https://travis-ci.org/Quramy/electron-connect) [![npm version](https://badge.fury.io/js/electron-connect.svg)](http://badge.fury.io/js/electron-connect) ![dependency](https://david-dm.org/quramy/electron-connect.svg)
Utility tool to develop applications with [Electron](http://electron.atom.io/).

Using this in your Node.js scripts(e.g. `gulpfile.js`), you can livereload your Electron app.

It provides the following features:

* start(and restart) Electron process in your Node.js script
* reload renderer process in your Node.js script

## Install
Use npm:

```bash
npm install electron-connect
```

## Usage

### Using with gulp

* gulpfile.js

```js
'use strict';

var gulp = require('gulp');
var electron = require('electron-connect').server.create();

gulp.task('serve', function () {

  // Start Electron process
  electron.start();

  // Restart main process
  gulp.watch('app.js', electron.restart);

  // Reload renderer process
  gulp.watch(['index.js', 'index.html'], electron.reload);
});
```

* app.js(an entry point of your Electron app)

```js
'use strict';

var app = require('app');
var BrowserWindow = require('browser-window');
var client = require('electron-connect').client;

app.on('ready', function () {
  var mainWindow = new BrowserWindow({
    width: 400,
    height: 300
  });
  mainWindow.loadUrl('file://' + __dirname + '/index.html');

  // Connect to server process
  client.create(mainWindow);
});
```

If you want details, see [example/simple](example/simple).

## API References

## server.create([options])

* `option` Object
 * `electron` Object. An `electron-prebuilt` module. Set it If you want to use your falked Electron.
 * `path` String. A path to your `package.json` file(default: `process.cwd()`).
 * `port` Number. WebSocket server port(default: `30080`).

Returns a new `ProcessManager` object.

## Class: ProcessManager

### start([callback])
Starts a server and Electron application process.

### restart([callback])
Kills Electron process if it exsists, and start new one.

This method is useful for callback of your browserProcess sourcecodes' change event.

### reload()
Broadcasts reload event to all connected `Client` object. This method does not kill any Electron processes.

This method is useful for callback of your rendererProcess sourcecodes' change event.

### stop([callback])
Kills Electron process and stops server.

## client.create(browserWindow, [options], [callback])

* `browserWindow` Object
* `options` Object
 * `port` Number
 * `sendBounds` Boolean
* `callback` Function

Creates a new client with `browserWindow`.  `browserWindow` should be an Electron [browser-window](https://github.com/atom/electron/blob/master/docs/api/browser-window.md) instance.
Once a client is created and connects the server, the client can recieve events(e.g. reload).

If `sendBounds` is set(default `true`), the client sends a bounds object when `browserWindow` moves or resizes.
And when `ProcessManager.restart()` is called, the client recover the bounds stored at server.

## License
MIT
