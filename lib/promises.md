## Promises

Promises is simply a way to dealing with Async calls.

*Async call*  
is a calls that can't return a result at the same moment on invokation, best example for it is fetching data from the server. 
When fetching data you will have to wait until the data will come back and only then you cal use the data. 

<br/>

#### example of the problem
```js
let asyncfunc = function() {
  let value;
  setTimeout(() => {
    value = "value here";
  }, 1000);
  console.log(value); // undefined
};
```


in the example above we use `setTimeout` to demonstrate a call to the server. As you can see when we trying to access to `value` we getting `undefined` thats because `setTimeout` is invoked BUT it will take another 1000ms until it will asign the value to `value` and until it happaned - the `console.log(value)` statement will be invoked.

to make this work we used to use callbacks which has bunch of downsided for this kind of tesks - one of them called "callback hell" (google it)

<br/>

#### example of the solution
```js
let setValue = new Promise(resolve => {
  setTimeout(() => {
    resolve("value here");
  }, 1000);
});

setValue
  .then(res => {
    console.log(res); // value here
  })
  .catch(e => console.log(e));
```

As you can see - we use the `Promise` constructor to create a new promise so later we can use it's resolved value as we like. 

what does it means is - `setTimeout` getting invoked just like in the first example BUT now - because we use `Promise` - it will wait until the `setTimeout` block will finished and we then could access the resolved data inside the `then` method of `Promise`.

but how about more real life example? 

<br/>

#### error handling

The example above might work in theory, but in real life things wont always be that simple. 
As we mentioned before - the main use of `promises` is fetching data from the server - and sometimes the server wont work as expected and the data you expecting is isn't the data that has being returned. 
For these cases we have the second argument of the `Promise` callback and we call it `reject`. 

`reject` is pretty simple - it's basically used to tell the `Promise` what to return if the data you expected isnt returned.
and just as we get the data from `resolve` using `then` - we can get the data from `reject` using `catch`

<br/>

#### `reject` example
```js
let promise = function(val) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (typeof val == "object") {
        resolve("this is true");
      } else {
        reject("this isnt true");
      }
    }, 1000);
  });
};

promise({})
  .then(res => console.log(res))
  .catch(e => console.log(e));
```
Above you can see a little more complex example of `Promise`. 

So whats going on?
first we creating `function` that take an argument. This function is returning new `Promise`. 
Lets pouse here. 

So what does it means? 
it means now we can invoke `promise()` and its value will be an unresolved promise. Why unresolved? because we didn't actually used the returned values from this promise but we simply created one. 

So as we said - the function return a promise that we later can use. The promise is getting two args - `resolve` and `reject` which will be used to returning the resolved value IF the condition is met, or returning the rejected value IF the condition isnt met. As earlier we use `setTimeout` to demonstrate the behavior of fetching data.

In `setTimeout` we simply saying , if `val` is an object - return `this is true`, if not? return `this isnt true`.

Then we invoking that function with an emply object as the argument by doing `promise({})` , and chaining it to `then` and `catch` to use the values returned from each of the methods. 

<br/>

### `async` / `await`

while using `Promise` to handle async calls - ES6 present to us even better way to do it by using `async` and `await`.

`async` and `await` are two new keywords that ment to make promises simpler. And imo they succssed. 

### Explanation

#### `async`
```js
let asyncFunc = async function() { 

  return let somecode;
  
}
```
the syntax of `async` is basically ataching the keyword infront of `function` and all it does it to turn this function into a promise - which means we can later invoke `asyncFunc` and use its returned value with `then` and `catch`.
cool right? well it's not all! 

<br/>

#### `await`

***note*** `await` can only be used inside a `async` function block.

<br/>

`await` is getting attached before any async function call and what it does is basically HOLD the code until the function that it's being attached to won't resolved. So we can use the values returned from the async call inside the original block - without the use of chaining `then`! 

```js
let asyncAwait = async function() {
  let returned;

  returned = await new Promise(resolve => {
    setTimeout(() => {
      resolve("value");
    }, 1000);
  });

  console.log(returned);
};
```

in this exapmle we combining the old `Promise` constructor with `await` which gives us a way to access the resolved value from `Promise` without chaining the function but with simply ataching `await` to `Promise`.
Now the `new Promise` block will be called and it wont move to the next block which in `console.log()` until the promise wont resolve! and by the time it resolved - `console.log` will be called with the expected value.



#### `async` / `await` error hendling

to handle errors in `async` / `await` block - we simply wrap the block with 
```js
  try {

  } catch(e) {

  }
```
it will look something like that 
```js

let asyncAwait = async function(val) {
  let returned;
  try {
    returned = await new Promise((resolve, reject) => {
      setTimeout(() => {
        if (typeof val === "object") {
          resolve("value");
        } else {
          reject("not value");
        }
      }, 1000);
    });
  } catch (e) {
    returned = e;
  }

  console.log(returned);
};

asyncAwait(true);
```

So what we doing here is wraping the whole function with `try catch` statement and dealing the `rejected` value inside the `catch` statement just as we would do in a regulat `try catch` block.