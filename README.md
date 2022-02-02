# docker-exec-websocket-client

## Purpose
Client for docker-exec-websocket-server.

## Usage
Client:

```js
var DockerClient = require('../lib/client.js');
var client = new DockerClient({
  url: 'ws://localhost:8081/a' //whole url of websocket, preface with wss if secure
  tty: 'true', //Whether or not we expect VT100 style output, also enables exit codes
  command: '/bin/bash', //Command to be run, can be an array with options such as ['cat', '-E']
  wsopts: {}, //Pass in websocket options for the underlying websocket
});
await client.execute();
process.stdin.pipe(client.stdin);
client.stdout.pipe(process.stdout);
client.stderr.pipe(process.stderr);
client.on('exit', (exitCode) => {
  //exitCode is a number between 0 and 255
  process.exit(exitCode);
});
```
There are also other client events:
* `open` signifies the opening of the websocket
* `pause` and `resume` signify when the server has paused/resumed sending data
* `shutdown` signifies the server was shut down
* `error` signifies that some sort of internal error occured, and may carry a utf-8 payload

## Testing

Ensure Docker is installed.

To test locally:

* Run ``yarn install`` to install the dependencies, including developer dependencies
* Run ``yarn test``

To test with ``docker-compose``:

* Run ``docker-compose build --build-arg NODE_VERSION=16-bullseye``, or change to the desired
  [Node.js image tag](https://hub.docker.com/_/node/)
* Run ``docker-compose run test``
* Repeat ``docker-compose build ...`` when the code changes or you want to try a different Node.js image.
