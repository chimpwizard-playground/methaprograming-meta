# Code generation with meta cli

```yaml
by: иÐгü
email: ndru@chimpwizard.com
date: 03.07.2019
version: draft
```

---

Methaprograming is for lazy coders, as a great coders the goal is to produce the best functionality with less typing, so if you have the same opinion methaprograming is dedicated to achieve that.

This POC goal is to explore how to extend the meta cli framework and use it appropertly to work with microservices.

## Install

- npm run meta-install
- npm run meta-link-all-global
- npm i -g

## How to extend the framework

At the end it is really simple, just cerate a package that starts with 'meta-' to comply with 'commander' subcomands standards and the meta cli will find it and use it.

A hello world sample will look like this

First you need an index.js that looks like this

```js
module.exports.register = (program) => {

  program
    .command('helloworld', 'run helloworld commands for your meta repo and child git repositories')

}
```
Then you need to create an entry point and register it on our package.json

```json
{
  "name": "meta-helloworld",
  "version": "0.0.0",
  "description": "helloworld plugin for meta",
  "main": "index.js",
  "bin": {
    "meta-helloworld": "./bin/meta-helloworld",
    "meta-helloworld-hi": "./bin/meta-helloworld-hi"
  },

  ...
```

Your entry point will look like this

```js
#!/usr/bin/env node

let loaded = false;

if (loaded) return process.kill();

const program = require('commander');
const debug = require('debug')('meta-helloworld');

program
  .command('hi', 'Say hi!!!')
  .parse(process.argv);

loaded = true;
```

Then you can create several subcomands for your command, here is the 'hi' subcommand

```js
#!/usr/bin/env node

const debug = require('debug')('meta-npm-clean');
const getMetaFile = require('get-meta-file');
const loop = require('loop');
const path = require('path');
const program = require('commander');


program
  .version('0.1.0')
  .parse(process.argv);


const meta = getMetaFile();
const projects = meta.projects;
const folders = Object.keys(projects).map((folder) => { return path.resolve(folder); });

folders.unshift(process.cwd());

loop({
  command: 'echo hi',
  directories: folders.reverse()
});
```


## Throubleshooting

### cannot find file

There is a bug on the find-module-bin that can be fixed adding this line

```js
if (!fs.existsSync(p)) {
  p = path.join(nmp, binFileName, 'bin', binFileName);
}
```

But for the sake of be able to use the tool as-is the solution is to clone meta and add the additiona meta-<command> as a dependency and install meta directly from the folder.

## Reference

- https://github.com/mateodelnorte/meta
- https://medium.com/@patrickleet/developing-a-plugin-for-meta-bd2e9c39882d
