# Scope & closures

The scope is basically the "environment" where the declared variables live, and where they can be accessed.

### example

```js 
function foo() {
  let val = 10;
  console.log(val) // 10 
}
console.log(val) // RefError
```

In this example we prove the variable `val` only exists inside the scope of `foo` and can't be accessed in the outer scope (which in this example is the `global` scope).

The compiler will "bubble" through the scopes up to the `global` scope to search for the variable that has been called. If it doesnt find it, it return `RefError`.

# `let`

let is a new way to declare variables. What is special about it is the fact that `let` will only exist inside the **BLOCK** it has been declared in and **NOT** in the whole scope.

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
Here we use the old `var` keyword, and as you can see, it can be accessed in the whole **BLOCK** it has been declared on. We proved it when assigning a new value to `secret` inside of `if` even though it's not the same BLOCK - and yet the new value is accessible through the whole BLOCK. 

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
Here, as we can see, each of the `secret` variables has its own value through the BLOCK it has been declared in - one inside the `foo` BLOCK, the other in the `if` BLOCK.

### the problems `let` fixes

For one example: `let` fixes the weird behavior of JS show below:

```js
a = 2;
var a;
console.log( a );
```

What will print? One might think it will print `undefined` because the declaration of `a` comes after `a = 2`, 
but the compiler has its own rules (as you will learn in next chapter) and it actually reads this code like this:

```js
var a;
a = 2;
console.log( a );
```
So it will print `2`. It happens because the compiler first declares all variables in the code (no matter if they come after expressions) and only then it will assign values and execute all function calls

So how does `let` fix this confusing concept?

Simply - declarations made with `let` will not hoist to the entire scope of the block they appear in. Such declarations will not observably "exist" in the block until the declaration statement. 

means:
```js
a = 2;
let a;
console.log( a ); // a is not defined
```
Why? Because we tried to access `a` BEFORE it got declared and because `let` isn't being hoisted - it simply doesn't exist.

## Hoisting

First we need to declare one important role - in JS , the compiler first DECLARES all the variables and functions that need to be DECLARED and only then it will assign values and execute them. 

### example 
Taking the same example from before to demonstrate:
```js
a = 2;
var a;
console.log( a );
``` 
Here even though it seems like we assign `a = 2` first, the compiler actually read the `var a` part first because it's a declaration and `a = 2` is assignment.

Lets take another example: 

```js
console.log(a)
var a = 10;
```
The compiler actually breaks this statement down to what it knows best - declarations apart, assignments apart. It will read it like so:

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
`1` is printed instead of `2`! This snippet is interpreted by the compiler as:
```js
function foo() {
	console.log( 1 );
}

foo(); // 1

foo = function() {
	console.log( 2 );
};
```

It happens because, as the title suggests, `function` comes before variables. Therefore it will print `1` because `foo()` comes before `foo = function() {...}`, so it never called with the new function that has being **assigned** to is.

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
### what's going on? 

What we do in this snippet is declare a function `foo` with an inner function `bar` that, as we learned, has access through lexical scope to all of the `foo` inner scope. 

Then we take the ref to `bar` and `return` the `bar` function object.

Later we of course invoke `foo` and assign the value returned from it to `baz` and the value is? Correct - the function `bar`. 

At the end we invoke `baz` (while it's value is the func `bar`) and it's print `2`.
<br/>

**One might ask** - How do we have access to `var a = 2;` which causes it to print `2` if we invoked it **out** of its declared scope? Well - this is the magic of ***closures***.

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

### what's going on
In the example above, we declare var `fn` in the global scope.

Then we declare function `foo` with inner function `baz`, that as we learned, has an access to outer values through lexical scope.

Later we assign the `baz` func to the global var `fn` so now we have ref to `baz` out of the author declared scope!

At the end, we declare `bar`, which invokes `fn` which by the time `foo` will be invoked. It's value will be the ref to `baz` - this means that when we invoke `bar`, thanks to ***closure***, we can access to the scope of `foo` and succesfully access the value of `a`.

## closures in loops
```js
for (var i=1; i<=5; i++) {
	setTimeout( function timer(){
		console.log( i );
	}, i*1000 );
}
```

If we take this code above - one might think the result will be `1..2..3..4..5` - but it is not.

What actually happens is for each loop, a new function is being declared and invoked, but the access to `i` is through the global scope, and it has the value `6` by the time it is invoked. This means what actually will print is `6..6..6..6..6`, because 6 is the value of `i` in the global scope by the time it's invoked. 

**We can fix it using `let`**

As you recall - the `let` variable is **only** accessible to its own scope - which means that on each iteration of the loop , it creates a **new** `i` and you will have access to it through **closure** with its **new value**.

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

Let's break it down: 

The first thing we do is declaring `CoolModule` which is a function in the global scope. Inside it we declare a couple of variables and another couple of function. (All have access to the `CoolModule` scope, of course). The interesting part is right below the declaration - 
the `return` statement in the bottom of the function that returns an object with both functions that were declared, stored inside the object props. 

So what now? 

Now you can use pattern called *module reveal* which basically mean you call the module and store it's value inside another variable as you can see here `var foo = CoolModule();`. Now `foo` has the ref to the object returned from `CoolModule`, therefore it has the refs to both functions AND these functions have access to the **scope** of `CoolModule`!  

*NOTE:*
We don't return the variables inside the object like so:
```js
return {
    doSomething: doSomething,
    doAnother: doAnother,
    something:something,
    another:another
};
```
What does it mean? 
It means we **can't** access these variables outside of the scope of `CoolModule` - **only** the functions that has being returned can access and use these values because they share the same scope even if they get invoked outside of author contex (thanks to *closure*). 
**These variables are invisible to the rest of the app!**

The example above can create as many instance of the module as you like - if you prefer to have only one main instance of the model (this pattern can be called *singleton*) you can do this:

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
This example very similar to the first one only here we dont need to invoke `CoolModule` and assign it to a variable - we use *IIFE* to do so as the declaration of the module. It means at the time you declare the module - you assign it to a variable and can use it right away.

Another powerful pattern you can use is to name the object you return so that you can modify its method directly through its API:
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
As you can see we return `publicAPI` which is a named object that holds the functions we return as the module API. Now consider these snippets:
```js
    function change() {
        // modifying the public API
        publicAPI.identify = identify2;
    }
```
When you call `foo.change();` it changes the value of the `publicAPI.identify` and assigns to it new value `identify2`. Now when you call `foo.identify();` its values will be the function `identify2` and not `identify1`.

<br/>

### Lexical `this`

Consider these snippets:

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

We have couple of solutions to this problem. 

#### first `() =>` 

The arrow function is a new syntax of functions declaration presented to us with ES6. It has couple of features and one of them is the special treatment of `this`.

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
In the snippets above you can see we use the arrow function inside `setState` - before that we have seen the regular `function` keyword is cousing the `this` contex to be lost - but here the contex is correct because of `() =>` that use *lexical this* to help us with that issue. Basically what it does is taking the `this` contex of their immediate lexical enclosing scope which in this example is `cool` therefore it has the same `this` as to the rest of variables in its scope.

This approach has couple of minuses, such as - the arrow function is anonymous and at times you will have to use named functions and it can cause a problem. Another way to handle this issue is to use `bind` and properly assign the right `this` context to the code.

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

Here we simply use `bind` to have a contex to the `cool` scope therefore it works as expected.
