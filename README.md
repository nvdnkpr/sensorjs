# sensorjs

This module handles all about sensor with powerful and familiar techs.
In other words this sensorjs is not only sensor driver module but also framework like express.

And it's working on linux boards such as the BeagleBone or Raspberry Pi.

![gateway](https://raw.github.com/daliworks/sensorjs/master/doc/image/gateway.png "gateway")

[Here](https://github.com/daliworks/sensorjs/blob/master/lib/sensor/README.md) is currently available sensors.

## Installation

    $ npm install sensorjs 

## sensor.js URL scheme
### sensorjs://[{gateway}]/{sensor network}[:{bus id}]/{address}/{sensor model}/{sensor id}
 - example
    - ```sensorjs:///ble/000A3A58F310/dht22/22-000003a7f590```
    - ```sensorjs:///i2c:1/33/dht33/22-000003a7f590```
    - ```sensorjs:///gpio/22/singleled/r222```

## Example - get

```javascript
var sensor = require('sensorjs').sensor;

sensor.discover('oneWire', function (err, ids) {
  ids.forEach(function (id) {
    var thermometer = sensor.createSensor('ds18b20', id);

    thermometer.get(function (err, data) {
      if (!err) {
        console.log(data);
      } 
    });
  });
});
```

## example - listen and transport
```javascript
var connect = require('sensorjs'),
   sensor = require('sensorjs').sensor;

var app = connect().
  use(connect.filter({$between: [-50, 50]})). // filter: passing between -50 and 50
  use(connect.average(5 /*duration*/)).       // reduce: to an average every 5 sec.
  use(connect.queue(100)).                    // buffering max # of 100.
  // transport(mqtt, localStorage, websocket and etc)
  use(connect.websocket('http://yourhost.com', 'temperature/{id}'/*topic*/));

sensor.discover('oneWire', function (err, ids) {
  ids.forEach(function (id) {
    var thermometer = sensor.createSensor('ds18b20', id);
    app.listen(thermometer);
  });
});
```

## example - custom filter and route
```javascript
var app = connect().
  use(connect.filter({$between: [-50, 50]})). // filter: passing between -50 and 50
  use(connect.average(5 /*duration*/)).       // reduce: to an average every 5 sec.
  use(connect.queue(100)).                    // buffering max # of 100.
  use(function (data, next) {                 // custom middleware
    if (Math.max.apply(null, data.queue) < data.value) {
      data.hint = 'new record';
    } 
    next();
  }).
  use('/oneWire/22/ds18b20', function (data, next) {
    if (data.value > 30) {
      data.message = 'too hot';  
    }
    next();
  }).
  use('/gpio/23/dht11', function (data, next) {
    if (data.value < 20) {
      data.message = 'too dry';  
    }
    next();
  }).
  use(connect.websocket('http://yourhost.com', 'temperature/{id}'/*topic*/));
```

There are more [examples](https://github.com/daliworks/sensorjs/tree/master/example).

## Contributor

[https://github.com/daliworks/sensorjs/graphs/contributors](https://github.com/daliworks/sensorjs/graphs/contributors)

## License 

(The MIT License)

Copyright (c) 2013 [Daliworks Inc](http://www.daliworks.co.kr)

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the 'Software'), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.


[@sensorjs](https://twitter.com/sensorjs)
