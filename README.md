# Objects

## Overview
In this lesson, we'll introduce, define, and work with objects in JavaScript.

## Objectives
+ Create JavaScript objects.
+ Access a value stored in an object.
+ Add a property to an object.
+ Work with convenience methods like `Object.assign()` and `Object.keys()`.
+ Use the `delete` operator to remove a property from an object.

## Introduction
Arrays are awesome for representing simple, ordered data sets, but they're generally not great at modeling any sort of complex structure. For example, let's think about how we could represent the address of Flatbook HQ in JavaScript.

Addresses are made up of words and numbers, so at first it might make sense to store the address as a string:
```js
const address = '11 Broadway, 2nd Floor, New York, NY 10004';
```

That looks decent enough, but what happens if the company moves to a different floor in the same building? We just need to modify one piece of the address, but with a string we'd have to involve some pretty complicated find-and-replace pattern matching or simply replace the entire thing. Instead, let's throw the different pieces of the address into an array:
```js
const address = ['11 Broadway', '2nd Floor', 'New York', 'NY', 10004];
```

Now, we can just grab the small piece that we want to update and leave the rest as-is:
```js
address[1] = '3rd Floor';

address;
// => ["11 Broadway", "3rd Floor", "New York", "NY", 10004]
```

This seems like a better solution, but it still has its drawbacks. Namely, `address[1]` is a **terrible** way to refer to the second line of an address. What if there is no second line, e.g., `['11 Broadway', 'New York', 'NY', 10004]`? Then `address[1]` will contain the city name instead of the floor number.

We could standardize it, putting an empty string in `address[1]` if there's no second line in the address, but it's still poorly named. `address[1]` offers very little insight into what data we should expect to find in there. It's a part of an address, sure, but which part?

To get around this, we could store the individual pieces of the address in separate, appropriately-named variables:
```js
const street1 = '11 Broadway';
const street2 = '2nd Floor';
const city = 'New York';
const state = 'NY';
const zipCode = 10004;
```

That's solved one issue but reintroduced the same problem we tackled in the lesson on arrays: storing pieces of related data in a bunch of unrelated variables is not a great idea! If only there were a best-of-both-worlds solution — a way to store all of our address information in a single data structure while also maintaining a descriptive naming scheme...

Well, we sort of ruined the surprise when we introduced all of the JavaScript data types. Oh, and the title of this lesson might provide a small hint...

Oh well. The amazing data structure we're after is the ***object***.

## What is an object?
We briefly touched on the syntax for an object back in the data types lesson, and let's quickly revisit that description here:
>JavaScript objects are a collection of properties bounded by curly braces (`{ }`), similar to a hash in Ruby. The properties can point to values of any data type — even other objects.

We can have empty objects:
```js
{}
```

Or objects with a single _key-value_ pair:
```js
{ key: value }
```

When we have to represent multiple key-value pairs in the same object (which is most of the time), we use commas to separate them out:
```js
{
  key1: value1,
  key2: value2
}
```

For a real example, let's see our address as an object:
```js
const address = {
  street1: '11 Broadway',
  street2: '2nd Floor',
  city: 'New York',
  state: 'NY',
  zipCode: 10004
};
```

The real data in an object is stored in the _value_ half of the key-value pairings. The _key_ is what lets us access that value. In the same way we use _identifiers_ to name variables and functions, inside an object we assign each value a key. We can then refer to that key and the JavaScript engine knows exactly which value we're trying to access.

## Accessing values in an object
There are two ways to access values in an object, one of which we already learned about in the arrays lesson.

### Dot notation
With _dot notation_, we use the _member access operator_ (a single period) to access values in an object. For example, we can grab the individual pieces of our address, above, as follows:
```js
address.street1;
// => "11 Broadway"

address.street2;
// => "2nd Floor"

address.city;
// => "New York"

address.state;
// => "NY"

address.zipCode;
// => 10004
```

Dot notation is fantastic for readability, as we can just reference the bare key name (e.g., `street1` or `zipCode`). Because of this simple syntax, it should be your go-to strategy for accessing the properties of an object.

***NOTE***: Most people just call it _dot notation_ or the _dot operator_, so don't worry too much about remembering the term _member access operator_.

