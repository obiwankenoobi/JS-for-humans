# *`this`* 
This is a special keyword that each function gets to identify the scope it belongs to. 

*NOTE* in JS we have a method `call` which allows you to bind context and reuse code on different objects:
```js

// <contex> is the obj name the prop belong to
function identify() {
  return this.name
}

let me = {
  name: "Arty"
}

let you = {
  name: "Yosef"
}

identify.call(me)  // Arty
identify.call(you) // Yosef
```

Instead of using `call`, we can simply pass the object as a prop and no longer require any context to an object because we have the object itself.

```js
function identify(context) {
	return context.name.toUpperCase();
}

function speak(context) {
	var greeting = "Hello, I'm " + identify( context );
	console.log( greeting );
}

identify( you ); // YOSEF
speak( me ); // Hello, I'm ARTY
```

So why do we need call?
```js
function foo(num) {
	console.log( "foo: " + num );

	this.count++;
}

foo.count = 0;

var i;

for (i=0; i<10; i++) {
	if (i > 5) {
		foo( i );
	}
}
// foo: 6
// foo: 7
// foo: 8
// foo: 9

// how many times was `foo` called?
console.log( foo.count ); // 0 -- WTF?
```
In the example above you can see we are trying to count the number of times the function has being called. The problem is when we call `this.count++`, we DON'T actually refer to our function `foo` when we use `this`, but instead we are creating a new global variable `foo`.  

To solve this problem we can use `call` when invoking the function to properly bind the `this`  ref to the correct object or function 
```js
for (i=0; i<10; i++) {
	if (i > 5) {
		foo.call( foo, i );
	}
}
```
The first argument in `call` is the object we want to refer to. The additional arguments are the parameters we want to pass to the function.

### [what `this` is not](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch1.md#its-scope) ?

> Its Scope
The next most common misconception about the meaning of this is that it somehow refers to the function's scope. It's a tricky question, because in one sense there is some truth, but in the other sense, it's quite misguided.
To be clear, this does not, in any way, refer to a function's lexical scope. It is true that internally, scope is kind of like an object with properties for each of the available identifiers. But the scope "object" is not accessible to JavaScript code. It's an inner part of the Engine's implementation.

### what `this` is ?
>Having set aside various incorrect assumptions, let us now turn our attention to how the this mechanism really works.
We said earlier that this is not an author-time binding but a runtime binding. It is contextual based on the conditions of the function's invocation. this binding has nothing to do with where a function is declared, but has instead everything to do with the manner in which the function is called.
When a function is invoked, an activation record, otherwise known as an execution context, is created. This record contains information about where the function was called from (the call-stack), how the function was invoked, what parameters were passed, etc. One of the properties of this record is the this reference which will be used for the duration of that function's execution.
In the next chapter, we will learn to find a function's call-site to determine how its execution will bind this.

#### simple words: 
`this` reffer to the place where the function is getting called and not where it has being declared! (as the scope suggest)


#### Default Binding
```js
function foo() {
	console.log( this.a );
}

var a = 2;

foo(); // 2
```
Above we declared `a` in the globl scope, and called `foo` in the global scope - therefore the global scope is the `this` inside `foo` and will print `2`.


#### Implicit Binding
```js
function foo() {
	console.log( this.a );
}

var obj = {
	a: 2,
	foo: foo
};

obj.foo(); // 2
```
Whatever you choose to call this pattern, at the point that `foo()` is called, it's preceded by an object reference to `obj`. When there is a context object for a function reference, the implicit binding rule says that it's that object which should be used for the function call's this binding.
</br>
#### Implicitly Lost
One of the most common frustrations that this binding creates is when an implicitly bound function loses that binding, which usually means it falls back to the default binding, of either the `global` object or `undefined`, depending on strict mode.

```js
function foo() {
	console.log( this.a );
}

function doFoo(fn) {
	// `fn` is just another reference to `foo`

	fn(); // <-- call-site!
}

var obj = {
	a: 2,
	foo: foo
};

var a = "oops, global"; // `a` also property on global object

doFoo( obj.foo ); // "oops, global"
```
Parameter passing is just an implicit assignment, and since we're passing a function, it's an implicit reference assignment, so the end result is the same as the previous snippet.

What if the function you're passing your callback to is not your own, but built-in to the language? No difference, same outcome.

#### Explicit Binding
To use explicit binding we can use two methods provided to us by JS `call` and `apply` they are both takes as first arg the object/context you want to use as `this` and by doing so you can maka a proper function call with the binded `this` of your choice.
```js
function foo() {
	console.log( this.a );
}

var obj = {
	a: 2
};

foo.call( obj ); // 2
```


#### Hard Binding
As alternative - you can bind the context for a particular function **BEFORE** you call it by using `bind`. 

`bind` is another method you have in all functions in JS and it works similar to `apply` and `call` but also fix all bind lost issues by **hard binding** `this` before invoking the functions call 

