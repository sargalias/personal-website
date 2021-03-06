---
title: 'Programming first principles - 11. First principle - Separation of concerns'
slug: 'programming-first-principles-first-principle-separation-of-concerns'
date: 2020-02-02
author: 'Spyros Argalias'
featuredImage: './programming-first-principles-first-principle-separation-of-concerns.png'
category: 'Programming principles'
excerpt: What is separation of concerns and why do we need it for software that's safe and easy to work with?

seoTitle: 'Programming first principles - First principle - Separation of concerns'
seoDescription: What is separation of concerns and why do we need it for software that's safe and easy to work with?
---

This article is part of the "Programming first principles series":

1. [Purpose - What this series is about](/blog/programming-first-principles-purpose-what-this-series-is-about/)
2. [Audience - Who this series is for](/blog/programming-first-principles-audience-who-this-series-is-for/)
3. [Requirements of software](/blog/programming-first-principles-requirements-of-software/)
4. [Premise - Minimal information](/blog/programming-first-principles-premise-minimal-information/)
5. [Premise - We must understand what we're doing](/blog/programming-first-principles-premise-we-must-understand-what-were-doing/)
6. [Premise - Minimize propagating changes throughout the system](/blog/programming-first-principles-premise-minimize-propagating-changes/)
7. [Premise - Complexity increases exponentially with scale](/blog/programming-first-principles-premise-complexity-increases-exponentially-with-scale/)
8. [First principle - Proof that code works](/blog/programming-first-principles-first-principle-proof-that-code-works/)
9. [First principle - Principle of least astonishment](/blog/programming-first-principles-first-principle-principle-of-least-astonishment/)
10. [First principle - Principle of least knowledge](/blog/programming-first-principles-first-principle-principle-of-least-knowledge/)
11. [First principle - Separation of concerns](/blog/programming-first-principles-first-principle-separation-of-concerns/) (this article)
12. [First principle - Abstraction](/blog/programming-first-principles-first-principle-abstraction/)
13. [Side effects](/blog/programming-first-principles-side-effects/)

Also suggested:

- [When not to apply programming principles](/blog/when-not-to-apply-programming-principles/)
- [Why code changes are error prone](/blog/why-code-changes-are-error-prone/)

---

Along with the other first principles, this is one of the most important things in a complex system.

Before we start, I would like to recap that a lot of our first principles overlap. Certain aspects of them can be thought of as applications of the other first principles. Also there are many other principles such as the "single responsibility principle", "open-closed principle", etc. that can also be thought of as applications or equivalents to these principles. When explaining, I'll try to keep the principles as distinct as possible.

## What does separation of concerns mean?

This is a bit difficult to describe.

You know how with OOP we think of objects as though they are simulating a real-life thing? E.g. we have a `Car` class that represents a car, or a `Person` class that represents information about a person and what they can do.

I'll try to use that spirit to explain separation of concerns.

It means that:

- **Code should be as selfish, ignorant and lazy as possible**.
- It should do a single thing only, a very small single thing. It should be extremely lazy.
- It should delegate everything else to other functions / methods. It should not care about how those other functions do things. It should be ignorant.
- It should know as little as possible about everything else in the codebase. See principle of least knowledge for more information on this.
- It should only care about itself and the little thing it does. It should be absolutely selfish.
- It should not care about who calls it, why it's called or where it's called (that's the responsibility of the caller). It should be selfish and not care about anything except itself.

## Examples

Let's start with some real-life analogies.

### Ordering a product

If you order a product online to be delivered to your house, you don't particularly care how it's delivered.

It's not your concern.

It doesn't matter if it comes by car, if it had to go on an airplane at some point, or a boat, or whatever.

You just don't care and it doesn't affect your life in any way.

### Delivering an item with post

If you're delivering an item to a friend, here's how the process might go:

1. The post office has certain rules you need to follow, specifications for things it accepts, etc. You keep those in mind.
2. You get your friend's exact address.
3. You prepare the package as required by the post office.
4. You go to the post office, give them the package, and they send it.

In terms of separation of concerns:

- You do not care how the post office does the delivery. It is not your concern. You just trust it will be fine.
- You do not care where your friend lives (well, in the context of this exercise at least...). They could live in the UK, or in the North Pole. You just get the address from them and write it on the package.
- You just do the smallest amount of work required (ask for the address and prepare the package), and then delegate the rest.

(Okay we're simplifying the situation by ignoring weird addresses, insurance, etc. but otherwise the situation is fairly analogous to programming.)

Let's relate it to code a bit more. Here is what the code may look like:

```js
function getItem(item) {
  // Internal stuff that no one else in the codebase cares about
}

function createPackage(item, address) {
  // Internal stuff that no one else in the codebase cares about
}

function post(package) {
  // Internal stuff that no one else in the codebase cares about
}

function main() {
  const item = getItem('super-gadget');
  const address = myFriend.getAddress();
  const package = createPackage(item, address);
  post(package);
}
```

Here is what the code should not look like:

```js
function main() {
  // Code to go buy item from the store
  // Code to get address from friend
  // Code to package up the item
  // Code to deliver the item
}
```

In the bad version we have a `main` function that does everything.

Assuming we put all code from `post`, `packageItem`, etc. in main, including all helper functions, `main` could end up being hundreds or thousands of lines long.

There are more reasons and implications for why this is bad, but more on that in a bit.

In the good example, we try to clearly separate concerns:

- Just like it would be in real life, delivering post is a separate concern which is not your problem and you don't care about.
- Your friend provides you with the address and that's it. You don't go out searching for it or whatever.
- You prepare the package. How the package is prepared has 0 effect on everything else. It is a separate concern. Therefore it should be a separate, isolated function in the program.
- Getting the item is its own concern. It is a separate process you could delegate to someone else. It doesn't matter how the item is acquired (online purchase / buying it in-person in a store / manufacturing it yourself). It is a completely separate concern that in no way influences anything else. So it should be isolated and separate.

### Fizzbuzz example

Next let's look at something like fizzbuzz.

We'll start with simple fizzbuzz but then expand the example to show more of what may happen in a real application.

Anyway, let's start with this: Standard fizzbuzz + we need to log the results of the first 100 numbers.

Good example:

```js
function fizzbuzz(n) {
  if (n % 15 === 0) {
    return 'fizzbuzz';
  } else if (n % 3 === 0) {
    return 'fizz';
  } else if (n % 5 === 0) {
    return 'buzz';
  }
}

function main() {
  for (let i = 1; i <= 100; i++) {
    console.log(fizzbuzz(i));
  }
}
```

Example with even better separation of concerns, although I wouldn't actually use this version because I think it's a bit overkill in this simple scenario. (See [when not to apply programming principles](/blog/when-not-to-apply-programming-principles/).)

```js
function fizzbuzz(n) {
  if (n % 15 === 0) {
    return 'fizzbuzz';
  } else if (n % 3 === 0) {
    return 'fizz';
  } else if (n % 5 === 0) {
    return 'buzz';
  }
}

function display(content) {
  console.log(content);
}

function main() {
  for (let i = 1; i <= 100; i++) {
    display(fizzbuzz(i));
  }
}
```

Bad example:

```js
function fizzbuzz(n) {
  let result;
  if (n % 15 === 0) {
    result = 'fizzbuzz';
  } else if (n % 3 === 0) {
    result = 'fizz';
  } else if (n % 5 === 0) {
    result = 'buzz';
  }
  console.log(result);
}

function main() {
  for (let i = 1; i <= 100; i++) {
    fizzbuzz(i);
  }
}
```

Worst example:

```js
function fizzbuzz(n) {
  if (n % 15 === 0) {
    console.log('fizzbuzz');
  } else if (n % 3 === 0) {
    console.log('fizz');
  } else if (n % 5 === 0) {
    console.log('buzz');
  }
}

function main() {
  for (let i = 1; i <= 100; i++) {
    fizzbuzz(i);
  }
}
```

The subtle difference in the last examples is that we're now mixing the display logic and the fizzbuzz calculation into one function.

Even worse, in the last example we have many duplicate `console.log` statements.

Let's talk about what consequences these might have:

**Testability:**

`fizzbuzz` is not a pure function anymore. In the first example testing it is trivially easy. In the second example we'll have to do additional work to mock `console.log`.

`main` should have no difference here. The best way to test it would be to mock `console.log` and see what it has been called with. This way we are not testing implementation details and only testing the end result, but more on that when we cover the principles of testing. More things would be different if we were using dependency injection, but let's ignore this point for now.

**Reusability**

In the first examples we can take `fizzbuzz` and re-use it anywhere in the codebase.

In the later examples, `fizzbuzz` is impossible to re-use unless we also need to `console.log` it every time.

And of course the more concerns we mix together, the more difficult it is to reuse code.

Separating concerns increases re-usability. This can also be thought of as an application of abstraction.

**Changeability**

In this very simple case we'll probably never change the `fizzbuzz` calculation. Fizzbuzz is something that has a global definition worldwide. It's not something that is driven by our client requirements which could change at any time.

However we may need to change our display functionality. Perhaps new requirements need us to display it on the DOM. Fairly simple stuff. We just get the target element, and replace / append the content.

The DOM display code may be something like this:

```js
const target = document.getElementById('target');
target.textContent = 'something';
```

Here are the examples again with this change:

Good example:

```js
function fizzbuzz(n) {
  if (n % 15 === 0) {
    return 'fizzbuzz';
  } else if (n % 3 === 0) {
    return 'fizz';
  } else if (n % 5 === 0) {
    return 'buzz';
  }
}

function display(content) {
  const target = document.getElementById('target');
  target.textContent += `\n${content}`;
}

function main() {
  for (let i = 1; i <= 100; i++) {
    display(fizzbuzz(i));
  }
}
```

Bad example:

```js
function fizzbuzz(n) {
  let result;
  if (n % 15 === 0) {
    result = 'fizzbuzz';
  } else if (n % 3 === 0) {
    result = 'fizz';
  } else if (n % 5 === 0) {
    result = 'buzz';
  }
  display(result);
}

function display(content) {
  const target = document.getElementById('target');
  target.textContent += `\n${content}`;
}

function main() {
  for (let i = 1; i <= 100; i++) {
    fizzbuzz(i);
  }
}
```

Really bad example:

```js
function fizzbuzz(n) {
  const target = document.getElementById('target');

  let result;
  if (n % 15 === 0) {
    result = 'fizzbuzz';
  } else if (n % 3 === 0) {
    result = 'fizz';
  } else if (n % 5 === 0) {
    result = 'buzz';
  }

  target.textContent += `\n${result}`;
}

function main() {
  for (let i = 1; i <= 100; i++) {
    fizzbuzz(i);
  }
}
```

Notice that:

- In the first example `fizzbuzz` didn't have to change at all.
- In our original second example (the one that originally applied the best separation of concerns) the `main` function didn't need to change either. Only the `display` function had to change and nothing else. In other words there were no propagating changes.
- In the bad example we at least only changed `fizzbuzz` minorly and abstracted our `display` function.
- In the really bad example we had to change `fizzbuzz` and make it even bigger. With more complex code this could have been a significant change.

So what?

In the good examples we had to make minimal changes.

In the bad examples we had to make more changes to more things.

And [code changes are error prone](/blog/why-code-changes-are-error-prone/).

This hardly matters when the example is so small and simple.

However complexity increases exponentially with scale.

At scale and with things that are complicated, even small changes can be difficult to make and error-prone. Even more so if we need to worry about a 100-line function instead of a 5-line one (because if we don't apply separation of concerns functions could easily become that long). Repetition increases the chance of mistakes too. We tend to be bad with repetitive work.

Consider if we had a function `generateAndDisplayAndEmailReport` which: generated a report, had the display functionality in the same function, and on top of that it also sent it as an email.

Making changes to that function would be pretty horrendous.

- Making any change to any one of those aspects could break the rest, because all the code is placed together, possibly intertwined with bits of each aspect all scattered throughout the function. We could make a logic error, or just miss a few of the times where, for example, the display functionality is used.
- There is more for us to read and understand before we could even start making changes. If we want to change the display functionality, we have to read the entire 100-line function. We have to do that because we need to ensure we've read and understood all of the display functionality, and also that we didn't miss any instances where it's used.
  We're also breaking the principle of least astonishment by having a more complicated function.
- It's very likely that there will be cascading changes, especially if we're using the function in multiple places. What would we do if fizzbuzz with the display logic included is currently used in 3 places, and we need to change the display logic in just one of those places? What if we need to write the result to a file instead? In short, there will be many cascading changes. We'll have to change fizzbuzz, and change 2 or 3 other things as well. In a real codebase, those 2 or 3 other things may produce their own cascading changes, and so on. The classic case where we wanted to make a simple change and had to change every file in the system.

**Testability and reusability**
All the points from the original examples still apply. The good examples are far easier to reuse and test than the bad examples.

Note that with the good examples we wouldn't have any of the problems outlined. We could make isolated changes with minimal effort, there would be 0 cascading changes, things would be very easy to test (or at least the easiest they could be) and maximally re-usable.

### React example

Another example, this time using React.

Notes:

- This will be more of a flow of my thoughts if I was to develop this, rather than a pre-made example and description.
- I think refactoring later is perfectly applicable here, especially with code that's very small.
- There is value in pragmatism here. Previously in the fizzbuzz example, in the first example I mentioned that I wouldn't extract the `console.log` statement into a separate function. It's probably just not worth it at a scale that small. If things are too small, there is no need to split them. However I think applying separation of concerns early is better than applying it later. We should apply separation of concerns quickly if we feel we should.

The scenario is quite simple, even if you don't know React.

It's about containers, presentational / view components in React, and React custom hooks too.

**Quick overview of React**

First a quick overview in case you don't know React.

React is primarily a view library. Its purpose is similar to other templating libraries. It allows us to display stuff on the DOM and is easy to write and develop with and such.

Of course before displaying stuff, we often need to get data first. In this example we'll just use application state. In a real application it could also be AJAX calls or other stuff.

I'll be talking in React terms but just understand that:

- When I say React I conceptually mean any view library.
- When I say "container" I mean something that gathers data to present to the view and perhaps sets up functionality. Kind of like a controller or presenter in MV\* architecture.
- React hooks are just what we use for certain logic in React.
- About React custom hooks, think of them as just an implementation detail in React to allow us to reuse containers or logic for multiple views. Kind of like a normal `import` statement but for react code.

**Scenario**

We'll just have a very simple counter.

A single button with a + icon and a number next to it. When we click the +, the number increases.

How should we structure things like views, containers and even React hooks?

I think the first thing we should always do is consider our requirements:

- We need to display a + button.
- We need to display a number.
- We need to hold some state about what number we're on.
- We need to increment the number when we click the + button.

In this case we won't challenge or ponder over any of the requirements or attempt to find the motivation behind them in case there are better solutions. We'll just accept the requirements as-is.

Okay, let's start simple.

**View**

Who will display the stuff?

Does it make sense to display both the + button and the number in the same component, or should we split stuff up? Are the things separate concerns, or do they belong together?

I think it makes sense to keep them together at this time. They are definitely related, and I don't think the code has enough concerns to warrant them being apart at this time.

Perhaps if we had 3 buttons in the future it would be easier to separate them. We could split into having a view component to display the three buttons and a separate view component to display the number. Even at that scale it might be a bit overkill, but at least this showcases some of the thoughts we may have in a scenario like this. Anyway, at this small scale let's display both of them in one view.

Okay so we've decided on the view.

What does the view care about? What does it want?

- It wants the number to display.
- It wants functionality to apply to the button.

What does the view not care about?

It does not care about handling the functionality to increment the value of the number. We could put it in the same component in an example this small, however especially in a more complicated component we would not want this logic in the view.

What I like to do is think of the view (or anything for that matter) as an entity, and think about what it would care about. The view just does not care about functionality and state. Like all our other code, the view wants to be lazy, selfish and ignorant. The view just wants to display stuff and do nothing else. It needs the number, but does not go out of its way to get it. It just lazily accepts it as a prop (argument in React). The buttons must also necessarily have functionality. Since the view doesn't want to provide functionality, we must also accept functions as a prop to attach to the buttons, so we do that too.

This is what our view might look like:

```js
// React setup code.
import React from 'react';

const CounterView = props => (
  <div>
    <div>Count: {props.count}</div>
    <button type="button" onClick={props.onIncrement}>
      +
    </button>
  </div>
);

export default CounterView;
```

**Benefits**

Why is it good to separate functionality (containers) and views like this?

- We can reuse this view anywhere in the codebase. We can even take it and put it in an NPM module and reuse it in other codebases. For example we may have some generic BlogPostView component we may want to use with different containers which obtain data in different ways.
- The code is smaller and has less concerns, making it easier and less error-prone to change.
- We can test it easily without mocking stuff or worrying about things like Redux, etc.

On the other hand, if it was coupled with logic, we would lose all the benefits:

- We wouldn't be able to reuse the view anywhere except in places that also required the exact same logic.
- The code would be larger and have multiple concerns, so it would be more difficult to change.
- Testing would still be fine because the component is simple enough, but would quickly need to become more elaborate with more complicated components.

**Logic**

Our view needs something to provide it with data and functionality.

Who should take that role?

Should it be the caller (the application, page, etc.)?

Probably not.

It's not the application's concern to worry about the details of how Counter works. It does not care. It wants to delegate it to something else.

All the application wants is to have a statement of `<Counter />`. In the worst case maybe it will pass in an initial number or something, such as `<Counter initialCount={10} />`, but nothing else.

Okay so we need a middle layer. We need what we refer to as a "container" in React. This layer will handle things to allow our application to just write `<Counter />`. Our container will create and pass everything the view requires.

Here is some example container code:

```js
import React, { useState } from 'react';
import CounterView from './CounterView';

const Container = ({ initialCount = 0 }) => {
  const [count, setCount] = useState(initialCount);
  const handleIncrement = () => setCount(count + 1);

  return <CounterView count={count} onIncrement={handleIncrement} />;
};

export default Container;
```

Don't worry too much about the details of the code. All it does is:

- Initialises count to be `initialCount` if the caller passes that as a prop, otherwise 0.
- Keeps track of the current value of count.
- Creates a handleIncrement function which handles the logic of incrementing the count value.
- Calls and renders the view, passing it everything it requires.

The problem remaining is that the view is hardcoded.

In the fizzbuzz example, we specifically examined how bundling the fizzbuzz calculation with how we display the result could cause problems.

Here the main problem is that we would not be able to reuse the container with different views.

A simple example why we may want to do that is to display blog posts in a different format. The blog post may have the same data, but we can use one of many views to display it differently in different scenarios.

Understanding the motivation is the important part. Having that, we can use React custom hooks, higher order components or render props to be able to reuse functionality easily.

## Sidenote on dependency injection

If you use dependency injection you may have thought that the examples given could be improved with it.

I agree. I think dependency injection is awesome, and you're welcome to use it.

Dependency injection allows us to test using spies instead of mocks, which feels like a nicer and more natural way to test. It also allows us to modify aspects of the program in a dedicated layer rather than changing hardcoded source code.

The reason I didn't use it in the examples is because it's not particularly popular in JavaScript and front end in general (except for Angular).

If you don't use dependency injection then don't worry too much about it. But feel free to check it out regardless and see if you like it.

## Summary - Why should we care about separation of concerns?

The examples given already cover this, so here is a brief summary.

With separation of concerns, we get the following benefits:

**Code becomes maximally re-usable**

If a function or method, class or module, only does one thing, it means it is maximally re-usable throughout the codebase.

Anything that needs that one thing can import and reuse the code.

However if it does two or more things, only code that requires both of those things can reuse the code.

We saw the example of fizzbuzz which also logs out results to the screen.

It's not possible to reuse the fizzbuzz functionality and, say, write it to the DOM. We can only re-use it if we need to calculate fizzbuzz and write it to the screen.

On the other hand, if fizzbuzz only calculates fizzbuzz, we can reuse that anywhere.

**Code becomes maximally testable**

Smaller functions are easier to test, particularly functions that are pure.

It's easier to test `fizzbuzz()` and `appendToTargetNode(target)` individually, rather than if they're bundled together.

**Code is smaller, does less things, and is easier to understand**

As required by the principle of least astonishment.

**Functionality can change with minimal changes and no cascading changes**

[Code changes are error prone](/blog/why-code-changes-are-error-prone/), so we want to minimise their scope and the amount of changes we have to make.

If we have clearly separated the display logic from the calculation logic, we can make independent changes to either one without worrying about affecting the other (assuming that the interface doesn't change).

On the other hand, if they're bundled together in the same function, attempting to make changes to one may break the other.

**Bonus: Code should read like well-written prose**

A result of following the principle of least astonishment with good naming, abstractions and separation of concerns.

## Theory

Finally, time for the theory promised in this series.

To recap, our requirements for software are:

- It should work as intended.
- It should be easy to change.

Our premises are:

- We can only be aware of minimal information.
- We must understand what we're doing.
- We must minimize propagating changes.
- Complexity increases exponentially with scale.

And so we have our motivation for separation of concerns.

**Minimal information and we must understand what we're doing**

Code with concerns properly separated results in code that is as small as possible. This means there is less for us to be aware of and the code is easier to understand.

Understanding is required for us to make changes. Also all things being equal, making changes on a smaller scope is easier than making changes on a larger scope.

**Abstraction**

It can potentially break the principle of abstraction, which long-story-short breaks our requirement that code should be easy to change. (For deeper information on this, please see the post on the dedicated principle.)

Abstraction says that there should be no duplicate code which is semantically the same.

If we don't follow separation of concerns, it means that our functions can do more than one thing.

Elsewhere in the codebase, we may require one of the things that one of our functions already does. However it may be bundled with additional functionality we cannot use.

It may not be possible to refactor the function doing multiple things into separate functions. We may not even bother considering refactoring if the function is in a bad enough state or if our entire codebase is like that. Writing things from scratch every time may even be the norm. Our entire codebase may be like that with duplicate functionality scattered throughout.

Here the [broken windows theory](https://en.wikipedia.org/wiki/Broken_windows_theory) mentioned in The Pragmatic Programmer comes to mind.

In this case we would need to create duplicate code yet again for the functionality we need.

This breaks the principle of abstraction, which breaks our requirement that code should be easy to change.

### Guidelines

- Keep the principle in mind. By keeping it in mind you'll probably always make progress towards it. It will definitely be better than if you're not aware of the principle in the first place.
- Ask yourself questions such as:
  - "What does this function care about?"
  - "If it was selfish, ignorant and lazy, what would it care / not care about?"
  - "What is its concern?"
  - "What does the caller care about?" "How does the caller want to call this function?" (This kind of thinking is also encouraged by TDD, where by doing TDD we're essentially thinking about the caller first and foremost.)
  - One from Robert Martin: "What reasons does this code have to change?"
  - "Who (which person in the company) may want to change this function / method, or class / module?" E.g. designers may want to change the style, that's a CSS concern. Accountants may want to change the generated report. Administrators may want to change where the report is displayed. All of these scenarios imply different concerns in the code.
- Make the code you write as selfish, ignorant and lazy as possible.
- Everything should only do the absolute minimum needed.
- Code should only be concerned with itself, and nothing else. E.g. not caring about who calls it, or the state of the system. Only about itself, the small thing it does and what it receives through arguments.
- [Extract till you drop](https://sites.google.com/site/unclebobconsultingllc/one-thing-extract-till-you-drop) technique (credits Robert Martin).

### Benefits

- Maximum re-usability.
- Maximum testability.
- Maximum changeability.

---

This article is part of the "Programming first principles series":

1. [Purpose - What this series is about](/blog/programming-first-principles-purpose-what-this-series-is-about/)
2. [Audience - Who this series is for](/blog/programming-first-principles-audience-who-this-series-is-for/)
3. [Requirements of software](/blog/programming-first-principles-requirements-of-software/)
4. [Premise - Minimal information](/blog/programming-first-principles-premise-minimal-information/)
5. [Premise - We must understand what we're doing](/blog/programming-first-principles-premise-we-must-understand-what-were-doing/)
6. [Premise - Minimize propagating changes throughout the system](/blog/programming-first-principles-premise-minimize-propagating-changes/)
7. [Premise - Complexity increases exponentially with scale](/blog/programming-first-principles-premise-complexity-increases-exponentially-with-scale/)
8. [First principle - Proof that code works](/blog/programming-first-principles-first-principle-proof-that-code-works/)
9. [First principle - Principle of least astonishment](/blog/programming-first-principles-first-principle-principle-of-least-astonishment/)
10. [First principle - Principle of least knowledge](/blog/programming-first-principles-first-principle-principle-of-least-knowledge/)
11. [First principle - Separation of concerns](/blog/programming-first-principles-first-principle-separation-of-concerns/) (this article)
12. [First principle - Abstraction](/blog/programming-first-principles-first-principle-abstraction/)
13. [Side effects](/blog/programming-first-principles-side-effects/)

Also suggested:

- [When not to apply programming principles](/blog/when-not-to-apply-programming-principles/)
- [Why code changes are error prone](/blog/why-code-changes-are-error-prone/)