#### Accessing nonexistent properties
If we try to access the `country` property of our `address` object, what will happen?

```js
address.country;
// => undefined
```

It returns `undefined` because there is no matching key on the object. JavaScript is too nice to throw an error, so it lets us down gently. Keep this in mind, though: if you're seeing `undefined` when trying to access an object's properties, it's a good indicator to recheck which properties exist on the object (along with your spelling and capitalization)!

### Bracket notation
With _bracket notation_, we use the _computed member access operator_, which, recall from the lesson on arrays, is a pair of square brackets (`[]`). To access the same properties as above, we need to represent them as strings inside the operator:
```js
address['street1'];
// => "11 Broadway"

address['street2'];
// => "2nd Floor"

address['city'];
// => "New York"

address['state'];
// => "NY"

address['zipCode'];
// => 10004
```

Bracket notation is a bit harder to read than dot notation, so we always default to the latter. However, there are two main situations in which to use bracket notation.

#### With nonstandard keys
If (for whatever reason) you need to use a nonstandard string as the key in an object, you'll only be able to access the properties with bracket notation. For example, this is a valid object:
```js
const wildKeys = {
  'Cash rules everything around me.': 'Wu',
  'C.R.E.A.M.': 'Tang',
  'Get the money.': 'For',
  "$ $ bill, y'all!": 'Ever'
};
```

It's impossible to access those properties with dot notation:
```js
wildKeys.'Cash rules everything around me.';
// ERROR: Uncaught SyntaxError: Unexpected string
```

But bracket notation works just fine:
```js
wildKeys["$ $ bill, y'all!"];
// => "Ever"
```

In order to access a property via dot notation, **the key must follow the same naming rules applied to variables and functions**. It's also important to note that under the hood **all keys are strings**. Don't waste too much energy worrying whether a key is accessible via dot notation; simply follow these rules when naming your keys, and everything will work:
- camelCaseEverything.
- Start the key with a lowercase letter.
- No spaces or punctuation.

If you follow those three rules, you'll be able to access all of an object's properties via bracket notation **or** dot notation.

***Top Tip***: Always name your object's keys according to the above three rules. Keeping everything standardized is good, and being able to access properties via dot notation is much more readable than having to always default to bracket notation.

#### Accessing properties dynamically
The other situation in which bracket notation is required is if we want to dynamically access properties. From the lesson on arrays, remember why we call it the _computed member access operator_: we can place any expression inside the brackets and the JavaScript engine will _compute_ its value to figure out which property to access. For example, we can access the `zipCode` property from our `address` object like so:
```js
address['zip' + 'Code'];
// => 10004
```

Pretty neat, but the real strength of bracket notation is its ability to compute the value of variables on the fly. For example:
```js
const meals = {
  breakfast: 'Oatmeal',
  lunch: 'Caesar salad',
  dinner: 'Chimichangas'
};

let mealName = 'lunch';

meals[mealName];
// => "Caesar salad"
```

If we try to use the `mealName` variable with dot notation, it doesn't work:
```js
mealName = 'dinner';
// => "dinner"

meals.mealName;
// => undefined
```

With dot notation, the JavaScript engine doesn't treat `mealName` as a variable — it checks whether a property exists with a key of `mealName`, only finds properties named `breakfast`, `lunch`, and `dinner`, and so returns `undefined`. Essentially, dot notation is for when you know the exact name of the property in advance, and bracket notation is for when you need to compute it at runtime.

The need for bracket notation doesn't stop at dynamically setting properties on an already-created object. Since the ES2015 update to JavaScript, we can also use bracket notation to dynamically set properties _during the creation of a new object_. For example:
```js
const morningMeal = 'breakfast';
const middayMeal = 'lunch';
const eveningMeal = 'dinner';

const meals = {
  [morningMeal]: 'French toast',
  [middayMeal]: 'Personal pizza',
  [eveningMeal]: 'Fish and chips'
};

meals;
// => { breakfast: "French toast", lunch: "Personal pizza", dinner: "Fish and chips" }
```