```js
function foo() {
	console.log( this.a );
}

var obj = {
	a: 2
};

let fooBindedToObj = foo.bind(obj)

fooBindedToObj(); // 2
```
As you can see in the example above - we declare new variable `fooBindedToObj` ans set it with the value of `foo.bind(obj)` which mean , `foo` with the context of `obj` as `this`

> `bind(..)` returns a new function that is hard-coded to call the original function with the `this` context set as you specified.

###### API Call "Contexts"
Many libraries' functions, and indeed many new built-in functions in the JavaScript language and host environment, provide an optional parameter, usually called "context", which is designed as a work-around for you not having to use `bind(..)` to ensure your callback function uses a particular `this`.
```js
function foo(el) {
	console.log( el, this.id );
}

var obj = {
	id: "awesome"
};

// use `obj` as `this` for `foo(..)` calls
[1, 2, 3].forEach( foo, obj ); // 1 awesome  2 awesome  3 awesome
```
as the example aboce suggest , we pass second param to `foreach` with the context we want to bind the function to. We use it instead of using `bind`.
<br/>

#### `new` Binding
when creating new function constructors with the special keyword `new` you get the properties inside it auto binded to the new constructor that has been created. 
```js
function foo(a) {
	this.a = a;
}

var bar = new foo( 2 );
console.log( bar.a ); // 2
```
### Everything In Order
so now when we know about the 4 method we can bind `this` , which one is the strongest? 
```js
function foo() {
	console.log( this.a );
}

var obj1 = {
	a: 2,
	foo: foo
};

var obj2 = {
	a: 3,
	foo: foo
};

obj1.foo(); // 2
obj2.foo(); // 3

obj1.foo.call( obj2 ); // 3
obj2.foo.call( obj1 ); // 2
```
In the example above we can see that the explicit binding method using `call` is stronger then the implicit binding using the object name infront of the method. 
<br/>

### Determining `this`
Now, we can summarize the rules for determining `this` from a function call's call-site, in their order of precedence. Ask these questions in this order, and stop when the first rule applies.

Is the function called with `new` (**new binding**)? If so, `this` is the newly constructed object.

`var bar = new foo()`

Is the function called with `call` or `apply` (**explicit binding**), even hidden inside a `bind` hard binding? If so, `this` is the explicitly specified object.

`var bar = foo.call( obj2 )`

Is the function called with a context (**implicit binding**), otherwise known as an owning or containing object? If so, `this` is that context object.

`var bar = obj1.foo()`

Otherwise, default the `this` (**default binding**). If in strict mode, pick `undefined`, otherwise pick the `global` object.

`var bar = foo()`

That's it. That's all it takes to understand the rules of this binding for normal function calls. 

### Lexical `this`

As we learned in the previews book - in ES6 we have this new arrow function `() =>` which use `this` with totally diff aproach. What it does is **adopting** the `this` from the outter scope on its run time automatically and doesnt have it's own `this` as we know from regular functions. Which means - without any need of binding the function - the arrow function will check for the scope at it's run time - and use the `this` from it. 

```js
function foo() {
	setTimeout(() => {
		// `this` here is lexically adopted from `foo()`
		// as arrow functions doesn't have their own `this` context
		console.log( this.a );
	},100);
}

var obj = {
	a: 2
};

foo.call( obj ); // 2
```

Before ES6 we had another way to achive this pattern:
```js
function foo() {
	var self = this;

	// without using `self` the next block will have ITS own `this` context and 
	// wont have the right access to `a`
	setTimeout( function() {
		console.log( self.a );
	}, 100 );
}

var obj = {
	a: 2
};

foo.call( obj ); // 2
``` 

What we do is to assign `this` (whatever it will be) inside `foo` to a variable so later use it inside `setTimeout` so we wont lose its context by passing it as param. If we simply used `this` inside `setTimeout` we were getting NOT the `this` we want but the `this` of `setTimeout`.

Later we bind `this` using `call` to the object `obj` so when we use `self` inside `foo` the contex will be of `obj` and `a` will be `2`

Leter when invoking the function we simply do what we learned we need to do - bind the function with the context we need in this case `obj` what gives us the `this` context of `obj` inside a variable `self` which we later need to use inside `setTimeout`

<br/>

## notes

When you declare a `function` inside a `function` it has the `this` context of it's execution context (the `this` from the time you called the function (usually the `global` objet))! Even though you might think it should have the context of the function that it has being declared on. 

that's because **only** methods inhert the lexical context of `this` and even though the function has being declared inside another function , it's still a function and **not** a method - therefor it inhert the execution context as any function. (usually the `global` objet)

important to note - even that fucntions that has being declared inside another function have the `this` context of the execution context , it doesn't mean it can be called from the out of its scope - thats because of the Lexical scope and the fact it's not has being declared there.


#### example 
```js
function first() {
  let a = true;
  function second() { // <== regular function
    console.log(this.a); // <== <this> pointing to the global scope (this.a == undefined) 
  }
  second();
}

let obj = {
  a: true,
  consoleContext: function() { // <== a method

    console.log(this.a); // <== <this> pointing to <obj> scope (thia.a == true)
  }
};
```

#### Real life example 
lets say you have 