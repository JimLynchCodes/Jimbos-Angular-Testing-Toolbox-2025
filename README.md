# Jimbos-Angular-Testing-Toolbox-2025
Some stuff for springs winning! 🥇

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

## Thinking About Code Coverage

Test coverage should not be a pointless competition that puts developers under pressure and shames those that do not meet an arbitrary mark. Measuring coverage is a tool you should use for your benefit. Keep in mind that writing meaningful, spot-on tests does not necessarily increase the coverage score.

For beginners and experts alike, the coverage report helps to set up, debug and improve their tests. For experienced developers, the score helps to keep up a steady testing practice.

Code coverage shows you _where you are clearly missing unit tests,_ but it does not necessarily mean your assertions are good!

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

## Testing Component Inputs

Don't forget that you need to manually call `ngOnChanges` in unit tests!

```
describe('CounterComponent', () => {
  let component: CounterComponent;
  let fixture: ComponentFixture<CounterComponent>;

  const startCount = 123;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [CounterComponent],
    }).compileComponents();

    fixture = TestBed.createComponent(CounterComponent);
    component = fixture.componentInstance;
    component.startCount = startCount;
    // Call ngOnChanges, then re-render
    component.ngOnChanges();
    fixture.detectChanges();
  });
```

## Presentational Components
Low-level Components that render plain HTML elements but no child Components are called ___presentational components___.

- They directly render what the user sees and interacts with.
- They are often highly generic and reusable.
- They are controlled through Inputs and report back using Outputs.
- They have little to none dependencies.
- They are easy to reason about and therefore easy to test.
- The preferred way of testing them is a unit test.

These Components are called presentational Components since they directly present a part of the user interface using HTML and CSS. Presentational Components need to be combined and wired to form a working user interface.


## Container Components
This is the duty of container Components. These high-level Components bring multiple low-level Components together. They pull data from different sources, like Services and state managers, and distribute it to their children.

Container Components have several types of dependencies. They depend on the nested child Components, but also Injectables. These are classes, functions, objects, etc. provided via dependency injection, like Services. These dependencies make testing container Components complicated.

## Shallow vs. deep rendering

There are two fundamental ways to test Components with children:

A unit test using shallow rendering. The child Components are not rendered.
An integration test using deep rendering. The child Components are rendered.

Each works better in different situations.

tldr - Shallow rendering is good for testing _container components_.

<br/>

## Faking Services

With dependency injection, we can easily provide a fake service to a component or even a fake service as a dependency of another service.

Faking guidelines:

There are two guidelines that may help you:

1) Is the test valuable?
2) Does it cover the important interaction between Component and Service?

Decide whether to test the interaction superficially or in-depth.

Whichever approach you choose, make sure to meet the basic requirements:

- Equivalence of fake and original: The fake must have a type derived from the original.

- Effective faking: the original stays untouched.”

<br/>

## Form Accessibility with pa11y

The tool _pa11y_ is nice for testing accessibiliity of forms.

<br/>

## Using Spectator

[Spectator](https://github.com/ngneat/spectator) is an opinionated library for testing Angular applications. Technically, it sits on top of TestBed, ComponentFixture and DebugElement. But the main idea is to unify all these APIs in one consistent, powerful and user-friendly interface – the Spectator object.
Spectator simplifies testing Components, Services, Directives, Pipes, routing and HTTP communication. Spectator’s strength are Component tests with Inputs, Outputs, children, event handling, Service dependencies and more.

<br/>

## Testing Services

- Writing tests for Services with internal state
- Testing Observables returned by Services
- Verifying HTTP requests and payload processing
- Covering HTTP success and error cases

In an Angular application, Services are responsible for fetching, storing and processing data. Services are singletons, meaning there is only one instance of a Service during runtime. They are fit for central data storage, HTTP and WebSocket communication as well as data validation.

The single Service instance is shared among Components and other application parts. Therefore, a Service is used when Components that are not parent and child need to communicate with each other and exchange data.

<br/>

## Common Service Tests

So what does a Service do and how do we test it? Services are diverse, but some patterns are widespread.

- Services can have public methods that return values.
In the test, we check whether a method returns correct data.

- Services can store data. They hold an internal state. We can get or set the state.
In the test, we check whether the state is changed correctly. Since the state should be held in private properties, we cannot access the state directly. We test the state change by calling public methods. We should not peek into the black box.

- Services can interact with dependencies. These are often other Services. For example, a Service might send HTTP requests via Angular’s HttpClient.
In the unit test, we replace the dependency with a fake that returns canned responses.

<br/>

## Testing a Service that sends HTTP requests

We have used the HttpClientTestingModule for testing a Service that depends on HttpClient

<br/>

## Pipe Test Example

Let us study the structure of a Pipe first to find ways to test it. In essence, a Pipe is class with a public transform method. Here is a simple Pipe that expects a name and greets the user.
```
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({ name: 'greet' })
export class GreetPipe implements PipeTransform {
  transform(name: string): string {
    return `Hello, ${name}!`;
  }
}
```
In a Component template, we transform a value using the Pipe:
`{{ 'Julie' | greet }}`

The GreetPipe take the string 'Julie' and computes a new string, 'Hello, Julie!'.

```
“describe('GreetPipe', () => {
  let greetPipe: GreetPipe;

  beforeEach(() => {
    greetPipe = new GreetPipe();
  });

  it('says Hello', () => {
    expect(greetPipe.transform('Julie')).toBe('Hello, Julie!');
  });
});
```

<br/>

## Testing Attribute Directives

An Attribute Directive adds logic to an existing host element in the DOM. Examples for built-in Attribute Directives are NgClass and NgStyle.

<br/>

## Testing Structural Directives

A Structural Directive alters the structure of the DOM, meaning it adds and removes elements programmatically. Examples for built-in Structural Directives are NgIf, NgFor and NgSwitch.

<br/>

# Strengths of end-to-end tests

As discussed in distribution of testing efforts, all types of automated tests have pros and cons. Unit and integration tests are fast and reliable, but do not guarantee a working application. End-to-end test are slow and often fail incorrectly, but they assess the fitness of the application as a whole.

<br/>

# Deterministic Environment
The database needs to be filled with pre-fabricated fake data. With each run of the end-to-end tests, you need to reset the database to a defined initial state.
The back-end services need to answer requests with deterministic responses. Third-party dependencies need to be set up so they return realistic data but do not compromise production data.

<br/>

# Common E2e Test Webdriver Commands

- Navigate to a given URL
- Find one or more elements in the DOM
- Get information about a found element:
  - Get an element attribute or property
  - Get the element’s text content
- Click on an element
- Send keyboard input to a form field
- Execute arbitrary JavaScript code

<br/>

# Cypress

Since Angular v12 Protractor has been deprecated, and many devs recommend using Cypress for e2e testing of today's Angular applications.

Can be easily installed to an Angular project with the cypress schematic:
```
ng add @cypress/schematic
```

Example cypress test:
```
describe('Counter', () => {
  beforeEach(() => {
    cy.visit('/');
  });

  it('has the correct title', () => {
    cy.title().should('equal', 'Angular Workshop: Counters');
  });
});
```

# Page Objects

A nice pattern for reducing duplication of "DOM element finding code" with higher level abstractions.

<br/>

<br/>

# Resources

- [Testing Angular – A Guide to Robust Angular Applications
Mathias Schäfer (molily)](https://itunes.apple.com/WebObjects/MZStore.woa/wa/viewBook?id=0)

- [Angular Testing Succinctly - Unit Testing](https://www.syncfusion.com/succinctly-free-ebooks/angular-testing-succinctly/unit-testing)