Let's try doing the same thing without the square brackets:
```js
const morningMeal = 'breakfast';
const middayMeal = 'lunch';
const eveningMeal = 'dinner';

const meals = {
  morningMeal: 'French toast',
  middayMeal: 'Personal pizza',
  eveningMeal: 'Fish and chips'
};

meals;
// => { morningMeal: "French toast", middayMeal: "Personal pizza", eveningMeal: "Fish and chips" }
```

Without the square brackets, the JavaScript engine treated each key as a literal identifier instead of a variable. Bracket notation — the _computed member access operator_ once again shows its powers of computation!

Bracket notation will really come in handy when we start iterating over objects and programmatically accessing and assigning properties.

## Adding properties to an object
To add properties to an object, we can use either dot notation or bracket notation:
```js
const circle = {};

circle.radius = 5;

circle['diameter'] = 10;

circle.circumference = circle.diameter * Math.PI;
// => 31.41592653589793

circle['area'] = Math.PI * circle.radius ** 2;
// => 78.53981633974483

circle;
// => { area: 78.53981633974483, circumference: 31.41592653589793, radius: 5 }
```

Multiple properties can have the same value...
```js
const meals = {
  breakfast: 'Avocado toast',
  lunch: 'Avocado toast',
  dinner: 'Avocado toast'
};

meals.breakfast;
// => "Avocado toast"

meals.dinner;
// => "Avocado toast"
```

But keys must be unique. If the same key is used for multiple properties, only the final value will be retained — the rest will be overwritten:
```js
const meals = {
  breakfast: 'Avocado toast',
  breakfast: 'Oatmeal',
  breakfast: 'Scrambled eggs'
};

meals;
// => { breakfast: "Scrambled eggs" }
```

We can update or overwrite existing properties by assigning a new value to an existing key:
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
// => { cheesePlate: { soft: "Chèvre", semiSoft: "Gruyère", hard: "Manchego" }, fries: "Sweet potato", salad: "Cobb" }
```

Note that modifying an object's properties in the above manner is _destructive_. This means that we're making changes directly to the original object. We could encapsulate this modification process in a function, like so:
```js
function destructivelyUpdateObject (obj, key, value) {
  obj[key] = value;

  return obj;
}
```

At our restaurant, we've finished serving for the day. It's time to update our `mondayMenu` to the `tuesdayMenu`:
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
// => { cheesePlate: { soft: "Chèvre", semiSoft: "Gruyère", hard: "Manchego" }, fries: "Sweet potato", salad: "Caesar" }

tuesdayMenu.salad;
// => "Caesar"
```

Looks like our `tuesdayMenu` came out perfectly. But what about `mondayMenu`?

```js
mondayMenu.salad;
// => "Caesar"
```

Dang! We don't serve Caesar salad on Mondays. Instead of destructively updating the original menu, is there a way to nondestructively merge the change(s) into a new object, leaving the original intact?

## Nondestructively returning a new object
We can create a method that accepts the old menu and the proposed change:
```js
function nondestructivelyUpdateObject(obj, key, value) {
  // Code to return new, updated menu object
}
```

Then, with the ES2015 _spread operator_, we can copy all of the old menu object's properties into a new object:
```js
function nondestructivelyUpdateObject(obj, key, value) {
  const newObj = { ...obj };

  // Code to return new, updated menu object
}
```

And finally, we can update the new menu object with the proposed change and return the updated menu:
```js
function nondestructivelyUpdateObject(obj, key, value) {
  const newObj = { ...obj };

  newObj[key] = value;

  return newObj;
}

const sundayMenu = nondestructivelyUpdateObject(tuesdayMenu, 'fries', 'Shoestring');

tuesdayMenu.fries;
// => "Sweet potato"

sundayMenu.fries;
// => "Shoestring"
```

However, that's quite a bit of code to write, and it's not very extensible. If we want to modify more than a single property, we'll have to completely rewrite our function! Luckily, JavaScript has a much better solution for us.

### `Object.assign()`
JavaScript provides us access to a global `Object` object that has a bunch of helpful methods we can use. One of those methods is `Object.assign()`, which allows us to combine properties from multiple objects into a single object. The first argument passed to `Object.assign()` is the initial object in which all of the properties are merged. Every additional argument is an object whose properties we want to merge into the first object:
```js
Object.assign(initialObject, additionalObject, additionalObject, ...);
```

