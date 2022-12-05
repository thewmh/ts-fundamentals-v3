## Introduction

TypeScript is an open source, typed syntactic superset of JavaScript. Meaning it starts w/ the JS syntax and layers additonal concepts on top. TS is maintained by Microsoft, the goal of TS is to add types to JS. Through the use of the TS compiler, TS compiles out to readable JS, and it comes in three parts; the programming language, the language server, and the language compiler. TS is popular. TS has surpassed React in downloads (which makes sense considering it is framework / library agnostic). But why would anyone care about TS? TS allows you, the developer, to leave more of your intent "on the page" / in the code. The kind of intent that is usually missing from pure JS code. Consider function parameters, how do you communicate what type of parameter (i.e. string, number, boolean) should be passed in to a function? With TS, you can assign types to define what is expected for a particular value:

```
function add(a: number, b: number): number {
    return a + b;
}

add(3, "4");
```

Calling the `add` function w/ a number and a string would cause TS to throw an error that the type 'string' is not assignable to parameter of type 'number'. TS has the potential to move some kinds of errors from runtime (affects end-users) to compile time (affects developers). Some examples of which might be; absent values (`null` or `undefined`), incomplete refactoring, breakage around internal code contracts (i.e. an argument becomes required). TS serves as the foundation for a great code authoring experience w/ in-editor autocomplete.

Q: In a design system, how can I support consuming apps that don't use TS as well as those that do? And do I need to provide type definitions as well as prototypes?
A: One of the core principles of TS is that it needs to be able to work w/ regular JS code. So even if your consumers are using regular JS, they'll still get a lot of the benefits of the type information that TS provides. If you write the TS, all of your developers will get the enhanced experience.

Q: Are there any open source projects where we can practice or apply TS after this workshop?
A: Probably.

Q: Are there any plans to build any aspects of TS into JS?
A: No. JS needs to remain backward compatible, so it is unlikely that TS features will get built-in to JS. There are some things that get rolled out in TS that do get rolled in to JS, but types will probably not be coming to JS.

## Compiling a TypeScript Program

With the minimum possible setup, let's compile a TS program w/ 3 files. The goals of this section are to get hands-on w/ TS and the compiler CLI, learn how the compiler-emitted JJS code changes depending on JS language level and module type, Examine a program's compiled output, including the type declaration file. For this minimal project, we'll have 3 files; `package.json` (package manifest), `tsconfig.json` (TS compiler settings), `src/index.ts` ("the program"). These files are here: `packages/hello-ts/src`. By deault, TS will put output files side-by-side w/ source code. Also by default, TS will output JS to support ES3 syntax (~IE6). Any of the options set in the `tsconfig.json` file can be passed as commands to the TS CLI, but as the complexity of the required configuration grows, using a config file is the better option. The `src/index.ts` file for this lesson is using a couple of JS features that came to the language in ES2015 (Promise) and ES2017 (async/await), which is important when looking at what the target and compiled output is. In a terminal, navigate to `packages/hello-ts` and run `yarn dev`. This will start the TS compiler. You should see a `dist` directory created, inside of which will be an `index.js` file. Take a look at what the ES3 output of that file looks like, then change the `tsconfig.json` target to ES2015, take another look at the `dist/index.js` file, then change the `tsconfig.json` target to ES2017, take another look at the `dist/index.js` file... You should have seen the `dist/index.js` file go from very large for ES3, to essentially the same as `src/index.ts` when setting the target to ES2017. The only difference should be that the TS types have been removed in the final output.

Q: Multi-target output? Use TS to output a modern bundle, then use Babel to transpile for older bowsers (if needed).

You may also notice a `.d.ts` file in the `dist` folder, this is a declaration file. Declaration files only contain type information and are useful for both JS and TS users. JS users can essentially ignore them, TS users can essentially "recombine" them and get the benefits of TS. Another important thing to understand about TS is the types of modules that get exported. If you tried to run the `dist/index.js` file w/ node, you would get an error because node uses CommonJS modules, which is not the default module type in TS. If you wanted to output something that does run w/ node, in the `tsconfig.json` file, you could add to the "compilerOptions" object: `"module": "CommonJS",` Changing the module type will change the exported file. Try it.

## Variables & Values

Using standard JS variable declaration of `let` or `const` (`var` would also work, but eww), TS can infer a type if the variable has been initialized w/ a value. And, if you have initialized a variable w/ a value, then try to assign a different value type to the variable (i.e. initialized w/ a number, try to assign a string), TS will throw an error. In TS, variables are "born" w/ their types. It's imoprtant to assign the types that your variables need to have when the variable is created. If you ever need to make a variable type more general, you usually have to go back to the initial variable definition to do so. When you declare a variable w/ `let`, TS will infer a type from the value the variable is assigned at initialization, if you assigned `6` to your variable, the infered type would be number. W/ `const`, TS will set the type equal to the value that the variable was initialized w/; if the `const` was initialized w/ the value of `6`, the inferred type is also `6`. Essentially, the value a `const` is assigned at initialization can ONLY ever be that type. The type `6` is what is known as a literal type in that it is literally the number 6. If a `let` declaration is a variable that can hold any number, the `const` declaration is one that can only hold `6` - a specific number.

