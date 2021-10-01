# Unit 3 Activity 2: Closures
## 1. ¿What is a closure? 

A **closure** a function within a function that is enclosed or bundled with its outer environment also called **lexical scope**.

---

## 2. Give me an example of closure. 
```js
function sayHello() {
    var greeting = "Hello";
    function dsiplayGreeting (){
        console.log(greeting);
    }
    return displayGreeting;
}

const myFunction = sayHello();
myFunction();
```
In the example above: `displayGreeting` is a closure returned by `sayHello` and `greeting` is a variable in its lexical environment, thus when saving the returned value of `sayHello` in variable `myFunction`, we can access the variable `greeting`.

---

## 3. What is '()()' in code?
Is a way to immediately call for a function nested within another function, the number of parenthesis pairs depends of how deep is the function we are willing to execute. For example:
```js
function counter() {
  var countStart = 0;
  return function count10() {
    for (let i = 0; i < 10; i++) {
      countStart++;
    }
    console.log(countStart);
  };
}

counter()();
```
In the example above the statement `counter()()` means that after the `counter` function ends it will immediately call the second innermost function within, which is the `count10` function if it were another function within `count10` in order to access it we could use triple parenthesis as `counter()()()`.

---

## 4. Move the variable after the closure (the function inside the function) and explain what happens.
If we move the variable after the closure it will still work as long as the variable is located before the `return` statement and not after otherwise we will get `NaN` or `undefined` depending of the function. For example:
#### This will work ✔️
```js
function counter() {
  function count10() {
    for (let i = 0; i < 10; i++) {
      countStart++;
    }
    console.log(countStart);
  }
  var countStart = 0; //Variable before the 'return' statement
  return count10;
}

counter()(); // Output will be 10
```
#### This won't work ❌
```js
function counter() {
  
  return function count10() {
    for (let i = 0; i < 10; i++) {
      countStart++;
    }
    console.log(countStart);
  }
  var countStart = 0; //Variable after the 'return' statement
}

counter()(); // Output will be NaN (Not a Number)
```
---

## 5. Change var for let and explain why the logic is not affected 
For the case where the variable is located **before** the `return` statement it still going to work because hoisting is not involved as the function call is made later. However, for the case where the variable is **after** the `return` statement, if we replace `var` for `let` we won't get `undefined` or `NaN` (side effect of hoisting), instead we will get a `Reference error` because variables declared with `let` cannot be hoisted as variables declared with `var` do.

---

## 6. Scope chain, an example of it, how many closures can we nest 
When a variable is used in JavaScript, the JavaScript engine will try to find the variable’s value in the current scope. If it could not find the variable, it will look into the outer scope and will continue to do so until it finds the variable or reaches global scope. Once on the global scope if the variable can't be found it will declare it implicitly on the global scope or return an error if `strict mode` is being used. As for how many closures can we nest the answer is **infinite**, we can nest as many closures as we want. For example:

```js
function foo() {
  let greet = "Hello, ";
  return function baz() {
    greet += " how";
    return function bar() {
      greet += " are";
      return function daz() {
        greet += " you?";
        return greet;
      };
    };
  };
}
console.log(foo()()()()); // Output will be "Hello, how are you?"
```

---
  
## 7. There are conflicts between the closure and the global scope?
Under normal circumstances there shouldn't be any conflict, unless you declare a variable with `var` and put it after the `return` statement (as mentioned in questions 4 and 6), in that case it will happen what I mentioned in the previous question that the the JavaScript engine will look up for that variable until reaching the global scope and then 'create it'. This side effect is known as 'Global Variable Leaking'. For example:

```js
//global scope
function foo() {
// foo scope
  return function bar() {
  //bar scope
    greet = "Hello";
  };
}
foo()();
console.log(greet);
```
In the above example the variable `greet` is inside the `bar` function scope and technically it shouldn't be accessible for the `console.log` in the global scope. Nevertheless since `greet` is not defined, and is only being assigned the value 'Hello' within the `bar` scope, the JavaScript engine will look for its declaration in `foo` and since is neither in that scope then the JS engine will implicitly create the declaration of `greet` in the global scope, thus being accessible for `console.log`.

---

## 8. Advantages of closures.
The most important advantages of closures are:
- Can use the same name for different variables through different scopes.
- Data hidding and privacy.
- Encapsulation.
- Function factories and HOCs (High Order Components).
- Functions are first-class citizens.

---

## 9. ¿What is data hiding and encapsulation?
**Data Hiding**: Is concentrates on restricting the access to data from outside of the program defining it as 'private' in order to enforce security.
**Encapsulation**: Is one of the characteristics of OOP it focuses on wrapping the implementation code with the data or state it manipulates, contrary to data hiding, in encapsulation there may be some data that can be public.

---

## 10. Give me an example of privacy with closures. 
```js
const counter = (function () {
  let privateCounter = 0;
  
  function changeBy(val) {
    privateCounter += val;
  }
  
  return {
      increment: function() {
        changeBy(1);
        return privateCounter;
    },
  }
})();

console.log(counter.increment()); // 1
console.log(counter.increment()); // 2
```
In above example `incremet()`, is a public function since it is included in `return`, whereas `changeBy()` becomes a private function because it is not returned and is only used internally by `increment()`.

---

## 11. What happens if you create two counters with the same closure?
It won't happen anything because everytime the function is called and the result is stored in a variable the closures are created with their own new lexical scope so they don't collide with each other. Example:
```js
function counter() {
  let privateCounter = 0;
  function changeBy(val) {
    privateCounter += val;
  }
  
  return {
    increment: function () {
      changeBy(1);
      return privateCounter;
    },
  };
}

const counter1 = counter();
const counter2 = counter();

console.log(counter1.increment()); // 1 
console.log(counter1.increment()); // 2 one lexical scope

console.log(counter2.increment()); // 1
console.log(counter2.increment()); // 2 another lexical scope
```
In the example above although `counter1` and `counter2` are created using the same function `counter()` each one of them have their own lexical enviroment and that's why despite calling for their `increment` method twice we only get two `2` as output instead of one `4`.

---

## 12. How can we add more functions as a decrement counter? Give an example of it. 
We can return an object containing functions defined as **methods** which then we can call with dot notation. For example:
```js
function counter() {
  let privateCounter = 0;

  function changeBy(val) {
    privateCounter += val;
  }
  
  return {
    increment: function () {
      changeBy(1);
    },
    decrement: function () {
      changeBy(-1);
    },
    value: function () {
      return privateCounter;
    },
  };
}

const counter1 = counter();

console.log(counter1.value()); // 0
counter1.increment();
counter1.increment();
console.log(counter1.value()); // 2
counter1.decrement();
console.log(counter1.value()); //1
```

---

## 13. What are the disadvantages of closures?
Closures consume a lot of memory because we're creating a lot of functions and variables, therefore slowing down the performance of the application.

---