The return value of `Object.assign()` is the initial object after all of the additional objects' properties have been merged in:
```js
Object.assign({ eggs: 3 }, { flour: '1 cup' });
// => { eggs: 3, flour: "1 cup" }

Object.assign({ eggs: 3 }, { chocolateChips: '1 cup', flour: '2 cups' }, { flour: '1/2 cup' });
// { eggs: 3, chocolateChips: "1 cup", flour: "1/2 cup" }
```

Pay attention to the `flour` property in the above example. **If multiple objects have a property with the same key, the right-most object's value for that key wins out**. Essentially, the last call to `Object.assign()` in the above snippet is wrapping all of the following assignments into a single line of code:
```js
const recipe = { eggs: 3 };

recipe.chocolateChips = '1 cup';

recipe.flour = '2 cups';

recipe.flour = '1/2 cup';
```

A common pattern for `Object.assign()` is to provide an empty object as the first argument. That way we're composing an entirely new object instead of modifying or overwriting the properties of an existing object. This pattern allows us to rewrite the above `destructivelyUpdateObject()` function in a nondestructive way:
```js
function nondestructivelyUpdateObject(obj, key, value) {
  return Object.assign({}, obj, { [key]: value });
}

const recipe = { eggs: 3 };

const newRecipe = nondestructivelyUpdateObject(recipe, 'chocolate', '1 cup');
// => { eggs: 3, chocolate: "1 cup" }

newRecipe;
// => { eggs: 3, chocolate: "1 cup" }

recipe;
// => { eggs: 3 }
```

It's important that we merge everything into a new, empty object. Otherwise, we would be modifying the original object. In your browser's console, test what happens if the body of the above function were `return Object.assign(obj, { [key]: value });`. Uh oh, back to being destructive!

Let's write a function for our restaurant that accepts an old menu and some changes we want to make and returns a new menu **without modifying the old menu**:
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
    semiSoft: 'Fontina'
  },
  salad: 'Southwestern'
};

const wednesdayMenu = createNewMenu(tuesdayMenu, newOfferings);

wednesdayMenu;
// => { cheesePlate: { soft: "Brie", semiSoft: "Fontina", hard: "Manchego" }, fries: "Sweet potato", salad: "Southwestern" }

tuesdayMenu;
// => { cheesePlate: { soft: "Chèvre", semiSoft: "Gruyère", hard: "Manchego" }, fries: "Sweet potato", salad: "Caesar" }
```

Bon appétit!

## `Object.keys()`
Another convenience method available on the global `Object` object is `Object.keys()`. When an object is passed as an argument to `Object.keys()`, the return value is an array containing all of the keys at the top level of the object:
```js
const wednesdayMenu = {
  cheesePlate: {
    soft: 'Brie',
    semiSoft: 'Fontina',
    hard: 'Manchego'
  },
  fries: 'Sweet potato',
  salad: 'Southwestern'
};

Object.keys(wednesdayMenu);
// => ["cheesePlate", "fries", "salad"]
```

Notice that it didn't pick up the keys in the nested `cheesePlate` object — just the keys from the properties declared at the top level within `wednesdayMenu`.

***NOTE***: The sequence in which keys are ordered in the returned array is not consistent across browsers and should not be relied upon. All of the object's keys will be in the array, but you can't count on `keyA` always being at index `0` of the array and `keyB` always being at index `1`.

## Deleting a property from an object
Uh oh, we ran out of Southwestern dressing, so we have to take the salad off the menu. In JavaScript, that's as easy as:
```js
const wednesdayMenu = {
  cheesePlate: {
    soft: 'Brie',
    semiSoft: 'Fontina',
    hard: 'Manchego'
  },
  fries: 'Sweet potato',
  salad: 'Southwestern'
};

delete wednesdayMenu.salad;
// => true

wednesdayMenu;
// => { cheesePlate: { soft: "Brie", semiSoft: "Fontina", hard: "Manchego" }, fries: "Sweet potato" }
```

We pass the property that we'd like to remove to the `delete` operator, and the JavaScript engine takes care of the rest. Poof! No more `salad` property on the `wednesdayMenu` object.

## A brief teaser: arrays and objects
Think back to the early lesson on data types in JavaScript. We listed off seven types into which all data falls: numbers, strings, booleans, symbols, objects, `null`, and `undefined`. Notice anything missing? Arrays!

Why isn't an array a fundamental data type in JavaScript? The answer is that **it's actually a special type of object**. Yes, that's right: ***arrays are objects***. To underscore this point, check out what the `typeof` operator returns when we use it on an array:
```js
typeof [];
// => "object"
```

We can set properties on an array just like a regular object:
```js
const myArray = [];