Sometimes you need to declare a variable before it gets initialized and if you've not provided any type information, that variable will be assigned (by TS) the `any` type. `any` is the most flexible type in TS meaning it can be any value that is allowed in JS. To get the benefit of TS, we need to provide what is known as a type annotation to the uninitialized variable. i.e. `let endTime: Date`, note the colon after the variable, w/ the type following that. The same sort of syntax to type variables can also be applied to functions. Here's the same example we saw earlier that shows a typed function:

```
function add(a: number, b: number): number {
    return a + b;
}

add(3, "4");
```

Not only have the arguments of the function been typed, but TS will be able to infer what the types of the return should be.

## Typing Functions

If you want to explicitly define the return type of a function, you can do so:

```
function add(a: number, b: number): number {}
```

If you state the return type in this way, you will limit what you are able to do w/ your function, a better approach may be:

```
function add(a: number, b: number) {
    return 
}
```

TS allows you to implement type driven development where you can verify on a function by function basis whether your program has any errors. TS is not a replacement for tests or unit tests, but it does give you the ability to shift between micro and macro. Is the function doing the right thing, returning the correct thing, etc... All TS does is check types. So if any of your tests are for type validation, those tests can be removed an TS can do the type checking, but for other tests, TS is not a testing framework.

## Typing Functions Q&A and Objects

The `any` type does not only cause problems for itself, but, due to its flexibility, an `any` will also cause problems for well-typed code. An `any` can hold anything and masquerade as anything, which is what makes it dangerous.

Q: Why do we say that there is so much value that comes along w/ TypeScript when you could have done some of these things in JSDoc?
A: Fair point. But JSDoc is less strict than TS meaning you can not type things and JSDoc will not complain. Also, defining complex types in JSDoc is more complicated than using TS. JSDoc was missing strong enforcement of alignment between comments and the code that they were documenting. JSDoc requires a lot of effort to get to the same level of type checking as TS.

Moving on to objects. Object types are defined by; the names of the properties that may be present and the types of those properties. Here is an example of an object that represents a car:

```
{
    make: "Toyota",
    model: "Corolla",
    year: 2022
}
```

The type that would describe the above object's structure would look like this:

```
{
    make: string,
    model: string,
    year: number
}
```

You can also use the above typed object as the type definition for a variable declaration:

```
let car: {
    make: string,
    model: string,
    year: number
}
```

You could also use the type definition in a function definition:

```
function printCar(car: {
    make: string,
    model: string,
    year: number
}) {
    console.log(`${car.make} ${car.model} (${car.year})`)
}
```

## Optional Properties

Optional properties allow you to define a property that may not always be present. Using the `?` operator, you can define an optional property. Here's the `printCar` function with an optional property for `chargeVoltage`:

```
function printCar(car: {
    make: string,
    model: string,
    year: number,
    chargeVoltage?: number
}) {
    let str = `${car.make} ${car.model} (${car.year})`
    car.chargeVoltage

    if(typeof car.chargeVoltage !== "undefined")
        str +=  `// ${car.chargeVoltage}v`;

    console.log(str);
}
```

When defining a property as being optional, the type of the optional property will be either the type you've set (`chargeVoltage` is of the type number) OR `undefined`. In the above example, there is a condition that will only be satisfied when `chargeVoltage` is not of the `undefined` type. This is known as a 'type guard'. With the optional property, you can pass the optional property to the function or not and either will work.

TS also includes excess property checking. If you try to pass an additional property that has not been defined, TS will tell you that object literals may only specify known properties. While in JS, an additional property on an object is not an issue, w/ TS the additional undefined property would never be reached and is therefore unnecessary and TS will complain about it. You can extend the object by explicitly adding additional properties to the type definition, or you could define the extended object as its own variable w/ the additional properties on it.

## Index Signatures & Object Q&A

We've learned how to type rigid objects, but what about dictionaries? A dictionary would be a consistent type of value that is stored under an arbitrary key, an example of which could be phone numbers; home, work, fax as keys, w/ an object w/ coountry code, area code, and number being the value. In order to type something like this, we would describe it using an index signature. Here's what an index signature for a dictionary of phone numbers could look like:

```
const phones: {
    [k: string]: {
        country: string
        area: string
        number: string
    } | undefined // undefined will check whether or not a specific key exists in the dictionary
} = {}
```

Q: Why does storing an object as a variable [and then passing that variable to a function] get rid of an excess property error?
A: Because passing the object w/ an excess property directly as an argument to a function will directly type check against the type definition, whereas the variable w/ object + excess property as its value is separate from the function type definition and any excess properties w/in the variable may be reachable somewhere else.

## Arrays & Tuples

In JS, arrays are the other main type of 'collection' object that we work with. Instead of key / value storage, arrays are 'positional' storage objects. For the most basic of array, just adding `[]` at the end of the array member type will suffice. This also works for inference of type. A tuple is an ordered data structure where the position of each item has some special meaning or convention. Going back to the car example, we might want to create a tuple type that is - `[year, make, model]` - and be typed. TS is unable to effectively infer the types of each element in the array, in the case oo the car tuple, we'd get infered typing that the array is of arbitrary length and contains either strings or numbers. Not super helpful. This inference would allow for additional values in the tuple and for the values to be in any order, which is not useful when you are trying to work with some data and are expecting values of a certain type to be in a specific place. TS is not overly strict w/ arrays out of the box. If you want to make an array a tuple and have it be typed, you will need to explicitly define that type in order to get the type checking and safety. Here is how you could type the tuple:

```
let myCar: [number, string, string] = [
    2002,
    "Toyota",
    "Corolla"
]
```

The only caveat w/ tuples and TS is there is limited support for enforcing tuple length constraints. On assignment, you get the support you might expect, but using `push` or `pop` you do not.

Q: Is TC39 going to make JS a static typed language?
A: Highly unlikely because having JS being a compile target works well enough.

## Structural vs Nominal Types

Let's look at the different kinds of type systems and how they are categorized. But first, what is type checking? Type cheking is a task that attempts to evaluate the question of compatibility or type equivalence; i.e. is what is being passed to a function the same type that the function expects to receive? The same type check occurs when assigning a value to a variable. The same type check happens in a return.

The second thing we should look into for different types of type systems is static or dynamic type systems. A static type system is one that checks types at compile, a dynamic type system is one that checks types at runtime. TS is a static type system.

Another way to delineate different type systems is nominal vs. structural. Nominal type systems are all about names, structural type systems are all about structure or shape. TS is a structural type system, the primary concern of which is whether or not the shape and structure of objects match the expected types.

There's also something called "Duck Typing" which gets its name from thee "duck test" which says: "If it looks like a duck, swims like a duck, quacks like a duck, then its probably a duck". This is similar to structural typing, but is usually used to describe dynamic typing systems.

Finally, there's strong vs. weak types. While both terms are frequently used, there is no agreed-upon technical definition for them. In the context of TS, it's common for those who say it is strongly typed to in fact mean TS is statically typed.

## Union / Intersection Types

Union and intersection types can be thought of as logical boolean operators `AND`, `OR`. The union type is the `OR` logical boolean operator where when used will include anything that is of either condition. The intersection type is the `AND` logical boolean operator where when used will include anything that meets both conditions.

Union types in TS can be described w/ the `|` (pipe) operator. For example, if you have a type that could be one of two strings, "success" or "error", you could define it as: `"success" | "error"`. Using tuples, we could define a union type as:

* [0] either "success" or "failure"
* [1] something different, depending on the value found in [0]
    * "sucess" case: a piece of contact information: `{ name: string; email: string; }`
    * "error" case: an `Error` instance

With the above union type definition, we could implement it into a `coin flip` function:

```
function flipCoin(): "heads" | "tails" {
    if (Math.random() > 0.5) return "heads"
    return "tails"
}

