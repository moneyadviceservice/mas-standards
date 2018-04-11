# Money Advice Service Cucumber Testing Guide

## Table of Contents

  1. [Tools](#tools)
  2. [Directory structure](#directory)
  3. [Guidelines](#guidelines)
  4. [Sample files](#examples)
  5. [Running tests](#running)
  6. [Resources](#resources)

## Tools
This guide focuses on cucumber for [Javascript](https://github.com/cucumber/cucumber-js), but we can also make use of the [PHP](https://github.com/cucumber/cucumber/wiki/PHP) and [Rails](https://github.com/cucumber/cucumber-rails) implementations depending on the project at hand.

### Installing Cucumber.js
First make sure you have an up-to-date node environment. At the moment, one of the easiest ways is to use the [n](https://www.npmjs.com/package/n) package.

Install the n package:
`npm install -g n`

Get the latest stable version of node:
`n install latest`

Make sure you're using the latest version of npm:
`npm upgrade -g npm`

An easy setup to run cucumber tests is via [chimp.js](https://chimp.readme.io/). This handy framework combines [webdriverio](http://webdriver.io/), cucumber and other testing tools in an easy to use package.

Install chimp with via:
`npm install -g chimp`

## Directory structure

```
.
+-- features
|   +-- step_definitions
|     step1-definitions.coffee
|     step2-definitions.coffee
|   feature-name1.feature
|   feature-name2.feature
|   feature-name3.feature
```

### Feature files
These files are written in [Gherkin](https://github.com/cucumber/cucumber/wiki/Gherkin) and drive the automated tests by specifying scenarios under which we test the feature in question.

### Step definition files
These are coffeescript files than contain the individual steps that make up an scenario in a feature file. They allow the extraction of parameters from the feature file using regular expressions.

### Running tests
You can run all tests with `chimp --browser=phantomjs --compiler=coffee:coffee-script/register --path tests/features`. If you install chimp as a local package you can add it to the `test` section of your `package.json` file:

```json
  "scripts": {
    "test": "./node_modules/.bin/chimp --browser=phantomjs --compiler=coffee:coffee-script/register --path tests/features"
  }
```

Then you can run all tests easily with `npm test`.

## Guidelines
Adapted from [Engine Yard's](https://blog.engineyard.com/2009/15-expert-tips-for-using-cucumber)

### Features

Feature Files Should Actually be Features, Not Entire Portions of an App

Group your feature files by a business object, then action, and a context (if applicable). You can put all feature files in the same directory, e.g.:

```
article-navigation.feature
comment-post.feature
user-login.feature
```

Keep the file organized by grouping the steps with Given / When / Then / And.


**RULES**:
* Group feature files by an application domain, not by an actor.
* 1 Feature = 1 File

### Scenarios

Adding the details to a lower layer of implementation makes it is easier to write declarative scenarios, i.e.

```cucumber
Given the user navigates to the home page
And enters "username" in the "username" field
And enters "password" in the "password" field
When the user clicks on the login button
Then the user is logged in
```

could be written as

```cucumber
Given the user is logged in
And is on the home page
```

Using declarative style helps both the person writing and maintaining the tests in the long run as well as the product owner to see the forest from the trees, i.e. the acceptance criteria of the feature instead of a huge number of specific actions the user needs to take to accomplish the task.

There shouldn’t be any sort of coupling between scenarios. The main source of such coupling is the state that persists between scenarios. This can be done accidentally, or even worse, intentionally. For example, one scenario could step through adding a record to a database, while the subsequent scenarios depend on the existence of that record.

This may work, but will create a problem if the order in which scenarios run changes, or they are run in parallel. Scenarios need to be completely independent.

Each time a scenario runs, it should run the same, giving identical results. The purpose of a scenario is to describe how your system works. If you don’t have confidence that this is always the case, then it does not do its job. If you have non-deterministic scenarios, find out why and fix them.


If you use the same steps at the beginning of all scenarios for a feature, put them into the feature’s Background. Background steps are run before each scenario. Avoid putting too many steps there since your scenarios might become difficult to understand.

Also, avoid unnecessary requests (and thus improve test performance) by grouping steps unless there's a change of state.

For example:

```cucumber
  Scenario: The page shows a complete list of videos
    Given I am visiting http://whitelines.com/videos
    Then I should be able to see a list of videos
    And I can read the title of each video
    And I can read the excerpt of each video
    And I can see thumbnails of the videos
    And I should be able to see a pager
```

Instead of:
```cucumber
  Scenario: The page shows a list of videos
    Given I am visiting http://whitelines.com/videos
    Then I should be able to see a list of videos

  Scenario: Videos show related text
    Given I am visiting http://whitelines.com/videos
    And I can read the title of each video
    And I can read the excerpt of each video
    And I can see thumbnails of the videos

  Scenario: The page has a pager
    Given I am visiting http://whitelines.com/videos
    And I should be able to see a pager
```


**RULES**:
* Use a declarative style.
* Make scenarios independent and deterministic.
* Use backgrounds wisely.

### Steps

Steps must be clear and understandable (simply by reading the description).

Each step has to perform one action. Avoid using “and” in the step patterns, e.g.:

```cucumber
Given A and B
```

Instead, use two steps:

```cucumber
Given A
And B
```

The matched phrases must not necessarily   be enclosed in the double quotes. Often quotes are a good idea as they make it visually clear which phrases will be passed to the step definition, e.g.:

  Given I have "potatoes" in my cart

That’s reasonable. The quotes are meant to highlight the parts that are changed without hurting readability. But sometimes, quotes are just a poor style:

  Given I have "7" items in my cart

It should be pretty obvious that the number is a variable. The quotes add nothing but the noise. A good example of a step is:

```javascript
  @Then /^I have (\d+) items in my cart$/, (count, callback) ->
    ...
```

**RULES**:
* The step description must contain neither regexen, CSS or XPath selectors, nor any kind of code or data structure.
* Avoid using conjunctive steps.
* Anything that is literally used must be in double quotes, otherwise, it should be specified within the sentence.


## Step Definitions

You can create non-capturing groups by adding a “?:” to the beginning of an otherwise normal group (e.g. (?:some text) rather than (some text)). This is treated exactly as a normal group except that the result is not captured and thus not passed as an argument to your step definition. This might be useful in conjunction with the alternation:

```javascript
  @Then /^once the files? (?:have|has) finished processing$/, (callback) ->
    ...
```

**RULES**:
* Use flexible pluralization.
* Use non-capturing groups to ensure steps are read naturally.
* Simplify step definitions.

## Examples

### Sample feature file
Here's a sample `.feature` file for the video list page:

```cucumber
Feature: Videos list page
  As a user of one of our core sites
  I want to see a nice list of available videos

  Scenario: The page shows a complete list of videos
    Given I am visiting http://whitelines.com/videos
    Then I should be able to see a list of videos
    And I can read the title of each video
    And I can read the excerpt of each video
    And I can see thumbnails of the videos
    And I should be able to see a pager

  Scenario: The pager buttons work
    Given I am visiting http://whitelines.com/videos
    And I click on one of the pager buttons
    Then I should land on that page
```

### Sample step definitions file
Here are some of the steps used in the previous feature. Please note you must always return an invocation to the callback function in order for the step to finish and the next step on the scenario can start. Also note, that operations like click, visit, pressButton, etc provide a callback function from which you can invoke the scenario callback and end the step asynchronously.

```cucumber
assert = require 'assert'

module.exports = ->

  @Given /^I landed on the first video in (.*)$/, (url, callback) ->
    browser.url url
    browser.click '.o-cards .c-card__wrapper:nth-child(2) .c-card__link'
    callback()

  @Then /^I should be able to see a list of videos$/, (callback) ->
    elements = browser.elements '.c-card--videos'
    expect(elements.value.length).toBeGreaterThan 8
    callback()

  @Then /^I can see thumbnails of the videos$/, (callback) ->
    elements = browser.elements '.c-card--videos .c-card__media-wrapper img'
    expect(elements.value.length).toBeGreaterThan 8
    callback()
```

### Hints for tricky tests

#### Switch to regular browser
You can easily change the browser that runs the steps from phantom.js to firefox or chrome with:
```
chimp --browser=chrome --compiler=coffee:coffee-script/register --path tests/features
```

#### Get log messages
If you a test is giving you a hard time (specially with phantomjs) you can dump all console messages within phantom from your step deffinitions file to the node standard output like this:

```js
@After ->
  logs = browser.log('browser')
  if logs.value.length
  console.log(logs.value)
```

#### Taking screenshots
You can also take screenshots of what the browser is doing, specially handy with phantomjs. You can accomplish this via `browser.saveScreenshot 'screenshot.png'` in your step definition files.


## Resources
[The Cucumber book in Safari Books (login in passpack)](https://www.safaribooksonline.com/library/view/the-cucumber-book/9781941222911/)

[Neal Ford on Agile Engineering Practices - Functional Tests](https://www.safaribooksonline.com/library/view/neal-ford-on/9781449314439/part08.html)

[15 Expert Tips for Using Cucumber](https://blog.engineyard.com/2009/15-expert-tips-for-using-cucumber)

[Cucumber Documentation Overview](https://cucumber.io/docs)