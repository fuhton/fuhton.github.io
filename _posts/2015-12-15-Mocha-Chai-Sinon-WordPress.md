---
layout: post
title: Mocha, Chai, Sinon, WordPress
---

There’s not a lot of documentation for using Mocha, Chai, and Sinon to test prototyping. I spent a good chunk of time debugging the function and trying to code around the issue, but I eventually came to the same conclusion that I needed to test it as is. Creating a prototype to use inside of the function was necessary for how the rest of the application and since I had put off writing unit tests for this block of code, I had to use the tools I had already invested in – Mocha, Chai, and Sinon ( for stubbing ). I looked at a few other tools similar to SinonJS like JackJS but a lack of documentation and current bugs in the code made that un-useable (as of Sept, 01, 2015).

* MochaJS - our base framework
* ChaiJS - our assertion library
* SinonJS and Sinon-ChaiJS - for mocking

In our beforeEach method, we want to setup our stubbing :
```
beforeEach( function() {
    this.view = new View();
    // Other initialization code
    this.sandbox = sinon.sandbox.create();
});
```
For prototyping the prototype call :
```
it( '<NAME OF TEST>', function() {
    this.sandbox.stub( TemplateHelper.prototype, 'render');
    this.view.render();
    expect( this.view.templateHelper.render.callCount ).to.equal(1);
});
```
As you might have guessed, our actual file might look something like this :
```
View = Backbone.View.extend({
     render : function() {
          this.templateHelper = new TemplateHelper();
          this.templateHelper.render();
          return this;
     },
});
```
For me, this worked perfectly. If you have any ideas of an easier way to test prototype initialization and method calls, I’m all ears.
