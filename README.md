# Modifying Objects

## Learning Goals

* Add an `Object` property using dot or bracket notation
* Modify a property using dot or bracket notation
* Update an `Object` nondestructively using the spread operator
* Using `Object.assign()`
* Update an `Object` nondestructively using `Object.assign()`
* Remove a property from an `Object`
* Identify the relationship between `Array`s and `Object`s

## Introduction

In the previous lesson, we learned the basics of creating `Object`s and
accessing their properties. In this lesson we'll learn how to modify and remove
properties, both destructively and nondestructively. Finally, we'll explore the
relationship between `Array`s and `Object`s.

As always, don't forget to follow along in [repl.it][].

## Add an `Object` Property Using Dot or Bracket Notation

We know how to initialize a variable by declaring it and assigning it a value
using the assignment operator:

```js
const city = "New York";
```

The process of creating a property inside an existing object is similar; we
specify the key and assign it a value:

```js
const circle = {}; // Create `circle` and set it to an empty Object

circle;
//=> {}

circle.radius = 5; // Create the key inside `circle` and set its value to 5 

circle;
//=> { radius: 5 }
```

We can do this using either dot or bracket notation, and we can use any
expression as the value:

```js
const circle = {};

circle.radius = 5;

circle['diameter'] = 10;

circle.circumference = circle.diameter * Math.PI;
//=> 31.41592653589793

circle['area'] = Math.PI * circle.radius ** 2;
//=> 78.53981633974483

circle;
//=> { radius: 5, diameter: 10, circumference: 31.41592653589793, area: 78.53981633974483 }
```

> **A Side Note**: Recall from the lesson on `Array`s that we can add, modify or delete elements even if we use `const` to initialize the `Array`. The same thing applies here: we can add, modify or delete properties, but we can't reassign the variable itself.

> **Top Tip**: Note that the process above gives us an alternative to typing out our `Object` using literal syntax: we can initialize an empty object and then use dot notation or bracket notation to create the properties programmatically. This approach is less error-prone than using literal syntax since JavaScript creates the correct `Object` syntax for us. Try it out in the REPL.

### Modify a Property Using Dot or Bracket Notation

We can update or overwrite existing properties simply by assigning a new value
to an existing key:

```js
const mondayMenu = {
  cheesePlate: {
    soft: 'Chèvre',
    semiSoft: 'Gruyère',
    hard: 'Manchego'
  },
  fries: 'Curly',
  salad: 'Cobb'
};

mondayMenu.fries = 'Sweet potato';

mondayMenu;
//=> { cheesePlate: { soft: "Chèvre", semiSoft: "Gruyère", hard: "Manchego" }, fries: "Sweet potato", salad: "Cobb" }
```

Note that modifying an `Object`'s properties in the way we did above is
_destructive_. This means that we're making changes directly to the original
`Object`.

Let's take a look at an example. We'll start by creating a function to
encapsulate this modification process:

```js
function destructivelyUpdateObject (obj, key, value) {
  obj[key] = value; //Why are we using bracket notation here?

  return obj;
}
```

Our function takes three arguments: the original menu `Object`, the `key`
identifying the property we want to update, and the `value` we want to change
its value to.

At our restaurant, we've finished serving for the day. It's time to update our
`mondayMenu` to the `tuesdayMenu`:

```js
const mondayMenu = {
  cheesePlate: {
    soft: 'Chèvre',
    semiSoft: 'Gruyère',
    hard: 'Manchego'
  },
  fries: 'Sweet potato',
  salad: 'Cobb'
};

const tuesdayMenu = destructivelyUpdateObject(mondayMenu, 'salad', 'Caesar');
//=> { cheesePlate: { soft: "Chèvre", semiSoft: "Gruyère", hard: "Manchego" }, fries: "Sweet potato", salad: "Caesar" }

tuesdayMenu.salad;
//=> "Caesar"
```

Looks like our `tuesdayMenu` came out perfectly. But what about `mondayMenu`?

```js
mondayMenu.salad;
//=> "Caesar"
```

Dang! We don't serve Caesar salad on Mondays. Instead of destructively updating
the original menu, is there a way to nondestructively merge the change(s) into a
new `Object`, leaving the original intact?

## Update an Object Nondestructively Using the Spread Operator

Let's create a new method; it will take the same three arguments as the previous
method:

```js
function nondestructivelyUpdateObject(obj, key, value) {
  // Code to return new, updated menu object
}
```

Recall from the lessons on `Array`s that we can use the _spread operator_ to
copy all the elements of an existing array into a new array. We can do the same
thing with `Object`s. Let's use the spread operator to copy all of the old menu
`Object`'s properties into a new `Object`:

```js
function nondestructivelyUpdateObject(obj, key, value) {
  const newObj = { ...obj };

  // Code to return new, updated menu object goes here
}
```

This will create a clone of the original object and save it into a new variable.
We can then update the newly-created `newObj` with the desired change and
return that updated menu, leaving the original menu `Object` unchanged:

```js
function nondestructivelyUpdateObject(obj, key, value) {
  const newObj = { ...obj };

  newObj[key] = value;

  return newObj;
}

const sundayMenu = nondestructivelyUpdateObject(tuesdayMenu, 'fries', 'Shoestring');

tuesdayMenu.fries;
//=> "Sweet potato"

sundayMenu.fries;
//=> "Shoestring"
```

To review, we are calling our `nondestructivelyUpdateObject()` function, passing
as our arguments the original menu (`tuesdayMenu`) and the key and value
representing the desired change. The function first makes a copy of
`tuesdayMenu`, then changes the value associated with the `fries` key to
`"Shoestring"`. Finally, it returns the updated menu, which is stored into the
variable `sundayMenu`.

While this works, it's quite a bit to write, and it's not very extensible. If we
want to modify more than a single property, we'll have to completely rewrite our
function! Luckily, JavaScript has a much better solution for us.

## Using `Object.assign()`

In the previous lesson, we took a look at a couple of JavaScript's static
`Object` methods, `Object.keys()` and `Object.values()`. A third method,
[`Object.assign()`][assign], will allow us to combine properties from multiple
`Object`s into a single `Object`. The method takes two or more `Object`s as its
arguments. The first argument passed to `Object.assign()` is the `Object` into
which all of the properties will be merged. Every additional argument is an
`Object` whose properties we want to merge into the first `Object`:

```js
Object.assign(initialObject, additionalObject, additionalObject, ...);
```

The return value of `Object.assign()` is the initial `Object` after all of the
additional `Object`s' properties have been merged in:

```js
Object.assign({ eggs: 3 }, { flour: '1 cup' });
//=> { eggs: 3, flour: "1 cup" }

Object.assign({ eggs: 3 }, { chocolateChips: '1 cup', flour: '2 cups' }, { flour: '1/2 cup' });
// { eggs: 3, chocolateChips: "1 cup", flour: "1/2 cup" }
```

Pay attention to the `flour` property in the above example. **If multiple
`Object`s have a property with the same key, the last key to be defined wins
out**. Essentially, the last call to `Object.assign()` in the above snippet is
wrapping all of the following assignments into a single line of code:

```js
const recipe = { eggs: 3 };

recipe.chocolateChips = '1 cup';

recipe.flour = '2 cups';

recipe.flour = '1/2 cup';
```

Try both approaches out in REPL and verify that they yield the same results.

Note that the `Object` being passed as the first argument — in this case,
recipe — is modified and returned at the end. So simply using
`Object.assign()` does not make our function nondestructive. So how do we solve
that problem? Well, the first argument we pass **does not need to be an existing
`Object`**!

## Update an `Object` Nondestructively Using `Object.assign()`

A common pattern for `Object.assign()` is to provide an empty `Object` as the
first argument. That way we're composing an entirely new `Object` instead of
modifying or overwriting the properties of an existing `Object`. This pattern
allows us to rewrite the above `destructivelyUpdateObject()` function in a
nondestructive way:

```js
function nondestructivelyUpdateObject(obj, key, value) {
  return Object.assign({}, obj, { [key]: value });
}
```

The code above takes the first argument (an empty `Object`), adds all the
properties in `obj` to it, then adds one final property consisting of the key
and value that represent the change we want to make. If that key doesn't already
exist in `obj`, it is added and its value is set to `value`. If it does already
exist, its old value is replaced by `value`. Note that all the arguments to
`Object.assign()` must be objects, so we're representing the key-value pair
as an `Object` using literal syntax here. Finally, the resulting new `Object` is
returned.

In other languages (like Ruby), this behavior is called "merging." You take an
original base `Object` (maybe with some typical "standard" attribute / value
pairs already set), and then you "merge" in additional Object(s).

Let's take a look at it using our `recipe` example:

```js
function nondestructivelyUpdateObject(obj, key, value) {
  return Object.assign({}, obj, { [key]: value });
}

const recipe = { eggs: 3 };

const newRecipe = nondestructivelyUpdateObject(recipe, 'chocolate', '1 cup');
//=> { eggs: 3, chocolate: "1 cup" }

newRecipe;
//=> { eggs: 3, chocolate: "1 cup" }

recipe;
//=> { eggs: 3 }
```