myArray.summary = 'Empty array!';

myArray;
// => [summary: "Empty array!"]
```

And we can modify and access those properties, too:
```js
myArray['summary'] = 'This array is totally empty.';

myArray;
// => [summary: "This array is totally empty."]

myArray.summary;
// => "This array is totally empty."
```

In fact, _everything_ we just learned how to do to objects can also be done to arrays because arrays **are** objects. Just special ones. To see the special stuff, let's `.push()` some values into our array:
```js
myArray.push(2, 3, 5, 7);
// => 4

myArray;
// => [2, 3, 5, 7, summary: "This array is totally empty."]
```

Cool, looks like everything's still in there. What's your guess about the array's `.length`?

```js
myArray.length;
// => 4
```

Huh, that's interesting. Surely our `summary` must be the first element in the array, no? After all, we did add it before we `.push()`ed all those values in.

```js
myArray[0];
// => 2
```

Hm, then maybe it's the last element?

```js
myArray[myArray.length - 1];
// => undefined
```

What the heck? Where is it?

You see, one of the 'special' features of an array is that **its array-style elements are stored separately from its object-style properties**. The `.length` property of an array describes how many items exist in its special list of elements — its object-style properties are not included in that calculation.

This brings up an interesting question: if we add a new property to an array that has a key of `0`, how does the JavaScript engine know whether it should be an object-style property or an array-style element?

```js
const myArray = [];

myArray[0] = 'Will this be an object property or an array element?';
// => "Will this be an object property or an array element?"

// Moment of truth...
myArray.length;
// => 1
```

So the JavaScript engine used that assignment operation to add a new array-style element. What happens if we enclose the integer in quotation marks, turning it into a string?

```js
myArray['0'] = 'What about this one?';
// => "What about this one?"

myArray.length;
// => 1

myArray;
// => ["What about this one?"]
```

This is hitting on a fundamental truth: **all keys in objects and all indexes in arrays are actually strings**. In `myArray[0]` we're using the integer `0`, but under the hood the JavaScript engine automatically converts that to the string `"0"`. When we access elements or properties of an array, the engine routes all integers and integers masquerading as strings (e.g., `'14'`, `"953"`, etc.) to the array's special list of elements, and it treats everything else as a simple object property. For example:
```js
const myArray = [2, 3, 5, 7];

myArray['1'] = 'Hi';
// => "Hi"

myArray;
// => [2, "Hi", 5, 7]

myArray['01'] = 'Ho';
// => "Ho"

myArray;
// => [2, "Hi", 5, 7, 01: "Ho"]

myArray[01];
// => "Hi"

myArray['01'];
// => "Ho"
```

After adding our weird `'01'` property, the `.length` property still returns `4`:
```js
myArray.length;
// => 4
```

So it would stand to reason that `Object.keys()` would only return `'01'`, right?

```js
Object.keys(myArray);
// => ["0", "1", "2", "3", "01"]
```

Wrong. The reason why arrays have this behavior would take us deep inside the JavaScript source code, and it's frankly not that important. Just remember these simple guidelines, and you'll be just fine:
- **For accessing elements in an array, always use integers**.
- **Be wary of setting object-style properties on an array**. There's rarely any reason to, and it's usually more trouble than it's worth.
- **Remember that all object keys, including array indexes, are strings**. This will really come into play when we learn how to iterate over objects, so keep it in the back of your mind.

## Resources
- MDN
  + [`Object.assign()`][assign]
  + [`Object.keys()`][keys]
  + [`delete`][delete]
  + [Object basics][Basics]

[assign]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign
[keys]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys
[delete]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/delete
[Basics]: https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Basics

<p class='util--hide'>View <a href='https://learn.co/lessons/js-data-structures-objects-readme'>Objects</a> on Learn.co and start learning to code for free.</p>