function maybeGetUserInfo():
    | ["error", Error]
    | ["success", { name: string; email: string }] {
        if (flipCoin() === "heads") {
            return [
                "success",
                { name: "Viljar", email: "wharrison@horizontal.com" },
            ]
        } else {
            return [
                "error",
                new Error("The coin landed on TAILS :(")
            ]
        }
    }

    const outcome = maybeGetUserInfo();
```

But how can we consume the value of `outcome` from the above code block? We'd need to narrow the value w/ type guards. By employing `instanceof` we can break apart the two tuple options that could be returned:

```
...
const outcome = maybeGetUserInfo()
const [first, second] = outcome

if (second instanceof Error) {
    // second is an Error
    second
} else {
    // second is the user info
    second
}
```

Via a concept called discriminated unions, we can get additional type checking benefit from our tuples.

```
...
const outcome = maybeGetUserInfo()
if (outcome[0] === "error") {
    // outcome is the error tuple
    outcome
} else {
    // outcome is the success tuple
    outcome
}
```

Another word for a discriminated union is a tagged union type. Moving on to intersection types, it is mentioned that you will find union types far more often. Intersection types cann be described using the `&` operator. For example, what if we had a `Promise`, that had extra `startTime` and `endTime` properties added to it?

```
function makeWeek(): Date & { end: Date } {
    // return type

    const start = new Date()
    const end = new Date(start.valueOf() + ONE_WEEK)

    return { ...start, end }
}

const thisWeek = makeWeek()
thisWeek.toISOString()

thisWeek.end.toISOString()
```

Considering what you've seen so far about union and intersection types, why do you think you would see more union types than intersection types? Because you'll likely more often compare two things to determine what happens next than the intersection of two things to determine a singular action w/ a reduced dataset, AKA control flow.

## Type Aliases & Interfaces

Type aliases are a way to give a "friendly" name to your types. In many cases, you will be free to choose whether you use an interface of a type alias. Looking back to the type annotation syntax we've seen so far: ` : { name: string, email: string } `, this will get increasingly complicated as more properties are addd to this type. And if we pass objects of this type around through various functions and variables, we will end up with a lot of types that need to be manually updated whenever we need to make any changes... Type aliases help to address this by allowing us to: define a more meaningful name for a type, declare the particulars of the type in a single place, import and export the type from modules, the same as if it were an exported value. In other words, the type can be "centrally" defined once and referred to in a bunch of different places. You can use a type alias for any type you wish to define. Here is an example of a type alias named `UserContactInfo`:

```
export type UserContactInfo = {
    name: string
    email: string
}
```

The convention for casing an alias is TitleCase. Type aliases are the rare occasion where you'll see type information on the right hand side of the assignment operator `=`.

With inheritance, you can create type aliases that combine existing types with new behavior by using Intersection `&` types. There actally isn't 'true' inheritence in TS, but a similar outcome.

An interface is a way of defining an object type. An "object type" can be thought of as, "an instance of a class could conceivably look like this". The union type operator makes something not a object type. Like type aliases, interfaces can be imported/exported between modules just like values, and they serve to provide a "name" for a specific type. Just like type aliases, interfaces completely compile away and will not be present in the final output JS file. With interfaces, inheritance has a lot more formality aroundn it. TS calls JS classes that "inherit" behavior from a base class, heritage clauses, as the resultant inheritance has created an ancestral relationship. `extends` is used to describe inheritance between 'like' things, `implements` is used to describe inheritance between unlike things. Classes can "extend" from classes, interfaces can extend from other interfaces, but when working w/ classes and interfaces together, you will want to use the `implements` keyword:

```
interface AnimalLike {
    eat(food): void
}

