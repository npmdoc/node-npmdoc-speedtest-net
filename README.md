# api documentation for  [speedtest-net (v1.3.1)](https://github.com/ddsol/speedtest.net#readme)  [![npm package](https://img.shields.io/npm/v/npmdoc-speedtest-net.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-speedtest-net) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-speedtest-net.svg)](https://travis-ci.org/npmdoc/node-npmdoc-speedtest-net)
#### Speedtest.net client

[![NPM](https://nodei.co/npm/speedtest-net.png?downloads=true)](https://www.npmjs.com/package/speedtest-net)

[![apidoc](https://npmdoc.github.io/node-npmdoc-speedtest-net/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-speedtest-net_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-speedtest-net/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-speedtest-net/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-speedtest-net/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "DDSol"
    },
    "bin": {
        "speedtest-net": "bin/index.js"
    },
    "bugs": {
        "url": "https://github.com/ddsol/speedtest.net/issues"
    },
    "dependencies": {
        "chalk": "^1.1.3",
        "draftlog": "^1.0.12",
        "xml2js": "^0.4.4"
    },
    "description": "Speedtest.net client",
    "devDependencies": {},
    "directories": {},
    "dist": {
        "shasum": "d52a9d7f9566279763112821e6f8172637700a2d",
        "tarball": "https://registry.npmjs.org/speedtest-net/-/speedtest-net-1.3.1.tgz"
    },
    "gitHead": "d5b638797288bc84ed52a5cc463e4f5c18945c4d",
    "homepage": "https://github.com/ddsol/speedtest.net#readme",
    "main": "speedtest.js",
    "maintainers": [
        {
            "name": "ddsol",
            "email": "npm@digitaldesksolutions.com"
        }
    ],
    "name": "speedtest-net",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git+https://github.com/ddsol/speedtest.net.git"
    },
    "scripts": {
        "test": "node test/test"
    },
    "version": "1.3.1"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module speedtest-net](#apidoc.module.speedtest-net)
1.  [function <span class="apidocSignatureSpan">speedtest-net.</span>visual (options, callback)](#apidoc.element.speedtest-net.visual)



# <a name="apidoc.module.speedtest-net"></a>[module speedtest-net](#apidoc.module.speedtest-net)

#### <a name="apidoc.element.speedtest-net.visual"></a>[function <span class="apidocSignatureSpan">speedtest-net.</span>visual (options, callback)](#apidoc.element.speedtest-net.visual)
- description and source-code
```javascript
function visualSpeedTest(options, callback) {
  // We only need chalk and DraftLog here. Lazy load it.
  var chalk = require('chalk');
  require('draftlog').into(console);

  callback = once(callback);

  var test = speedTest(options)
    , log  = function() {}
    , finalData
    , percentage = 0
    , speed = 0
    , bar = console.draft()
    ;

  if (options.log) {
    if (typeof options.log === "function") {
      log = options.log;
    } else {
      log = console.log.bind(console);
    }
  }

  test.on('error', function(err) {
    callback(err);
  });

  test.on('testserver', function(server) {
    log('Using server by ' + server.sponsor + ' in ' + server.name + ', ' + server.country + ' (' + (server.distMi * 0.621371).toFixed
(0) + 'mi, ' + (server.bestPing).toFixed(0) + 'ms)');
  });

  test.on('config', function(config) {
    var client = config.client;
    log('Testing from ' + client.ip + ' at ' + client.isp + ', expected dl: ' + (client.ispdlavg / 8000).toFixed(2) + 'MB/s, expected
 ul: ' + (client.ispulavg / 8000).toFixed(2) + 'MB/s');
  });

  var size = 50;
  var red = (chalk.supportsColor ? chalk.bgRed(' ') : '─');
  var green = (chalk.supportsColor ? chalk.bgGreen(' ') : '▇');

  function prog(what, pct, spd) {
    percentage = pct || percentage;
    speed = spd || speed;

    var complete = Math.round(percentage / 100 * size);
    var barStr = '';

    // What + padding
    barStr += what;
    barStr += ' '.repeat(12 - what.length);

    // Bar
    barStr += green.repeat(complete);
    barStr += red.repeat(size - complete);

    // Percent
    pct = percentage + '%';
    barStr += ' ' + pct;

    // Speed
    barStr += ' '.repeat(8 - pct.length) + speed;

    bar(barStr);
  }

  test.on('downloadprogress', function(pct) {
    prog('download', pct, null);
  });

  test.on('uploadprogress', function(pct) {
    prog('upload', pct, null);
  });

  test.on('downloadspeed', function(speed) {
    log('Download speed: ', speed.toFixed(2) + 'Mbps');

    // Create a new line after each new download
    bar = console.draft();
  });

  test.on('uploadspeed', function(speed) {
    log('Upload speed: ', speed.toFixed(2) + 'Mbps');

    // Create a new line after each new upload
    bar = console.draft();
  });

  test.on('downloadspeedprogress', function(speed) {
    prog('download', null, speed.toFixed(2) + 'Mbps')
  });

  test.on('uploadspeedprogress', function(speed) {
    prog('upload', null, speed.toFixed(2) + 'Mbps')
  });

  test.on('data', function(data) {
    finalData = data;
  });

  test.on('result', function(url) {
    log('Results url: ' + url);
  });

  test.on('done', function(data) {
    callback(null, finalData);
  });

  return test;
}
```
- example usage
```shell
...

'''

Visual use example:
'''js
const speedTest = require('speedtest-net');

speedTest.visual({maxTime: 5000}, (err, data) => {
  console.dir(data);
});
'''

## Options

You can pass an optional 'options' object.
...
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
