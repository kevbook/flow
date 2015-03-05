flow
========

Simple & basic flow control with no dependencies. Its simple, fast. No BS. Its an extension of `fastsync` https://github.com/joekarl/fastsync.

Works in the browser or in node.js.

#### Covers 4 basic use cases:

* parallel - Run functions in parallel and callback when finished
* series - Run functions in series and callback when finished
* waterfall - Run functions in series while passing results of one function to the next
* asyncMap - Convenience method for mapping an array using asynchronous functions, analogous to an async version of Array.forEach with all tasks kicked off in parallel
* seriesMap - Convenience method for mapping an array using asynchronous functions, analogous to an async version of Array.forEach with all tasks kicked off in series

##Note about function resolution
All functions are resolved immediately, to delay/overhead of next tick of the event loop. If you need to evaluate on next tick, use either setImmediate or process.nextTick. Add a wrapper, probably on your functions.

##Examples
####parallel
  ```js
  flow.parallel([
    function one(cb) {
      setTimeout(function(){
          cb(null, 1);
      }, 100);
    },
    function two(cb) {
      setTimeout(function(){
          cb(null, 2);
      }, 100);
    },
    function three(cb) {
      setTimeout(function(){
          cb(null, 3);
      }, 100);
    },
    function four(cb) {
      setTimeout(function(){
          cb(null, 4);
      }, 100);
    }
  ], function(err, results){
    //100ms later
    console.log(results); //[1, 2, 3, 4]
  });
  ```

####series
  ```js
  flow.series([
    function one(cb) {
      setTimeout(function(){
          cb(null, 1);
      }, 100);
    },
    function two(cb) {
      setTimeout(function(){
          cb(null, 2);
      }, 100);
    },
    function three(cb) {
      setTimeout(function(){
          cb(null, 3);
      }, 100);
    },
    function four(cb) {
      setTimeout(function(){
          cb(null, 4);
      }, 100);
    }
  ], function(err, results){
    //400ms later
    console.log(results); //[1, 2, 3, 4]
  });
  ```

####waterfall
  ```js
  flow.waterfall([
    function(cb) {
      cb(null, 8);
    },
    function split(v, cb) {
      //v = 8
      cb(null, v / 2, v / 2);
    },
    function square(v1, v2, cb) {
      //v1 = 4, v2 = 4
      cb(null, v1 * v2);
    },
    function minus1(v, cb) {
      //v = 16
      cb(null, v - 1);
    }
  ], function(err, result){
    console.log(result); //15
  });
  ```

####parallelMap
**Note** Results can be processed in any order but will be returned in the order submitted

  ```js
  flow.parallelMap([1,2,3], function mult5(v, cb){
    setTimeout(function(){
      cb(null, v * 5);
    }, 100);
  }, function(err, mappedArray){
    console.log(mappedArray); //[5,10,15]
  });
  ```

####seriesMap

  ```js
  flow.seriesMap([1,2,3], function mult5(v, cb){
      setTimeout(function(){
          cb(null, v * 5);
      }, 100);
  }, function(err, mappedArray){
      console.log(mappedArray); //[5,10,15]
  });
  ```
