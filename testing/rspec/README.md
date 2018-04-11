# Money Advice Service RSpec Guide

Inspired by [Better Specs](http://betterspecs.org/)

## Table of Contents

  1. [How to describe your methods](#how-to-describe-your-methods)
  1. [Use contexts](#use-contexts)
  1. [Keep your description short](#keep-your-description-short)
  1. [Single expectation test](#single-expectation-test)
  1. [Test all possible cases](#test-all-possible-cases)
  1. [Expect vs Should syntax](#expect-vs-should-syntax)
  1. [Use subject](#use-subject)
  1. [Use let and let!](#use-let-and-let)
  1. [Mock or not to mock](#mock-or-not-to-mock)
  1. [Create only the data you need](#create-only-the-data-you-need)
  1. [Use factories and not fixtures](#use-factories-and-not-fixtures)
  1. [Easy to read matcher](#easy-to-read-matcher)
  1. [Shared Examples](#shared-examples)
  1. [Test what you see](#test-what-you-see)
  1. [Don't use should](#dont-use-should)
  1. [Automatic tests with guard](#automatic-tests-with-guard)
  1. [Stubbing HTTP requests](#stubbing-http-requests)
  1. [Useful formatter](#useful-formatter)

## How to describe your methods

  - [1.](#1.) <a name='1.'></a> Be clear about what method you are describing. For instance, use the Ruby documentation convention of `.` (or `::`) when referring to a class method's name and # when referring to an instance method's name.

    ```ruby
    # bad
    describe 'the authenticate method for User' do
    describe 'if the user is an admin' do

    # good
    describe '.authenticate' do
    describe '#admin?' do
    ```

**[⬆ back to top](#table-of-contents)**

## Use contexts

  - [2.](#2.) <a name='2.'></a> Contexts are a powerful method to make your tests clear and well organized. In the long term this practice will keep tests easy to read.

    ```ruby
    # bad
    it 'has 200 status code if logged in' do
      expect(response).to respond_with 200
    end
    it 'has 401 status code if not logged in' do
      expect(response).to respond_with 401
    end

    # good
    context 'when logged in' do
      it { is_expected.to respond_with 200 }
    end
    context 'when logged out' do
      it { is_expected.to respond_with 401 }
    end
    ```

  When describing a context, start its description with `when` or `with`.

**[⬆ back to top](#table-of-contents)**

## Keep your description short

  - [3.](#3.) <a name='3.'></a> A spec description should never be longer than 40 characters. If this happens you should split it using a context.

    ```ruby
    # bad
    it 'has 422 status code if an unexpected params will be added' do

    # good
    context 'when not valid' do
      it { is_expected.to respond_with 422 }
    end
    ```

  In the example we removed the description related to the status code, which has been replaced by the expectation `it { is_expected.to respond_with 422 }`. If you run this test typing `rspec` filename you will obtain a readable output.

    ```
    # formatted output
    when not valid
      it should respond with 422
    ```

**[⬆ back to top](#table-of-contents)**

## Single expectation test

  - [4.](#4.) <a name='4.'></a> The 'one expectation' tip is more broadly expressed as 'each test should make only one assertion'. This helps you on finding possible errors, going directly to the failing test, and to make your code readable.

  In isolated unit specs, you want each example to specify one (and only one) behavior. Multiple expectations in the same example are a signal that you may be specifying multiple behaviors.

  Anyway, in tests that are not isolated (e.g. ones that integrate with a DB, an external webservice, or end-to-end-tests), you take a massive performance hit to do the same setup over and over again, just to set a different expectation in each test. In these sorts of slower tests, I think it's fine to specify more than one isolated behavior.

    ```ruby
    # good (isolated)
    it { is_expected.to respond_with_content_type(:json) }
    it { is_expected.to assign_to(:resource) }

    # good (not isolated)
    it 'creates a resource' do
      expect(response).to respond_with_content_type(:json)
      expect(response).to assign_to(:resource)
    end
    ```

  When describing a context, start its description with `when` or `with`.

**[⬆ back to top](#table-of-contents)**

## Test all possible cases

  - [5.](#5.) <a name='5.'></a> Testing is a good practice, but if you do not test the edge cases, it will not be useful. Test valid, edge and invalid case. For example, consider the following action.
    ```ruby
    # destroy action
    before_filter :find_owned_resources
    before_filter :find_resource

    def destroy
      render 'show'
      @consumption.destroy
    end
    ```

    The error I usually see lies in testing only whether the resource has been removed. But there are at least two edge cases: when the resource is not found and when it's not owned. As a rule of thumb think of all the possible inputs and test them.

    ```ruby
    # bad
    it 'shows the resource'

    # good
    describe '#destroy' do

      context 'when resource is found' do
        it 'responds with 200'
        it 'shows the resource'
      end

      context 'when resource is not found' do
        it 'responds with 404'
      end

      context 'when resource is not owned' do
        it 'responds with 404'
      end
    end
    ```

**[⬆ back to top](#table-of-contents)**

## Expect vs Should syntax

  - [6.](#6.) <a name='6.'></a> On new projects always use the `expect` syntax.

    ```ruby
    # bad
    it 'creates a resource' do
      response.should respond_with_content_type(:json)
    end

    # good
    it 'creates a resource' do
      expect(response).to respond_with_content_type(:json)
    end
    ```

    Configure the Rspec to only accept the new syntax on new projects, to avoid having the 2 syntax all over the place.

    ```ruby
    # good
    # spec_helper.rb
    RSpec.configure do |config|
      # ...
      config.expect_with :rspec do |c|
        c.syntax = :expect
      end
    end
    ```

    On one line expectations or with implicit subject we should use `is_expected.to`.

    ```ruby
    # bad
    context 'when not valid' do
      it { should respond_with 422 }
    end

    # good
    context 'when not valid' do
      it { is_expected.to respond_with 422 }
    end
    ```

    On old projects you can use the [transpec](https://github.com/yujinakayama/transpec) to convert them to the new syntax.

    More about the new Rspec expectation syntax can be found [here](http://rspec.info/blog/2012/06/rspecs-new-expectation-syntax/) and [here](http://rspec.info/blog/2013/07/the-plan-for-rspec-3/#what_about_the_old_expectationmock_syntax).

**[⬆ back to top](#table-of-contents)**

## Use subject

  - [7.](#7.) <a name='7.'></a> If you have several tests related to the same subject use `subject{}` to DRY them up.

    ```ruby
    # bad
    it { expect(assigns('message')).to match /it was born in Belville/ }

    # good
    subject { assigns('message') }
    it { is_expected.to match /it was born in Billville/ }
    ```

    RSpec has also the ability to use a named subject.

    ```ruby
    # good
    subject(:hero) { Hero.first }
    it "carries a sword" do
      expect(hero.equipment).to include "sword"
    end
    ```

    Learn more about [rspec subject](https://www.relishapp.com/rspec/rspec-core/v/2-11/docs/subject).

**[⬆ back to top](#table-of-contents)**

## Use let and let!

  - [8.](#8.) <a name='8.'></a> When you have to assign a variable instead of using a `before` block to create an instance variable, use `let`. Using `let` the variable lazy loads only when it is used the first time in the test and get cached until that specific test is finished.

    ```ruby
    # bad
    describe '#type_id' do
      before { @resource = FactoryGirl.create :device }
      before { @type     = Type.find @resource.type_id }

      it 'sets the type_id field' do
        expect(@resource.type_id).to equal(@type.id)
      end
    end

    # good
    describe '#type_id' do
      let(:resource) { FactoryGirl.create :device }
      let(:type)     { Type.find resource.type_id }

      it 'sets the type_id field' do
        expect(resource.type_id).to equal(type.id)
      end
    end
    ```

    Use `let` to initialize actions that are lazy loaded to test your specs.

    ```ruby
    # good
    context 'when updates a not existing property value' do
      let(:properties) { { id: Settings.resource_id, value: 'on'} }

      def update
        resource.properties = properties
      end

      it 'raises a not found error' do
        expect { update }.to raise_error Mongoid::Errors::DocumentNotFound
      end
    end
    ```

    Use `let!` if you want to define the variable when the block is defined. This can be useful to populate your database to test queries or scopes.

    Here an example of what let actually is.

    ```ruby
    # good
    # this:
    let(:foo) { Foo.new }

    # is very nearly equivalent to this:
    def foo
      @foo ||= Foo.new
    end
    ```

    Learn more about [rspec let](https://www.relishapp.com/rspec/rspec-core/v/2-11/docs/helper-methods/let-and-let).

**[⬆ back to top](#table-of-contents)**

## Mock or not to mock

  - [9.](#9.) <a name='9.'></a> There's a debate going on. Do not (over)use mocks and test real behavior when possible. Testing real cases are useful when updating your application flow.

    ```ruby
    # good
    # simulate a not found resource
    context "when not found" do
      before { allow(Resource).to receive(:where).with(created_from: params[:id]).and_return(false) }
      it { is_expected.to respond_with 404 }
    end
    ```

    Mocking makes your specs faster but they are difficult to use. You need to understand them well to use them well. Read more [about](http://myronmars.to/n/dev-blog/2012/06/thoughts-on-mocking).

**[⬆ back to top](#table-of-contents)**

## Create only the data you need

  - [10.](#10.) <a name='10.'></a> If you have ever worked in a medium size project (but also in small ones), test suites can be heavy to run. To solve this problem, it's important not to load more data than needed. Also, if you think you need dozens of records, you are probably wrong.

    ```ruby
    # good
    describe "User" do
      describe ".top" do
        before { FactoryGirl.create_list(:user, 3) }
        it { expect(User.top(2)).to have(2).item }
      end
    end
    ```

**[⬆ back to top](#table-of-contents)**

## Use factories and not fixtures

  - [11.](#11.) <a name='11.'></a> This is an old topic, but it's still good to remember it. Do not use fixtures because they are difficult to control, use factories instead. Use them to reduce the verbosity on creating new data.

    ```ruby
    # bad
    user = User.create(
      name: 'Genoveffa',
      surname: 'Piccolina',
      city: 'Billyville',
      birth: '17 Agoust 1982',
      active: true
    )
    # good
    user = FactoryGirl.create :user
    ```

    One important note. When talking about unit tests the best practice would be to use neither fixtures or factories. Put as much of your domain logic in libraries that can be tested without needing complex, time consuming setup with either factories or fixtures. Read more in [this article](http://blog.steveklabnik.com/posts/2012-07-14-why-i-don-t-like-factory_girl)

    Learn more about [Factory Girl](https://github.com/thoughtbot/factory_girl).

**[⬆ back to top](#table-of-contents)**

## Easy to read matcher

  - [12.](#12.) <a name='12.'></a> Use readable matchers and double check the available [rspec matchers](https://www.relishapp.com/rspec/rspec-expectations/docs/built-in-matchers).

    ```ruby
    # bad
    lambda { model.save! }.to raise_error Mongoid::Errors::DocumentNotFound
    # good
    expect { model.save! }.to raise_error Mongoid::Errors::DocumentNotFound
    ```

**[⬆ back to top](#table-of-contents)**

## Shared Examples

  - [13.](#13.) <a name='13.'></a> Making tests is great and you get more confident day after day. But in the end you will start to see code duplication coming up everywhere. Use shared examples to DRY your test suite up.

    ```ruby
    # bad
    describe 'GET /devices' do
      let!(:resource) { FactoryGirl.create :device, created_from: user.id }
      let(:uri) { '/devices' }

      context 'when shows all resources' do
        let!(:not_owned) { FactoryGirl.create factory }

        it 'shows all owned resources' do
          page.driver.get uri
          expect(page.status_code).to be(200)
          contains_owned_resource resource
          does_not_contain_resource not_owned
        end
      end

      describe '?start=:uri' do
        it 'shows the next page' do
          page.driver.get uri, start: resource.uri
          expect(page.status_code).to be(200)
          contains_resource resources.first
          expect(page).to_not have_content resource.id.to_s
        end
      end
    end

    # good
    describe 'GET /devices' do

      let!(:resource) { FactoryGirl.create :device, created_from: user.id }
      let(:uri)       { '/devices' }

      it_behaves_like 'a listable resource'
      it_behaves_like 'a paginable resource'
      it_behaves_like 'a searchable resource'
      it_behaves_like 'a filterable list'
    end
    ```

    In our experience, shared examples are used mainly for controllers. Since models are pretty different from each other, they (usually) do not share much logic.

    Learn more about [rspec shared examples](https://www.relishapp.com/rspec/rspec-core/v/2-11/docs/example-groups/shared-examples).

**[⬆ back to top](#table-of-contents)**

## Test what you see

  - [14.](#14.) <a name='14.'></a> Deeply test your models and your application behaviour (integration tests). Do not add useless complexity testing controllers.

**[⬆ back to top](#table-of-contents)**

## Don't use should

  - [15.](#15.) <a name='15.'></a> Do not use should when describing your tests. Use the third person in the present tense. Even better start using the new `expectation` syntax.

    ```ruby
    # bad
    it 'should not change timings' do
      consumption.occur_at.should == valid.occur_at
    end

    # good
    it 'does not change timings' do
      expect(consumption.occur_at).to equal(valid.occur_at)
    end
    ```

    See [the should_not gem](https://github.com/should-not/should_not) for a way to enforce this in RSpec and [the should_clean gem](https://github.com/siyelo/should_clean) for a way to clean up existing RSpec examples that begin with `should.`

**[⬆ back to top](#table-of-contents)**

## Automatic tests with guard

  - [16.](#16.) <a name='16.'></a> Running all the test suite every time you change your app can be cumbersome. It takes a lot of time and it can break your flow. With Guard you can automate your test suite running only the tests related to the updated spec, model, controller or file you are working at.

    ```ruby
    # good
    bundle exec guard
    ```

    Here you can see a sample `Guardfile` with some basic reloading rules.

    ```ruby
    # good
    guard 'rspec', cli: '--drb --format Fuubar --color', version: 2 do
      # run every updated spec file
      watch(%r{^spec/.+_spec\.rb$})
      # run the lib specs when a file in lib/ changes
      watch(%r{^lib/(.+)\.rb$}) { |m| "spec/lib/#{m[1]}_spec.rb" }
      # run the model specs related to the changed model
      watch(%r{^app/(.+)\.rb$}) { |m| "spec/#{m[1]}_spec.rb" }
      # run the view specs related to the changed view
      watch(%r{^app/(.*)(\.erb|\.haml)$}) { |m| "spec/#{m[1]}#{m[2]}_spec.rb" }
      # run the integration specs related to the changed controller
      watch(%r{^app/controllers/(.+)\.rb}) { |m| "spec/requests/#{m[1]}_spec.rb" }
      # run all integration tests when application controller change
      watch('app/controllers/application_controller.rb') { "spec/requests" }
    end
    ```

    `Guard` is a fine tool but as usual it doesn't fit all of your needs. Sometimes your TDD workflow works best with a keybinding that makes it easy to run just the examples you want when you want to. Then, you can use a rake task to run the entire suite before pushing code.

    Learn more about [guard-rspec](https://github.com/guard/guard-rspec).

**[⬆ back to top](#table-of-contents)**

## Stubbing HTTP requests

  - [17.](#17.) <a name='17.'></a> Sometimes you need to access external services. In these cases you can't rely on the real service but you should stub it with solutions like `webmock`.

    ```ruby
    # good
    context "with unauthorized access" do
      let(:uri) { 'http://api.lelylan.com/types' }
      before    { stub_request(:get, uri).to_return(status: 401, body: fixture('401.json')) }
      it "gets a not authorized notification" do
        page.driver.get uri
        expect(page).to have_content 'Access denied'
      end
    end
    ```

    Learn more about [webmock](https://github.com/bblimke/webmock) and [VCR](https://github.com/vcr/vcr). Here a [nice presentation](http://marnen.github.io/webmock-presentation/webmock.html#(1)) explaining how to mix them together.

**[⬆ back to top](#table-of-contents)**

## Useful formatter

  - [18.](#18.) <a name='18.'></a> Use a formatter that can give you useful information about the test suite. I personally find fuubar really nice. To make it work add the gem and set `fuubar` as default formatter in your `Guardfile`.

    ```ruby
    # good
    # Gemfile
    group :development, :test do
      gem 'fuubar'

    # good
    # .rspec
    --drb
    --format Fuubar
    --color
    ```

    Learn more about [fuubar](http://jeffkreeftmeijer.com/2010/fuubar-the-instafailing-rspec-progress-bar-formatter/).

**[⬆ back to top](#table-of-contents)**

## License

(The MIT License)

Copyright (c) 2018 Money Advice Service Ltd

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
'Software'), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

**[⬆ back to top](#table-of-contents)**