class Dog implements AnimalLike {
    bark() {
        return "woof"
    }
}
```

In the above codeblock, TS will throw an error because the `Dog` class has not correctly implemented the `AnimalLike` interface, `Dog` is missing the `eat` function. TS and JS do not support true multiple inheritance (extending from more than one base class), the `implements` keyword does provide the ability to validate, at compile time, that instances of a class conform to one or more "contracts" (types). Both `extends` and `implements` can be used together. Interfaces are "open", meaning that unlike type aliases, you can have multiple declarations in the same scope:

```
interface AnimalLike {
    isAlive(): boolaen
}
function feed(animal: AnimalLike) {
    animal.eat
    animal.isAlive
}
interface AnimalLike {
    eat(food): void
}
```

The above code is valid and works. But why would you want to be able to declare an interface of the same name in multiple places, effectively augmenting the interface? Imagine you want to add a global property to the `window` object or add something to an already existing interface. TS allows you to do that. How do you choose whether to use an interface or a type alias? In most situations, either would be perfectly fine, however:

* If you need to define somehing other than an object type (i.e. use of the `|` union type operator), you must use a type alias
* If you need to define a type to use with the `implements` heritage term, it is best to use an interface
* If you need to allow consumers of your types to augment them, you must use an interface

Recursive types are self-referential and are often used to describe infinitely nestable types. Consider infinitely nestable arrays of numbers:

```
;[3, 4, [5, 6, [7], 59], 221]
```

You may read or see things that indicate you must use a combination of `interface` and `type` for recursive types. As of TS 3.7, this is much easier and workswith either type aliases or interfaces:

```
type NesetdNumbers = number | NestedNumbers[]
// a number or an array of itself

const val: NestedNumbers = [3, 4, [5, 6, [7], 59], 221]

if (typeof val !== "number") {
    val.push(41)
}
```

## Functions & Function Overloads

Moving back into the world of functions, we can expand on the argument and return types that were covered earlier. Callable types are interfaces or type aliases that describe functions or something that can be constructed using the `new` keyword. Type aliases and interfaces provide the capability to describe call signatures:

```
interface TwoNumberCalculation {
    (x: number, y: number): number
}

type TwoNumberCalc = (x: number, y: number) => number

const add: TwoNumberCalculation = (a, b) => a + b

const subtract: TwoNumberCalc = (x, y) => x - y
```

When using a type or interface to define a function, the function itself will inherit the types specified by the definition. With regular function definitions, you must declare the types because there is no way for TS to infer them. Sometimes, we don't return anything from functions, but what does a function return if there is no return statement? `undefined`. So functions, if they don't have a return statement, TS has a way of describing that which is known as a `void` type. `void` means that the return from the function should not be used, it should be ignored. You could type the function as `undefined`, but if you do have something implicitly return from a function that has no return statement, i.e. pushing to an array (in which the value pushed will have a type [number] infered by TS), typing such a function as `undefined` will throw an error, whereas if you typed the function as `void`, TS will allow that.

Construct signatures are similar to call signatures, except they describe what should happen w/ the `new` keyword. You might not run into this often, but here's what one looks like:

```
interface DateConstructor {
    new (value: number): Date
}

let MyDateConstructor: DateConstructor = Date
const d = new MyDateConstructor()
```

Construct signatures look very similar to a call signature, but have the `new` keyword.

Something you will come across is function overloads. What if you had to create a function that allowed you to register a "main event listener"?

* If you are passed a `form` element, you should allow the registration of a "submit callback"
* If you are passed an `iframe` element, you should allow the registration of a "`postMessage` callback"

Here's an attempt at solving for the above, using TS:

```
type FormSubmitHandler = (data: FormData) => void
type MessageHandler = (evt: MessageEvent) => void

function handleMainEvent(
    elem: HTMLFormElement | HTMLIframeElement,
    handler: FormSubmitHandler | MessageHandler
) {}

const myFrame = document.getElementByTagName("iframe")[0]

handleMainEvent(myFrame, (val) => {

})
```

The problem above is that `val`  in `handleMainEvent` gets set to the `any` type. There are simply too many possibilities, including things that we probably don't actually aim to support; i.e. using an `HTMLIFrameElement` w/ `FormSubmitHandler` which doesn't really make sense. Let's solve this with function overloads, where we define multiple function heads that serve as entry points to a single implementation:

```
type FormSubmitHandler = (data: FormData) => void
type MessageHandler = (evt: MessageEvent) => void

function handleMainEvent(
    elem: HTMLFormElement,
    handler: FormSubmitHandler
)
function handleMainEvent(
    elem: HTMLIFrameElement,
    handler: MessageHandler
)
function handleMainEvent(
    elem: HTMLFormElement | HTMLIFrameElement,
    handler: FormSubmitHandler | MessageHandler
) {}

const myFrame = document.getElementsByTagName("iframe")[0]

const myForm = document.getElementsByTagName("form")[0]

handleMainEvent(myFrame, (val) => {

})

