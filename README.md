# Ttyrec.js

a native implementation for encoding and decoding ttyrec files.

[![Build Status](https://travis-ci.org/jedi4ever/ttyrec.js.png)](https://travis-ci.org/jedi4ever/ttyrec.js)

# Usage
This implements a duplex / `transform` stream v2 .  Therefore only node v0.10.x is supported.

It also provides two simple executable `ttyrec` and `ttyplay` commands.

See [Ttyrec format specification](http://en.wikipedia.org/wiki/Ttyrec#Technical_file_format_specification)

## Write recStream
```
  var fs = require('fs');
  var pty = require('pty');

  var ttyrec = require('ttyrec');
  var ttyrecStream = new ttyrec.recStream();

  var fileStream = fs.createWriteStream('ttyrecord');

  var _pty = pty.spawn('/bin/bash');
  process.stdin.pipe(_pty);

  _pty.pipe(ttyrecStream);
  ttyrecStream.pipe(fileStream);
```

## Read playStream
```
  var fs = require('fs');

  var ttyrec = require('ttyrec');
  var fileStream = fs.createReadStream('ttyrecord');

  var ttyplayStream = new ttyrec.playStream();

  // Play at half the speed
  ttyplayStream.setSpeed(0.5);

  fileStream.pipe(ttyplayStream);
  ttyplayStream.pipe(process.stdout);
```

## Encode
```
  var ttyrec = require('ttyrec');
  var encoder = ttyrec.encoder;
  var sec = 0;
  var usec = 10;
  var record = encoder.encode(sec, usec, new Buffer('abc');
```

## Decode (parseBuffer)
```
  var ttyrec = require('ttyrec');
  var decoder = ttyrec.decoder;
  var results = decoder.decode(arecord);

  // This returns an array of
  // [0] = records
  // [1] = rest of chunk not parsed
  var records = result[0];
  var record = records[0];
  var rest = result[1];

  var header = record.header;
  console.log(header.sec, header.usec, header.length)
  var packet = record.packet; // Buffer
  console.log(packet.toString());

```

# Limitations
- only handles buffer streams(non-encoded streams)
- only works on v0.10.x

# Todo
- enhance the executables to mimic arguments from real ttyrec and ttyplay (almost, need peek + help)
- make v0.11 and v0.8 compatible
- avoid too much recursion
- handle special resizing escape codes for ttyrec
- browserify this code (almost, only process.hrtime does not exist in browser)
- help page
- ttytime