It's important that we merge everything into a new, empty `Object`. Otherwise,
we would be modifying the original `Object`.

So now let's write a new function for our restaurant that uses this technique.
We can also make one additional improvement: we can set up our function to take
an `Object` containing the changes we want to make as an argument, rather than a
single key and value. This enables us to make multiple changes with one call to
the function:

```js
function createNewMenu (oldMenu, menuChanges) {
  return Object.assign({}, oldMenu, menuChanges);
}

const tuesdayMenu = {
  cheesePlate: {
    soft: 'Chèvre',
    semiSoft: 'Gruyère',
    hard: 'Manchego'
  },
  fries: 'Sweet potato',
  salad: 'Caesar'
};

const newOfferings = {
  cheesePlate: {
    soft: 'Brie',
    semiSoft: 'Fontina',
    hard: 'Provolone'
  },
  salad: 'Southwestern'
};

const wednesdayMenu = createNewMenu(tuesdayMenu, newOfferings);

wednesdayMenu;
//=> { cheesePlate: { soft: "Brie", semiSoft: "Fontina", hard: "Provolone" }, fries: "Sweet potato", salad: "Southwestern" }

tuesdayMenu;
//=> { cheesePlate: { soft: "Chèvre", semiSoft: "Gruyère", hard: "Manchego" }, fries: "Sweet potato", salad: "Caesar" }
```

Note that the value for `fries` remains the same because our `newOfferings`
`Object` did not contain a change for that property. **However, this does not
work for _nested_ `Objects`.** For example, if `newOfferings` did not have an
updated value for `hard` cheese:

```js
const newOfferings = {
  cheesePlate: {
    soft: 'Brie',
    semiSoft: 'Fontina'
  },
  salad: 'Southwestern'
};
```

Our updated menu would not include that property and would look like this:

```js
wednesdayMenu;
//=> { cheesePlate: { soft: "Brie", semiSoft: "Fontina"}, fries: "Sweet potato", salad: "Southwestern" }
```

... instead of the desired outcome of this:

```js
wednesdayMenu;
//=> { cheesePlate: { soft: "Brie", semiSoft: "Fontina", hard: "Manchego"}, fries: "Sweet potato", salad: "Southwestern" }
```

You don't need to worry about this too much for now: just be aware that this
method does not create a _deep clone_. You will learn how to do that later in the
course.

Bon appétit!