handleMainEvent(myForm, (val) => {

})
```

The above function overload is much more specific in defining the types and elements for each of the possibilities. This will make TS happy and not provide an `any` type for `val` in either case. The first two definitions of the `handleMainEvent` are referred to as "heads" w/ the last definition being referred to as an implementation of the function. One important thing to note: "implementation" function signatures must be general enough to include everything that's possible through the exposed first and second function heads.

## this Types & Best Practices

`this` type refers to the type of `this` in a function when it is invoked. Methods generally don't need to have the type of `this` defined, but free standing functions do. For example, if you are adding a click event to a DOM element:

```
<button onClick="myClickHandler">Click it!</button>
```

You could definte `myClickHandler` thusly:

```
function myClickHandler(event: Event) {
    this.disabled = true
}

myClickHandler(new Event("click")) // TS doesn't have any issue w/ this
```

In the above example, even though `this` will be the element that fired the event, TS would infer that `this` has the `any` type assigned to it because it has not type annotation associated w/ it. To fix the error, we'd need to give the function a `this` type:

```
function myClickHandler(
    this: HTMLButtonElement,
    event: Event
) {
    this.disabled = true
}

myClickHandler(new Event("click)) // TS doesn't like this
```

Now, `this` has the correct type assigned, but the call to `myClickHandler` has broken. TS doesn't know what `this` will be, rather TS complains that the `this` context of type `void` is not assignable to method's `this` of type `HTMLButtonElement`. To fix this issue, you need to either bind the function or use `.call`.

Here's some function type best practices, starting w/ explicitly defined return types. TS is capable of inferring function return types quite effectively, but this accommodating behavior can lead to unintentional ripple effects where types change throughout your codebase. Consider this:

```
export async function getData(url: string) {
    const resp = await fetch(url)
    const data = (await resp.json()) as {
        properties: string[]
    }
    return data
}

function loadData() {
    getData("https://example.com").then((result) => {
        console.log(result.properties.join(", "))
    })
}
```

If you changed the `getData` function to only return the data on a successful response, you would see an error pop up in the `loadData` function, not where the error occurs, in the implementation. To get the error to show in the correct place, you'd have to update the `getData` function w/ additional typing.

## Classes & Access Modifier Keywords

TS classes add some powerful and important features on top of traditional JS classes. Let's take a close look at class fields, access modifier keywords, and more. Starting w/ class fields, let's take another look at the car example from earlier:

```
class Car {
    constructor(make, model, year) {
        this.make = make
        this.model = model
        this.year = year
    }
}

let sedan = new Car("Honda", "Accord", 2017)
sedan.activateTurnSignal("left") // not type safe
new Car(2017, "Honda", "Accord") // not type safe
```

The above code is fine in a JS only world, where every value, including class fields and instances of the class itself, is effectively of type `any`. In the TS world, we want some assurance that we will be stopped at compile time from invoking the non-existent `activateTurnSignal` method on the car. In order to get this, we need to provide additional information up front:

```
class Car {
    make: string
    model: string
    year: number
    constructor(make: string, model: string, year: number) {
        this.make = make
        this.model = model
        this.year = year
    }
}

let sedan = new Car("Honda", "Accord", 2017)
sedan.activateTurnSignal("left") // does not exist
new Car(2017, "Honda", "Accord") // incompatible types
```

Now, the class types are defined up front and the constructor, like a function call signature, has all the arguments and their types passed in, the rest remains unaltered. TS will properly throw errors for attempting to invoke non-existent methods, or passing in arguments of the wrong type. The code is very verbose, but we'll get to reducing that soon.

TS provides three access modifier keywords; `public`, `protected`, and `private`, which can be used with class fields and methods, to describe who should be able to see and use them.

* `public` - everyone can see and access (this is default)
* `protected` - the instance itself, and subclasses
* `private` - only the instance itself

Here's how access modifier keywords work w/in the context of the car example:

```
class Car {
    public make: string
    public model: string
    public year: number
    protected vinNumber = generateVinNumber()
    private doorLockCode = generateDoorLockCode()

    constructor(make: string, model: string, year: number) {
        this.make = make
        this.model = model
        this.year = year
    }

    protected unlockAllDoors() {
        unlockCar(this, this.doorLockCode)
    }

    class Sedan extends Car {
        constructor(make: string, model: string, year: number) {
            super(make, model, year)
            this.vinNumber
            this.doorLockCode // not accessible, only available in the Car class, not in this subclass
        }
    }

    public unlock() {
        console.log("Unlocking at " + new Date().toISOString())
        this.unlockAllDoors()
    }
}

let s = new Sedan("Honda", "Accord", 2017)
s.make
s.vinNumber // protected and only accessible w/in class "Car" and its subclasses
s.doorLockCode // private and only accessible w/in class "Car"
s.unlock()
```

A few things to note from the above example:

* The top-level scope doesn't seem to have access to `vinNumber` or `doorLockCode`
* `Sedan` doesn't have direct access to `doorLockCode`, but it can access `vinNumber` and `unlockAllDoors()`
* There are 2 examples of "limited exposure"
    * `Car` can expose `private` functionality through defining its own `protected` functionality
    * `Sedan` can expose `protected` functionality through defining its own `public` functionality

All of the access modifier keywords are only about encapsulation, not security. If a user decided to set a breakpoint and inspect the code that's executing at runtime, they'll be able to see everything. Do not store any sensitive information and expect it to be safe by means of access modifier keywords. It won't be.

JS recently got the concept of private class fields which are denoted by the `#` symbol. TS supports private class fields:

```
class Car {
    public make: string
    public model: string
    #year: number

    constructor(make: string, model: string, year: number) {
        this.make = make
        this.model = model
        this.#year = year
    }
}

const c = new Car("Honda", "Accord", 2017)
c.#year // not accessible outside of the class because it has a private identifier
```

While not strictly an access modifier keyword (because it has nothing to do w/ visibility), TS provides a `readonly` keyword that can be used w/ class fields:

```
class Car {
    public make: string
    public model: string
    public readonly year: number

    constructor(make: string, model: string, year: number) {
        this.make = make
        this.model = model
        this.year = year
    }

    updateYear() {
        this.year++ // cannot assign to 'year' because it is a read-only property
    }
}
```

Now that we've covered access modifier keywords, let's look at param properties, which are a more concise way to write classes.

```
class Car {
    constructor(
        public make: string,
        public model: string,
        public year: number
    ) {}
}
```

The above will be the only time you will see an access modifier keyword next to something other than a class member. The first argument passed to a constructor should be a `string` and should be available within the scope of the constructor as `make`. This also creates a `public` class field on `Car` called `make` and passed it the value that was given to the constructor. This is the order of what ends up in a class constructor:

1. `super()`
2. param property initialization
3. other class field initialization
4. anything w/in the body of the constructor after `super()`

In JS it is possible to put something before `super()`, but the use of class field initializers or param properties disallows this.

## Top Types: any & unknown

We're going to look at types in terms of something called set theory. We can think about types as a set of values that a variable or function argument might be; i.e. if we were to set the value of a variable to the `boolean` type, that variable could be either item from the following set of values: `{true, false}`. Another example could be setting the value of a variable to the `number` type, the value assigned to that variable could be any number. Here's a few more examples:

```
let a: 5 | 6 | 7 // anything in {5, 6, 7}
let b: null // anything in {null}
let c: {
    favoriteFruit?: "pineapple" // {"pineapple" | undefined}
}
```

A top type (symbol: `T`) is a type that describes any possible value allowed by the system. To use the previous set theory mental model we just established, we could describe the as `{x | x could be anything}`. TS provides two such types: `any` and `unknown`. Values w/ an `any` type might be familiar from standard [type-free] JS:

```
let flexible: any = 4
flexible = "Download some more ram"
flexible = window.document
flexible = setTimeout
```

`any` types values provide none of the safety we expect from using TS. `any` is not necessarily a problem — sometimes it's exactly the right type to use for a particular situation. For example, for `console.log()` it is appropriate that `any` be its type because it is intended to be able to log anything to the console.

`unknown`, like `any`, can accept any value, but values w/ an `unknown` type cannot be used w/out first applying a type guard; i.e. by using `typeof` to check its type. You will often run into places where top types come in handy. In particular, if you convert a project from JS to TS, it is very convenient to be able to incrementally add increasingly strong types. A lot of things will be `any` until you get a chance to give them some attention. `unknown` is great for values received at runtime (i.e. your daat layer). By obligating consumers of these values to perform some light validation before using them, errors are caught earlier, and can often be surfaced w/ more context.

## Bottom Types: never

A bottom type (symbol: `⊥`) is a type that describes no possible value allowed by the system. Again using the set theory mental model, this could be described as "any value from the following set: `{}` (intentionally empty)". TS provides one bottom type: `never`. This might appear extremely abstract and pointless, but there's one use case that should convince you otherwise. And that use case is, exhaustive conditionals:

```
class Car {
    drive() {
        console.log("vroom")
    }
}

class Truck {
    tow() {
        console.log("dragging bodies")
    }
}

type Vehicle = Truck | Car

let myVehicle: Vehicle = obtainRandomVehicle()

if (myVehicle instanceof Truck) {
    myVehicle.tow()
} else if (myVehicle instanceof Car) {
    myVehicle.drive()
} else {
    const neverValue: never = myVehicle
}
```

The final `else` clause in the above conditional is an example of where the `never` type is useful. All of the other possible conditions have been considered, if anything else should happen, the value should be nothing as there is nothing else that the value should be. If someone added an additional vehicle class and added it to the union of type Vehicle, but did not add the new vehicle to the conditional check, TS would throw an error and the code author would have an indication that they've not completely finished their code.

## Type Guards & Narrowing

We've already seen some examples of built-in type guards `instanceof`, `typeof`, `value === null`, `!value`, `Array.isArray(value)`, `"value" in value`. There is also the option to have user-defined type guards. How could we validate objects that are type-equivalent w/ the following interface:

```
interface CarLike {
    make: string
    model: string
    year: number
}

let maybeCar: unknown

if (
    maybeCar &&
    typeof maybeCar === "object" &&
    "make" in maybeCar &&
    typeof maybeCar["make"] === "string" &&
    "model" in maybeCar &&
    typeof maybeCar["model"] === "string" &&
    "year" in maybeCar &&
    typeof maybeCar["year"] === "number"
) {
    maybeCar
}
```

Validating in the above way may be possible, but it would almost certainly involve type casting. And even if it did work, it is getting messy enough that you'd want to refactor it out into a function or something, so that it could be reused across the codebase. That refactor could look like this:

```
interface CarLike {
    make: string
    model: string
    year: number
}

let maybeCar: unknown

function isCarLike(valueToTest: any) {
    return (
        valueToTest &&
        typeof valueToTest === "object" &&
        "make" in valueToTest &&
        typeof valueToTest["make"] === "string" &&
        "model" in valueToTest &&
        typeof valueToTest["model"] === "string" &&
        "year" in valueToTest &&
        typeof valueToTest["year"] === "number"
    )
}

if (isCarLike(maybeCar)) {
    maybeCar
}
```

The above refactor still doesn't effectively solve the problem. TS has no idea that the return value of `isCarLike` has anything to do w/ `valueToTest`. Let's look at better options to user-defined type guards.`is` is perfectly suited for the last 2 examples because it is meant to work in cooperation w/ a control flow statement of some sort, to indicate the different branches of the "flow" will be taken based on an evaluation of `valueToTest`'s type. Pay close attention to `isCarLike`'s return type.

```
interface CarLike {
    make: string
    model: string
    year: number
}

let maybeCar: unknown

function isCarLike(
    valueToTest: any
): valueToTest is CarLike {
    return (
        valueToTest &&
        typeof valueToTest === "object" &&
        "make" in valueToTest &&
        typeof valueToTest["make"] === "string" &&
        "model" in valueToTest &&
        typeof valueToTest["model"] === "string" &&
        "year" in valueToTest &&
        typeof valueToTest["year"] === "number"
    )
}

if (isCarLike(maybeCar)) {
    maybeCar
}
```

Now, using `is`, the return of `isCarLike`, `maybeCar`, will conform to the `CarLike` interface. TS is trusting that the checking logic you've implemented is accurate though and you will need to be vigilant in setting up the `is`. The second user-defined type guard we will look at is `asserts value is Foo` which eliminates the need for a conditional. In the following example pay close attention to `assertsIsCarLike` return type:

```
interface CarLike {
    make: string
    model: string
    year: number
}

let maybeCar: unknown

function assertsIsCarLike(
    valueToTest: any
): asserts valueToTest is CarLike {
    if (
        !(
            valueToTest &&
            typeof valueToTest === "object" &&
            "make" in valueToTest &&
            typeof valueToTest["make"] === "string" &&
            "model" in valueToTest &&
            typeof valueToTest["model"] === "string" &&
            "year" in valueToTest &&
            typeof valueToTest["year"] === "number"
        )
    )
        throw new Error(
            `Value does not appear to be CarLike${valueToTest}`
        )
}

maybeCar

assertsIsCarLike(maybeCar)
maybeCar
```

Conceptually, what is happening above is very similar. By using the `asserts ___ is ___` syntax to describe the return type, TS is informed that if `assertsIsCarLike` throws an error, it should be taken as an indication that the `valueToTest` is NOT type-equivalent to `CarLike`. Therefore, if we get past the assertion and keep executing code on the next line, the type changes from `unknown` to `CarLike`.

## Nullish Values

There are situations where you need to plan for and deal w/ the possibility that values are `null`, `undefined`, or `void`. `null` indicates that there is a value for something and that value is nothing. This nothing is very much a defined value, and is certainly a presence — not an absence — of infomation.

```
const userInfo = {
    name: "FirstName",
    email: "email@address.com",
    secondaryEmail: null, // user has no secondaryEmail
}
```

`undefined` means the value isn't available (yet?). In the following example, `completedAt` will be set at some point but there is a period of time when it hasn't been set. `undefined` is an unambiguous indication thhat there may be something different in the future:

```
const formInProgress = {
    createdAt: new Date(),
    data: new FormData(),
    completedAt: undefined
}

function submitForm() {
    formInProgress.completedAt = new Date()
}
```

`void` should be used explicitly for function returns when the return value of a function should be ignored.

```
console.log(`console.log returns nothing.`)
```

The non-null assertion operator `!.` is used to case away the possibility that a value might be `null` or `undefined`. The value still could be `null` or `undefined`, this operator just tells TS to ignore that possibility. If the value does turn out to be missing, you will get `cannot call foo on undefined` errors at runtime.

```
type GroceryCart = {
    fruits?: { name: string; qty: number }[]
    vegetables?: { name: string; qty: number }[]
}

const cart: GroceryCart = {}

cart.fruits.push({ name: "kumkuat", qty: 1 })
// Object is possibly 'undefined'

cart.fruits!.push({ name: "kumkuat", qty: 1 })
```

It is not recommended to use the non-null assertion operator in your app, but if your test infrastructure represents a `throw` as a test failure (most should) this is a great type guard to use in your test suite. In the above situation, if `fruits` was expected to be present ans it isn't, that is a reasonable test failure.

The definite assignment `!:` operator is used to supress TS's objections about a class field being used, when it can't be proven that it was initialized:

```
class ThingWithAsyncSetup {
    setupPromise: Promise<any>
    isSetup: boolean // has no initializer and is not definitely assigned in the constructor.

    constructor() {
        this.setupPromise = new Promise((resolve) => {
            this.isSetup = false
            return this.doSetup()
        }).then(() => {
            this.isSetup = true
        })

        private async doSetup() {
            // async things
        }
    }
}
```

TS cannot tell the difference between a synchronous callback vs an asynchronous callback, which is why the error will be thrown for `isSetup`. If instead, because YOU know the boolean will get initialized and TS should STFU, when setting `isSetup` up, do it like so: `isSetup!: boolean` and TS will calm down. A great place to use the definite assignment operator is with lifecycle hooks.

## Generics

 Generics allow us to parameterize types, which unlocks the opportunity to reuse types broadly across a TS project. Earlier, we saw dictionary data structures that could be types using index signatures:

 ```
 const phones: {
    [k: string]: {
        customerId: string
        areaCode: string
        num: string
    }
 } = {}

 phones.home
 phones.work
 phones.fax
 phones.mobile
 ```

 Sometimes, it is more convenient to organize data as key-value dictionaries, and other times more convenient to use arrays or lists. It would be nice to have some kind of utility that would allow us to convert a "list of things" into a "dictionary of things". We'll start w/ this array of objects:

 ```
 const phoneList = [
    { customerId: "0001", areaCode: "321", num: "123-4566" },
    { customerId: "0002", areaCode: "174", num: "173-9902" },
    { customerId: "0003", areaCode: "612", num: "897-1653" },
    { customerId: "0004", areaCode: "952", num: "435-0918" },
    { customerId: "0005", areaCode: "401", num: "816-4053" },
 ]
 ```

 And this is what we want to end up w/:

 ```
 const phoneDict = {
    "0001": {
        customerId: "0001",
        areaCode: "321",
        num: "123-4566"
    },
    "0002": {
        customerId: "0002",
        areaCode: "174",
        num: "173-9902"
    },
    // rest of data...  
 }
 ```

 One thing we will need to go from an array of objects to the dictionary data format is a way to produce the key for each object we encounter in the `phoneList` array. To remain flexible, we will design the function such that whoever is asking for the list-to-dictionary conversion should also provide a function that we can use to obtain a key from each item in the list. A possible function signature could look like this:

 ```
 interface PhoneInfo {
    customerId: string
    areaCode: string
    num: string
 }

 function listToDict(
    list: PhoneInfo[], // take list as argument
    idGen: (arg: PhoneInfo) => string // callback to get Ids
 ): { [k: string]: PhoneInfo } {
    // throws an error because there is no callback function yet
    // return a dictionary
 }
 ```

Let's throw in a `forEach` function to get rid of the error:

```
 function listToDict(
    list: PhoneInfo[], // take list as argument
    idGen: (arg: PhoneInfo) => string // callback to get Ids
 ): { [k: string]: PhoneInfo } {
    // create empty dictionary
    const dict: { [k: string]: PhoneInfo } = {}

    // loop through array
    list.forEach((element) => {
        const dictKey = idGen(element) // store element under key
        dict[dictKey] = element
    })

    // return dictionary
    return dict
 }

 console.log(
    listToDict(phoneList, (item) => item.customerId)
 )
 ```

 Let's now make the code more general and make it so it works for lists and dictionaries of the `PhoneInfo` type, but lots of other types as well. We could replace every `PhoneInfo` w/ any:

```
function listToDict(
    list: any[],
    idGen: (arg: any) => string
 ): { [k: string]: any } {
    
    const dict: { [k: string]: any } = {}
    list.forEach((element) => {
        const dictKey = idGen(element)
        dict[dictKey] = element
    })

    return dict
 }

const dict = listToDict(
    [{ name: "James" }, { name: "Theodore" }, { name: "Jessica" }],
    (item) => item.name
)

console.log(dict)
```

The problem w/ the above generic approach, is we lose the type safety. Now everything is an `any`. We need a mechanism to define a relationship between the type of the thing we're passed and the type of thing we'll return. This is what generics are all about. The first thing we need to do is define a type parameter which can be thought of as function arguments, but for types. Functions may return different values depending on the arguments you pass them. Generics may change their type, depending on the type parameters you use w/ them. The function signature is now going to include a type parameter `T`:

```
function listToDict<T>(
    list: T[],
    idGen: (arg: T) => string
): { [k: string]: T } {
    const dict: { [k: string]: T } = {}
    return dict
}
```

Here's a full breakdown of what the `T`(TypeParam) does in the above code:

* `<T>` to the right of `listDict` — means that the type of this function is now parameterized in terms of a type parameter `T` (which may change on a per-usage basis). You do not have to use the letter 'T'.
* `list: T[]` as a first argument means we accept a list of `T`'s which means on a per-invocation basis, you might end up with a different type `T`. `T` will become whatever it is being passed.
    * TS will infer what `T` is on a per-usage basis depending on what kind of array is passed in. If it's a `string[]`, `T` will be `string`, if we use a `number[]`, `T` will be `number`.

Here is a much simpler example:

```
function wrapInArray<T>(arg: T): [T] {
    return [arg]
}
```

In the above function, whatever gets passed to the function will get wrapped in an array. Let's take a look at some potential return types for the above function:

```
wrapInArray(3) // function wrapInArray<number>(arg: number): [number]

wrapInArray(new Date()) // function wrapInArray<Date>(arg: Date): [Date]

wrapInArray(new RegExp("/s/")) // function wrapInArray<RegExp>(arg: RegExp): [RegExp]
```

Back to the `listToDict` example, here's some more details about what `T` is doing:

* `idGen: (arg: T) => string` is a callback that also uses `T` as an argument. This means:
    * we will get the benefits of type checking within the `idGen` function
    * we will get some type checking alignment between thhe array and the `idGen` function

The last thing to look at in the `listToDict` example is the return type. Based on the way the function has been defined, a `T[]` will be urned into a `{ [k: string]: T }` for any `T` of our choosing.

## Dictionary map, filter & reduce

Exercise. Skipping notes.

## Generics Scopes & Restraints

