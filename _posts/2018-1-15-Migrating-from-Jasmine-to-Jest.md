---
layout: post
title: Migrating from Jasmine to Jest
---

Since the current application I help build runs about >1200 (and growing) Jasmine unit-tests, I figured [migrating to Jest](https://facebook.github.io/jest/docs/en/migration-guide.html) would be a pretty painless process. Snapshot testing was (and very much still is) a missing part of  testing stack and was the catalyst for this migration. While creating simple snapshots was super [easy](http://facebook.github.io/jest/docs/en/snapshot-testing.html) and I'm excited to snapshot everything more, what I uncovered about our Jasmine setup was the more interesting issue.

Our 1200+ Jasmine tests were running in about ~1.5-2 seconds. I didn't think much about that, but writing this now it's an obvious red flag. Little did I know, but we were also skipping about 50-75 tests (~25 of which were failing) and Jasmine wasn't picking up on this. I boiled the problem down to a memory leak we designed into some utilities within our system that Jasmine wasn't able to handle, involving some of our ES6 features and some of our memoization. While not ideal, I knew there was a problem with our Jasmine setup, but not with Jest, so I left it at that and pursued the migration to Jest.

## Switching the test command

The first step was adding an additional test commend. The existing test command was `"test": "babel-node jasmine.config.js"`, so we could run our ES6 and babel plugins over our source files. Dropping in Jest was pretty seamless - `"test": "jest"`.

`jasmine.config.js`
```javascript
import Jasmine from 'jasmine';

const jasmine = new Jasmine();
jasmine.loadConfig({
  helpers: [
    '../testUtils/helpers.js',
  ],
  random: false,
  spec_dir: 'frontend/js',
  spec_files: [
    '**/*[sS]pec.js',
  ],
  stopSpecOnExpectationFailure: true,
  throwFailures: true,
});
jasmine.execute();
```
`jest.config.js`
```javascript
module.exports = {
  clearMocks: true,
  setupFiles: ['./frontend/testUtils/helpers.js'],
  testMatch: ['frontend/js/**/?(*.)(spec|snap).js?(x)'],
  verbose: false,
};
```

This was an easy part of the migration.

## Additional needs for global Setup

An unexpected find was that we needed additional global setup for Jest, but I narrowed this down to existing Jasmine tests, that required requiring additional globals, not being run properly. I'm not satisfied that we uncovered everything (or the perfect setup) for our missing globals, but what I had to add is below.

`frontend/testUtils/helpers.js`
```javascript
require('babel-polyfill');

const jquery = require('jquery');
const jsdom = require('jsdom').jsdom;

global.window = jsdom().parentWindow;
// Make a global instance of jQuery available for tests
global.$ = jquery;

+ global.Cookies = {
+   value_: '',
+
+   get() {
+     return this.value_;
+   },
+
+   set(value) {
+     this.value_ += `${value};`;
+   },
+ };
+ window.location.assign = jest.fn();
+ window.history.pushState = jest.fn();
+ window.history.push = jest.fn();
```

## Spying and mocking

You'll notice the `jest.fn();` additions in the above the snippet. Jest offers really powerful mocking that simplify setup and make clear any expectations about output. The [mocking docs](https://facebook.github.io/jest/docs/en/mock-function-api.html) give a bunch of really great examples that helped me reach most of the implementations I have below

```
...
import FooClass from './foo;
describe('MockFoo', () => {
  beforeEach(() => {
    makeFooClass = jasmine.createSpyObj('fooClass', ['isFoo', 'getFooName']);
  });
  it("should check isFoo and check getFooName", () => {
    ...
    makeFooClass.isFoo.and.returnValue(true);
    makeFooClass.getFooName.and.returnValue('bar');
    ...
    expect(makeFooClass.getFooName).toHaveBeenCalled();
    expect(makeFooClass.isFoo).toHaveBeenCalledWith('...');
  });
});
```

Changed to

```
...
jest.mock('./foo');
import FooClass from './foo;
describe('MockFoo', () => {
  it("should check isFoo and check getFooName", () => {
    ...
    const getFooName = jest.fn().mockReturnValue('bar');
    const isFoo = jest.fn().mockReturnValue(true);
    const makeFooClass = FooClass.mockImplementation(() => ({
      getFooName,
      isFoo,
    });
    ...
    expect(getFooName).toHaveBeenCalled();
    expect(isFoo).toHaveBeenCalledWith('...');
  });
});
```

## After

With the setup and mocking adjustments, the only roadblock was the before-mentioned missing/failing tests. These changes made me 100% more confident in our passing tests and convinced these changes were necessary.

## Should you use Jest?

I'd encourage you to really dive into what your needs are and determine for yourself if Jest is the right tool. Finding missing/skipped tests was really scary, but a really great find. That made justifying time spent doing the migration very easy to discuss.

The below lists were helpful overviews of Jest features and other's discussions/use cases. [Let me know if you have others or if something doesn't make sense!](https://twitter.com/fuhton)

#### Features
* [Snapshot testing](http://facebook.github.io/jest/docs/en/snapshot-testing.html)
* Great, powerful mocking, right out of [the box](https://facebook.github.io/jest/docs/en/manual-mocks.html)
* Has a built in [code-coverage tool](https://istanbul.js.org/docs/tutorials/jest/) and a great default reporter
* Parallelization - can run a configurable number of tests (3 by default) at a given time.

#### Links
* [Blog post about Jest Best Practicies](http://facebook.github.io/jest/blog/2016/03/11/javascript-unit-testing-performance.html)
* [Kent C Dodds migrating to Jest ](https://blog.kentcdodds.com/migrating-to-jest-881f75366e7e)
* [A comparison of different testing frameworks](https://medium.com/powtoon-engineering/a-complete-guide-to-testing-javascript-in-2017-a217b4cd5a2a)