> **NOTE:** Doing nondestructive updates (i.e. "creating new things and merging
> on top") is a really important pattern. It turns out that, in many places,
> nondestructive updates are more performant. The main reason for this is when
> you add something to an existing `Object`, the computer has to make sure that
> the `Object` has enough room to add what you're saying to add. If it doesn't,
> the computer needs to do cleanup work, find some more space, copy the old
> thing over, add the new thing, and then resume work. That "accounting"
> process is actually quite slow.

> Furthermore, in the cloud-based world of programming we're moving to more and
> more, we can't be sure that two computers will share the same memory. They
> might be servers separated by centimeters or kilometers. Using nondestructive
> updates ensures that our functions have "all they need" to run a function call
> independently, i.e., they have their own copy of the data they need and aren't
> sharing memory with other machines.

## Remove a Property from an Object

Uh oh, we ran out of Southwestern dressing, so we have to take the salad off the
menu. In JavaScript, that's as easy as:

```js
const wednesdayMenu = {
  cheesePlate: {
    soft: 'Brie',
    semiSoft: 'Fontina',
    hard: 'Provolone'
  },
  fries: 'Sweet potato',
  salad: 'Southwestern'
};

delete wednesdayMenu.salad;
//=> true

wednesdayMenu;
//=> { cheesePlate: { soft: "Brie", semiSoft: "Fontina", hard: "Provolone" }, fries: "Sweet potato" }
```

We pass the property that we'd like to remove to the [`delete`][delete] operator, and
JavaScript takes care of the rest. Poof! No more `salad` property on the
`wednesdayMenu` `Object`.

## Identify the Relationship Between Arrays and Objects

Think back to the early lesson on data types in JavaScript. We listed off seven
types into which all data falls: numbers, strings, booleans, symbols, `Object`s,
`null`, and `undefined`. Notice anything missing? Arrays!

Why isn't an `Array` a fundamental data type in JavaScript? The answer is that
**it's actually a special type of `Object`**. Yes, that's right: ***`Array`s are
`Object`s***. To underscore this point, check out what the `typeof` operator
returns when we use it on an `Array`:

```js
typeof [];
//=> "object"
```

We can set properties on an `Array` just like a regular `Object`:

```js
const myArray = [];

myArray.summary = 'Empty array!';

myArray;
//=> [summary: "Empty array!"]
```

And we can modify and access those properties, too:

```js
myArray['summary'] = 'This array is totally empty.';

myArray;
//=> [summary: "This array is totally empty."]

myArray.summary;
//=> "This array is totally empty."
```

In fact, _everything_ we just learned how to do to `Object`s can also be done to
`Array`s because `Array`s **are** `Object`s. Just special ones. To see the special
stuff, let's `.push()` some values into our `Array`:

```js
myArray.push(2, 3, 5, 7);
//=> 4

myArray;
//=> [2, 3, 5, 7, summary: "This array is totally empty."]
```

Cool, looks like everything's still in there. What's your guess about the
`Array`'s `.length`?

```js
myArray.length;
//=> 4
```

Huh, that's interesting. Surely our `summary` must be the first element in the
`Array`, no? After all, we did add it before we `.push()`ed all those values in.

```js
myArray[0];
//=> 2
```

Hm, then maybe it's the last element?

```js
myArray[myArray.length - 1];
//=> 7
```

What the heck? Where is it?

You see, one of the 'special' features of an `Array` is that **its `Array`-style
elements are stored separately from its `Object`-style properties**. The `.length`
property of an `Array` describes how many items exist in its special list of
elements. Its `Object`-style properties are not included in that calculation.

This brings up an interesting question: if we add a new property to an `Array`
that has a key of `0`, how does the JavaScript engine know whether it should be
an `Object`-style property or an `Array`-style element?

```js
const myArray = [];

myArray[0] = 'Will this be an `Object` property or an `Array` element?';
//=> "Will this be an `Object` property or an `Array` element?"

// Moment of truth...
myArray.length;
//=> 1

myArray;
//=> ["Will this be an `Object` property or an `Array` element?"]
```

So JavaScript used that assignment operation to add a new `Array`-style element.
What happens if we enclose the integer in quotation marks, turning it into a
string?

```js
myArray['0'] = 'What about this one?';
//=> "What about this one?"

myArray.length;
//=> 1

myArray;
//=> ["What about this one?"]
```

This is hitting on a fundamental truth: **all keys in `Object`s and all indexes in
`Array`s are actually strings**. In `myArray[0]` we're using the integer `0`, but
under the hood the JavaScript engine automatically converts that to the string
`"0"`. When we access elements or properties of an `Array`, the engine routes all
integers and integers masquerading as strings (e.g., `'14'`, `"953"`, etc.) to
the `Array`'s special list of elements, and it treats everything else as a simple
`Object` property. For example:

```js
const myArray = [2, 3, 5, 7];

myArray['1'] = 'Hi';
//=> "Hi"

myArray;
//=> [2, "Hi", 5, 7]

myArray['01'] = 'Ho';
//=> "Ho"

myArray;
//=> [2, "Hi", 5, 7, 01: "Ho"]

myArray[01];
//=> "Hi"

myArray['01'];
//=> "Ho"
```

After adding our weird `'01'` property, the `.length` property still returns
`4`:

```js
myArray.length;
//=> 4
```

So it would stand to reason that `Object.keys()` would only return `'01'`,
right?

```js
Object.keys(myArray);
//=> ["0", "1", "2", "3", "01"]
```

Unfortunately not. The reason why `Array`s have this behavior would take us deep
inside the JavaScript source code, and it's frankly not that important. Just
remember these simple guidelines, and you'll be just fine:

* **For accessing elements in an `Array`, always use integers**.
* **Be wary of setting `Object`-style properties on an `Array`**. There's rarely any reason to, and it's usually more trouble than it's worth.
* **Remember that all `Object` keys, including `Array` indexes, are strings**. This will really come into play when we learn how to iterate over `Object`s, so keep it in the back of your mind.

## Conclusion

In this and the previous lesson, we dug deep into `Object`s in JavaScript. We
identified what an `Object` is and how to access values stored in it. We also
covered how to add and remove properties and how to use some of JavaScript's
convenience methods (`Object.keys()`, `Object.values()`, and `Object.assign()`).
We also explored the relationship between `Object`s and `Array`s.

## Resources

* MDN
  * [Object basics][Basics]
  * [`Object.assign()`][assign]
  * [`delete`][delete]

[assign]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign
[delete]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/delete
[Basics]: https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Basics
[repl.it]: https://repl.it/languages/javascript
