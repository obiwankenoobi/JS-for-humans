# Scope & closures
the scope is basically the "enviorment" the declared variables are live - and where they can be accessed from 
### example

```js 
function foo() {
  let val = 10;
  console.log(val) // 10 
}
console.log(val) // RefError
```
in this example we prove the variable `val` is onlt exist inside the scope of `foo` and can't be accessed in the outer scope - in this example is the `global` scope.

The compiler will "bubble" through the scopes up to the `global` scope to search for the variable that has been called and if it doesnt find it - it return `RefError` 

# `let`

let is a new way to declare variables, what special about it is the fact `let` will only exist inside the **BLOCK** it has been declared on and **NOT** in the whole scope.

### example
```js 
function foo(val) {
  var secret = "secret";
  if (val) {
    secret = "not a secret";
    console.log(secret); // not a secret
  }
  console.log(secret); // // not a secret
}

```
here we use the old `var` keyword and as you can see it can be access in the whole **BLOCK** it has been declared on. We proved it when assigning a new value to `secret` inside of `if` even though its not the same BLOCK - and yet the new value accessable through the whole BLOCK. 

```js 
function fee(val) {
  let secret = "secret";
  if (val) {
    let secret = "not a secret";
    console.log(secret); // not a secret
  }
  console.log(secret); // secret
}

```
here as we can see each of the `secret` variable has it's own value through the BLOCK it has been declared on - one inside the `foo` BLOCK , while the other have the `if` BLOCK and their are exist only there.

### the problems `let` fixing

for one example - `let` fix the weird behavior of JS as the example belove suggest:

```js
a = 2;
var a;
console.log( a );
```

whatwould print? one might think it will print `undefined` because the declaration of `a` comes after `a = 2` so you might think `a` is not equel `2` but in fact the compiler have its own rules as you learn in next chapter and it actually read this code like this:

```js
var a;
a = 2;
console.log( a );
```
so it will print `2` it happens because the compiler first declare all variables in the code (no matter if they come after expressions) and only then it will assign values and execute all function calls

so how `let` fix this confusing concept?

simply - declarations made with `let` will not hoist to the entire scope of the block they appear in. Such declarations will not observably "exist" in the block until the declaration statement. 

means:
```js
a = 2;
let a;
console.log( a ); // a is not defined
```
why? because we trying to access `a` BEFORE it getting declared and because `let` isn't being hoist - it's simply doesnt exist.

## Hoisting

first we need to declare one important role - in JS , the compiler first DECLARE all the variables and functions that need to be DECLARED and only then it will assign values and execute them. 
### example 
```js
a = 2;
var a;
console.log( a );
``` 
We took the example from before to demonstrate. Here even though it seems like we assign `a = 2` first, the compiler actually read the `var a` part first  because its a declaration and `a = 2` is assignment.

Lets take another example: 

```js
console.log(a)
var a = 10;
```
the compiler actually brake down this statment to what he know best - declarations apart , assignments apart. And it will read it like so:

```js
var a;
console.log(a);
a = 10;
```
the assignment `a = 10;`, is **left** in place for the execution phase while the declaration `var a;` hoised to the top. 

<br/>

**Function declarations are hoisted, But function expressions are not.**

```js
foo(); // not ReferenceError, but TypeError!

var foo = function bar() {
	// ...
};
```
The variable identifier foo is hoisted and attached to the enclosing scope (global) of this program, so foo() doesn't fail as a ReferenceError. But foo has no value yet (as it would if it had been a true function declaration instead of expression). So, foo() is attempting to invoke the undefined value, which is a TypeError illegal operation.

### functions first
```js
foo(); // 1

var foo;

function foo() {
	console.log( 1 );
}

foo = function() {
	console.log( 2 );
};
```
`1` is printed instead of `2`! This snippet is interpreted by the Engine as:
```js
function foo() {
	console.log( 1 );
}

foo(); // 1

foo = function() {
	console.log( 2 );
};
```

