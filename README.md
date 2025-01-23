# Jimbos-Angular-Testing-Toolbox-2025
Some stuff for springs winning

<br/>

# Why Write Tests?

Throughout my career, I have found it _extremely useful_, and as your codebase scales up- _necessarry_, to have automated tests.

When deploying code, it is imperitive that it works. But not just that it works- that you ___know___ that it works.

When you are a junior or mid level engineer you are not expected to have well tested code.

Even for many senior engineers, the high level of discipline is not always there.

The business gets axious, people cut corners, arbitraty deadlines approach...

But now, I am in somewhat of a leadership position on a new team.

I'm inheriting an Angular project that already has high profile clients using it in production.

It's my job to take owership of this platform, building it back up from a legacy codebase to one we can all happily open up and enjoy together like it's a warm bublling hot tub. 

I need to lead by example, and show others the way to deploying with confidence, and my secret weapon is having a strong suite of tests.

<br/>

# Testing Pyramid

Martin Fowler's testing pyramind:

<br/>

# Interesting Unit Testing Setups

Angular provides solid testing tools out of the box.

The Angular team already made decisions on using testing tooling for us: Jasmine as testing framework and Karma as test runner.

Application parts are typically tested inside Angular’s TestBed.


Althoug, some Angular developers use Jest instead of Jasmine and Karma. Some use Spectator or the Angular Testing Library instead of using TestBed directly.
These alternatives are not better or worse, they simply make different trade-offs. This guide uses Jasmine and Karma for unit and integration tests. Later, you will learn about Spectator.

Once you have reached the limits of a particular setup, you should investigate whether alternatives make testing your application easier, faster and more reliable.

<br/>

# Running Tests

Usually just with ng test, which runs all files ending in _".spec.ts"_
```
ng test
```

<br/>

# Red - Green - Yellow

This is the cycle you should be following, and working with faster / smaller / more loops around the cycle is usually better.

<br/>

# Launchers & Plugins

In order to make things work properly for testing you will usually need to configure plugins in a way similar to this:
```
“plugins: [
  require('karma-jasmine'),
  require('karma-chrome-launcher'),
  require('karma-jasmine-html-reporter'),
  require('karma-coverage'),
  require('@angular-devkit/build-angular/plugins/karma')
],”
```

<br/>

# Code Coverage

Add the code coverage flag when running ng test:
```
ng test --code-coverage
```

<br/>

# Structure of a Test (The Three A's)

We can craft well-made unit tests my covering the 3 A's in each test: Arrange, Act, and Assert.

1) ___Arrange___ is the preparation and setup phase. For example, the class under test is instantiated. Dependencies are set up. Spies and fakes are created.

2) ___Act___ is the phase where interaction with the code under test happens. For example, a method is called or an HTML element in the DOM is clicked.

3) ___Assert___ is the phase where the code behavior is checked and verified. For example, the actual output is compared to the expected output.

<br/>

# Before And After Hooks

When writing multiple specs in one suite, you quickly realize that the _Arrange_ phase is similar or even identical across these specs. 

When the setup is repeated over and over, it should be defined once in a central place. 

You _could_ write a setup function and call it at the beginning of each spec, but, using Jasmine, you can declare code that is called before and after each spec, or before and after all specs.

For this purpose, Jasmine provides four functions: **beforeEach**, **afterEach**, **beforeAll** and **afterAll**. 

They are called inside of a describe block, just like it.

<br/>

# Running A Single Test File

This can really improve test completion speed, especially for large projects.

```
ng test --include **/counter.component.spec.ts
```

<br/>

# Testing Angular Components

Components are the core building blocks of single page application. Components are composed to form the user interface.

A Component deals with several concerns, among others:

- It renders the template into the HTML DOM.
- It accepts data from parent Components using Input properties.
- It emits data to parent Components using Outputs.
- It reacts to user input by registering event handlers.
- It renders the content (ng-content) and templates (ng-template) that are passed.
- It binds data to form controls and allows the user to edit the data.
- It talks to Services or other state managers.
- It uses routing information like the current URL and URL parameters.”

<br/>

# The data-testid Attribute

Type and class selectors introduce a tight coupling between the test and the template. HTML elements are picked for semantic reasons. Classes are picked mostly for styling. Both change frequently when the Component template is refactored. Should the test fail if the element type or class changes?
Sometimes the element type and the class are crucial for the feature under test. But most of the time, they are not relevant for the feature. The test should better find the element by a feature that never changes and that bears no additional meaning: test ids.
Test ids

A test id is an identifier given to an element just for the purpose of finding it in a test. The test will still find the element if the element type or unrelated attributes change.

Mathias's preferred way (which I actually like too) is to mark an HTML element is a data attribute. In contrast to element types, class or id attributes, data attributes do not come with any predefined meaning. Data attributes never clash with each other.

For example, we mark the increment button in the CounterComponent with data-testid="increment-button":
```
<button (click)="increment()" data-testid="increment-button">+</button>
```

In the test, we use the corresponding attribute selector:
```
const incrementButton = debugElement.query(
  By.css('[data-testid="increment-button"]')
);
```
<br/>

# Triggering event handlers

DebugElement has a useful method for firing events: triggerEventHandler. This method calls all event handlers for a given event type like click. As a second parameter, it expects a fake event object that is passed to the handlers:
```
incrementButton.triggerEventHandler('click', {
  /* … Event properties … */
});
```

This example fires a click event on the increment button. Since the template contains (click)="increment()", the increment method of CounterComponent will be called.

<br/>

## Detecting Changes In The DOM

Angular tests do not automatically detect changes and update the DOM. Clicking the increment button changes the count property of the Component instance. To update the template binding {{ count }}, we need to trigger the change detection manually:
```
fixture.detectChanges();
```

<br/>



# Resources

- [Testing Angular – A Guide to Robust Angular Applications
Mathias Schäfer (molily)](https://itunes.apple.com/WebObjects/MZStore.woa/wa/viewBook?id=0)
