#What is worth not to forget in TypeScript
---
All examples in this text are checked for TS version 5.4.5

## CatDog!
```ts
interface Cat {
  legs: number;
  tail: string;
  wiskies: number;
}

class Kitty implements Cat {
  legs = 4;
  wiskies = 8;
  tail = 'Is my document'
}

interface Dog {
  legs: number;
  woof?: () => void;
}

function test(animal: Dog) {
  console.log(animal);
}


// NOTE: now we pass a Cat to the function that expects input parameter of type Dog aaaand.... nothing causes an error, TS thinks, that everything is ok
const fluffy: Cat = { legs: 5, wiskies: 16, tail: 'Not the right way to eat butterbread'};
const ginger = new Kitty();
test(fluffy);
// NOTE: ... and it really doesn't matter how the cat is created (this notice might seem strange, but common it's TS :) sometimes creation way matters (see below))
test(ginger);
```

Enother example:
```ts
class Sphere {
  radius: number;

  constructor(radius: number) {
    this.radius = radius;
  }

  volume() {
    return 3 / 4 * Math.PI * Math.pow(this.radius, 3);
  }
}

class Cylinder {
  radius: number;

  height: number;

  constructor(radius: number, height: number) {
    this.radius = radius;
    this.height = height;
  }

  volume() {
    return 2 * Math.PI * Math.pow(this.radius, 2) * this.height;
  }
}

function testFigure(s: Sphere) {
  console.log(s.volume());
}

const s = new Sphere(4);
const c = new Cylinder(4, 2000);

testFigure(c);
```

There is no real types in TS, only structural checks. To match the structural check objects should only have a matching required properties.
For the places where real typisation matters I saw a solution: create a class required property which key is a unique hash.
But this sounds really painfull.

## Method overload? - no!
There is a function overload, but... no method overload!
It is possible to create a multiple method signatures, but implementation is only one. To learn more:
https://dev.to/tomekbuszewski/method-overloading-in-typescript-25pm

## Automatically expandable interfaces (... but you expect it right? :) )
```ts
interface Stuff {
  prop1: number;
}

interface Stuff {
  prop2: number;
}

const stuff: Stuff = {prop1: 5} // Error: Property 'prop2' is missing
```
... and now let us image that we working with geometry objects and importing type definitions from some geometry library...

## Ambivalent strictness
```ts
interface Stuff {
  name: string,
  dataType: any,
}

const obj = {name: 'name', dataType: 3, otherData: 5};
const check: Stuff = obj; // No errors! TS is ok with it
const check2: Stuff = {name: 'name', dataType: 3, otherData: 5}; // Error: property `otherData` is missing in type Stuff... but wait! isn't that the same?
```

## Readonly protects properties!! hooray!... but only direct children
Nested objects in `readonly` properties might be changed. If you need a REAL `readonly`, you can use DeepReadonly from ts-essentials

## Your objects are too narrow! (с) TS
If types don't defined explicitly in a variables assignment, then TS will select a more common type for `let` assignment and more narrow type for `const` 
```
let x = 'x'; // x is of type string
const x = 'x'; // x is of type... 'x'
let x = 'x' as const; // x is of type... 'x' ... again
```
in objects type definition will work by default as `let` assignment if nothing else is defined
```
const A = {
   a: 'x' // A.a is of type string
   b: 'x' as const // A.b is of type 'x'
}
```
and it is a pretty normal to use `as const` and also treated as a good practice (:
For me it is a little strange to note that:
`as const` - good
`as SuperType` - baaad