it happens because as the title suggests `function` comes before variables. Therefor it will print `1` because `foo()` comes before `foo = function() {...}` so it never called with the new function that has being **assigned** to is

## Scope Closure
> Closure is when a function is able to remember and access its lexical scope even when that function is executing outside its lexical scope.

```js
function foo() {
	var a = 2;

	function bar() {
		console.log( a );
	}

	return bar;
}

var baz = foo();

baz(); // 2
```
### whats going on? 
What we do in this snippets is declaring a function `foo` with inner function `bar` that as we learned has access through lexical scope - to all of the `foo` inner scope. 


Then we taking the ref to `bar` and `return` the `bar` function object


Leter we ofcourse invoking `foo` and assigning the value returned from it to `baz` and the value is? correct the function - `bar`. 


At the end we invoke `baz` (while it's value is the func `bar`) and it's print `2`.
<br/>

**One might ask** - How do we have access to `var a = 2;` which couse it to print `2` if we invoked it **out** of its declared scope? well - this is the magic of ***clousers*** . 

> `bar()` still has a reference to that scope, and that reference is called closure.


### more example
```js
var fn;

function foo() {
	var a = 2;

	function baz() {
		console.log( a );
	}
    // assign `baz` to <fn> (out of scope)
	fn = baz; 
}

function bar() {
	fn(); // look ma, I saw closure!
}

foo();

bar(); // 2
```

### whats going on
In the example above what we do is declaring var `fn` in the global scope

Then we declaring function `foo` with inner function `baz` that as we learned has an access to outer values through laxical scope.

Later we assign the `baz` func to the global var `fn` so now we have ref to `baz` out of the author declared scope! 

at The end we daclare `bar` which invoke `fn` which by the time `foo` will be invoked - it's value will be the ref to `baz` means - when we invoke `bar` thanks to ***clouser*** we can access to the scope of `foo` and succesfully access the value of `a`

## clousers in loops
```js
for (var i=1; i<=5; i++) {
	setTimeout( function timer(){
		console.log( i );
	}, i*1000 );
}
```

If we take this code above - one might think the result will be `1..2..3..4..5` but not.

What actually happens is for each loop new function is being declared and invoked but the access to `i` is thorugh the global scope and has the value `6` by the time it invoked. means what actually being print is `6..6..6..6..6` as the value `i` of the global scope - by the time its invoked suggest. 

**We can fix it using `let`**

As you recall - `let` variable is **only** accessable to its own scope - which means that on each irritation of the loop , it creates **new** `i` and you will have access to it thought **clouser** with its **new value**

```js
for (let i=1; i<=5; i++) {
	setTimeout( function timer(){
		console.log( i );
	}, i*1000 );
}
```
> There's a special behavior defined for let declarations used in the head of a for-loop. This behavior says that the variable will be declared not just once for the loop, but each iteration. And, it will, helpfully, be initialized at each subsequent iteration with the value from the end of the previous iteration.


## Closurs in Modules
consider this snippets: 
```js
function CoolModule() {
	var something = "cool";
	var another = [1, 2, 3];

	function doSomething() {
		console.log( something );
	}

	function doAnother() {
		console.log( another.join("!"));
	}

	return {
		doSomething: doSomething,
		doAnother: doAnother
	};
}

var foo = CoolModule();

foo.doSomething(); // cool
foo.doAnother(); // 1 ! 2 ! 3
```
### This pattern we see above called *module* 

lets brake it down: 
The first thing we doing is declaring `CoolModule` which is a function in the global scope. Inside it we declare a couple of variables and another couple of function. (all have access to the `CoolModule` scope of course) the interesting part is right below the declaration - 
the `return` statement in the bottom of the function that returning an object with both functions that has being declared, stored inside the object props. 

So what now? 
Now you can use pattern called *module reveal* which basically mean you call the module and store it's value inside another variable as you can see here `var foo = CoolModule();`. Now `foo` has the ref to the object that has being returned from `CoolModule` therefore , it has the refs to both functions AND these functions has access to the **scope** of `CoolModule`!  

*NOTE:*
We dont return the variables inside the object like so:
```js
return {
    doSomething: doSomething,
    doAnother: doAnother,
    something:something,
    another:another
};
```
what does it mean? 
It means we **cant** access these variables outside of the scope of `CoolModule` - **only** the functions that has being returned can access and use these values because they share the same scope even if they get invoked outside of author contex (thanks to *closur*). 
**These variables are invisible to the rest of the app!**

The example above can create as many instance of the module as you like - if you preffer to have only one main instance of the model (this pattern can be called *singleton*) you can do this:

```js
var foo = (function CoolModule() {
	var something = "cool";
	var another = [1, 2, 3];

	function doSomething() {
		console.log( something );
	}

	function doAnother() {
		console.log( another.join( " ! " ) );
	}

	return {
		doSomething: doSomething,
		doAnother: doAnother
	};
})();

foo.doSomething(); // cool
foo.doAnother(); // 1 ! 2 ! 3
```
This example very similar to the first one only here we dont need to invoke `CoolModule` and assign it to a variable - we use *IIFE* to do so as the declaration of the module. It means at the time you declare the module - you assign it to a variable and can use it right aeway.


Another powerfull patter you can use is to name the object you return so you can modify it's method directly through it's API
```js
var foo = (function CoolModule(id) {
	function change() {
		// modifying the public API
		publicAPI.identify = identify2;
	}

	function identify1() {
		console.log( id );
	}

	function identify2() {
		console.log( id.toUpperCase() );
	}

	var publicAPI = {
		change: change,
		identify: identify1
	};

	return publicAPI;
})( "foo module" );

foo.identify(); // foo module
foo.change();
foo.identify(); // FOO MODULE
```
As you can see we return `publicAPI` which is an named object what hold the functions we returning as the module API. Now consider this snippets:
```js
    function change() {
        // modifying the public API
        publicAPI.identify = identify2;
    }
```
When you call `foo.change();` what it does is - changing the value of the `publicAPI.identify` and assign to it new value `identify2`. Now when you call `foo.identify();` its values will be the function `identify2` and not `identify1`.

<br/>

### Lexical `this`

consider the next snippets:
```js
var obj = {
	id: "awesome",
	cool: function coolFn() {
		console.log( this.id );
	}
};

var id = "not awesome";

obj.cool(); //awesome

setTimeout( obj.cool, 100 ); //not awesome
```
As you can see - by calling `obj.cool` inside `setTimeout` it lost it's `this` binding and instead of getting the scope of `obj` it getting the `global` scope which has diff value for `id`.

We have couple of solutions ti this problem. 

#### first `() =>` 
the arrow function is a new syntax of functions declaration presented to us with ES6. It has couple of features and one of them is the special treatment to `this` 
```js
var obj = {
	count: 0,
	cool: function coolFn() {
		if (this.count < 1) {
			setTimeout( () => {
				this.count++;
				console.log( "awesome?" );
			}, 100 );
		}
	}
};

obj.cool(); // awesome?
```
In the snippets above you can see we use the errow function inside `setState` - before that we have seen the regular `function` keyword is cousing the `this` contex to be lost - but here the contex is correct because of `() =>` that use *lexical this* to help us with thats issue. Basically what it does is taking the `this` contex of their immediate lexical enclosing scope which in this example is `cool` therfor it has the same `this` as to the rest of variables in it's scope.

This approach has couple of minuses such as - arrow function is annonymouse and at times you will have to use named functions and it can couse a problem. Another way to handle this issue is to use `bind` and propperlly assign the right `this` context to the code
```js
var obj = {
	count: 0,
	cool: function coolFn() {
		if (this.count < 1) {
			setTimeout( function timer(){
				this.count++; 
				console.log( "more awesome" );
			}.bind( this ), 100 );
		}
	}
};

obj.cool(); // more awesome
```

here we simply use `bind` to have a contex to the `cool` scope therefore it works as expected.