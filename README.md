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

Docker 1.6.1 or above must be installed with a container named `servertest` running with `cat` and `/bin/bash` capabilities to inject the exec process into. From there, `npm test` will carry out the test.
