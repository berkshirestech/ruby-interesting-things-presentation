# ruby

!SLIDE

# Some interesting things about Ruby

!SLIDE
## Installation

```bash
# If you need homebrew
ruby -e "$(curl -fsSL https://raw.github.com/Homebrew/homebrew/go/install)"

brew install rbenv ruby-build
rbenv install 2.0.0-p451
rbenv global 2.0.0-p451
brew install rbenv-gem-rehash
gem install bundler

# You might need to add this line to your `~/.bashrc` (or ~/.zshrc if you
# use zsh) and restart your terminal session
eval "$(rbenv init -)"
```

!SLIDE
## Syntax

!SLIDE
### methods are called by default

```ruby
def some_method
  'hello'
end

some_method # => 'hello'
method(:some_method).call # => 'hello'
```

!SLIDE
### return from methods is implicit

```ruby
def something
  'value'
end
something # => 'value'

def get_last_name(first_name)
  if first_name == 'John'
    'Smith'
  end
end
get_last_name('Bob') # => nil
```

!SLIDE
### "Everything is an object"

```ruby
3.times {|num| num + 1 }  # => 1 # => 2 #=> 3
"foo".upcase # => 'FOO'
nil.nil?
nil.object_id #=> 8
```
!SLIDE
### query and mutating methods

```ruby
def a_query_method?
  true
end

def mutate!
  # change something
end
```

!SLIDE
### Regexp and shell literal syntax

```ruby
/(foo|bar)/.match('foo') # => true
'foo' =~ /(foo|bar)/

`echo 'hello world'` # => 'hello world'
```

!SLIDE
### symbols
```ruby
a_string = 'foo'
a_symbol = :foo

{ a_symbol => a_string }
{ foo: 'foo' }
```

!SLIDE
### blocks

```ruby
def do_something
  'something before'
  yield 'hi' if block_given?
  'something after'
end

do_something { |something| "something in between: #{something.upcase}" }
# => 'something before'
# => 'something in between: SOMETHING'
# => 'something after'

def pass_the_block(&block)
  block
end

the_block = pass_the_block { "I'm the block" }
the_block.call # => I'm the block
```

!SLIDE
### Procs and blocks are the same

```ruby
times_3 = Proc.new { |num| num * 3 }
times_3.call(4) # => 12

def do_something
  'something before'
  yield 4
  'something after'
end

do_something(& times_3) # => 12
```

!SLIDE
### symbol to proc idiom

```ruby
[1,2,3].map(&:to_s) # => ['1', '2', '3']
[1,2,3].select(&:even?) # => [2]
```

!SLIDE
### Classes are objects

```ruby
class Foo
  def blah
    'blah'
  end
end

Foo.name # => 'Foo'
Foo.methods # => [:blah]
Foo.ancestors # => [Foo, Object, Kernel, BasicObject]

bar = Class.new('Bar') do
  def bar
    'bar'
  end
end

bar.name # => 'Class <id>'
Bar = bar
bar.name # => Bar
```

!SLIDE
### classes are always open
```ruby
class Foo
  def blah
    'bar'
  end
end

foo = Foo.new
foo.blah # => 'bar'

class Foo
  def blah
    'baz'
  end
end

foo.blah # => 'baz'
```

!SLIDE
### Instances have a 'singleton class'
```ruby
class Foo
  def blah
    'bar'
  end
end

foo = Foo.new
def foo.blah
  'za'
end

foo.blah # => 'za'
Foo.new.blah # => 'bar'
foo.singleton_class.methods(true) # => [:baz]
```

!SLIDE
### Class methods are defined on the singleton class
```ruby
class Foo
  class << self
    def blah
      'ha-chah-chah'
    end
  end

  def self.bar
    'tarala'
  end
end

Foo.blah # => 'ha-chah-chah'
Foo.singleton_methods # => [:blah, :bar]
```

!SLIDE
## Culture

- Based on Perl and Smalltalk
- Ruby creator is Matz ('Matz is nice and so we are nice')
- Rails creator is DHH ('Fuck the enterprise')
- Like code to be 'pretty' and 'expressive'
- Heavy emphasis on testing, lots of debates about correct way to test, TDD
  versus BDD. DHH recently started a flamewar about 'TDD is Dead'

!SLIDE
## Culture

- A lot of people come from non-CS backgrounds, a lot of code camps these days
- Cool to be interested in other languages, a lot of angst about what other
  languages have
- Tend to value clarity over speed
- Like to create DSLs (see rspec below)
- "Convention over configuration"
- "Duck typing"
- "Metaprogramming"

!SLIDE
### "Convention over configuration"
```ruby
# Found in app/models/user.rb
class User < ActiveRecord::Base
end
# Maps onto users table

# Found in app/helpers/user_helper.rb
module UserHelper
  def foo
    'foo'
  end
end

# Found in app/controllers/users_controller.rb
class UsersController < ApplicationController
  def index
  end
end

# app/views/index.html.erb has access to #foo from UserHelper
```

!SLIDE
### "Duck typing"

```ruby
class Duck
  def yell
    'quack!'
  end
end

class Goose
  def yell
    'honk!'
  end
end

def pluck_duck(duck)
  duck.yell
end

pluck_duck(Goose.new) # => 'honk!'
```

!SLIDE
## Libraries

- Rubygems
- Bundler
- Rails
- Rspec
- Rake
- Guard
- Rspec
- Cucumber

!SLIDE
### Rspec

```ruby
class Duck
  attr_reader :name

  def initialize(name)
    @name = name
  end

  def yell
    'quack!'
  end

  def eat(food)
    food.destroy!
  end
end
```

!SLIDE
### Rspec

```ruby
require 'spec_helper'

describe Duck do
  subject(:duck) { Duck.new('Donald' }

  its(:name) { should eq 'Donald' }

  it 'requires a name to be set' do
    expect { Duck.new }.to raise_exception(ArgumentError)
  end

  describe '#yell' do
    it 'makes a quacking sound when it yells' do
      duck.yell.should eq 'quack!'
    end
  end

  describe '#eat' do
    let(:food) { double }

    it 'destroys the food' do
      food.should_receive(:destroy!)
      duck.eat(food)
    end
  end
end

```
