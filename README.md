# QRequest

A [Q-promise](https://github.com/kriskowal/q) [request](https://github.com/request/request) node module wrapper.
It will produce Q-promises for request methods:
* del
* get
* head
* patch
* post
* put

[![Build Status](https://travis-ci.org/motorro/q-wrapped-request.svg?branch=master)](https://travis-ci.org/motorro/q-wrapped-request)
[![Coverage Status](https://coveralls.io/repos/motorro/q-wrapped-request/badge.svg?branch=master&service=github)](https://coveralls.io/github/motorro/q-wrapped-request?branch=master)

## Installation
```
npm install q-wrapped-request
```
Note: The module is using `peerDependencies` to require `Q` and `request`, so your project may have its own
dependencies on both libraries installed.

## Fulfillment

A fulfillment handler will receive an array of request callback params:
* response
* body

So the typical request chain may look like:
```javascript
    (new Request).get("http://echo.jsontest.com/key/value/one/two")
        .spread(function(response, body){
            // Process your body here...
        });
```

## Constructor

A QRequest constructor will accept an options param that will call `defaults` of request internally and use this
request for further requests:
```javascript
    it ("should use bound properties for further requests", function(){
        var r = new Request({url: "http://echo.jsontest.com/key/value/one/two"});
        return r.get(undefined)
            .spread(function(response, body){
                return JSON.parse(body);
            }).should.eventually.deep.equal({
                "one": "two",
                "key": "value"
            });
    });
```

## raw

Both `QRequest` class and its instances will have a `raw` property for convenience.
* QRequest `raw` is just a request itself.
* Instance `raw` property is a request produced by calling `defaults` as a constructor.

## Getting body

For more convenience QRequest expose two utility functions to get body:

### bodyIfStatusOk

Returns `body` if response status is one of the allowed:
```javascript
    it ("should fail on incorrect status using 'bodyIfStatusOk' shortcut", function(){
        return (new Request).get("http://echo.jsontest.com/key/value/one/two")
            .spread(Request.bodyIfStatusOk(500))
            .should.eventually.be.rejectedWith(RangeError);
    });
```

### body

Returns `body` if response status is 200:
```javascript
    it ("should get body if response status is 200 using a 'body' shortcut", function(){
        return (new Request).get("http://echo.jsontest.com/key/value/one/two")
            .spread(Request.body)
            .then(JSON.parse)
            .should.eventually.deep.equal({
                "one": "two",
                "key": "value"
            });
    });
```