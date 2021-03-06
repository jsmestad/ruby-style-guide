# Prelude

If you're into Rails you might want to check out the
complementary.
[Ruby on Rails 3 & 4 Style Guide](https://github.com/bbatsov/rails-style-guide).

# The Ruby Style Guide

Be consistent.

## Table of Contents

* [Source Code Layout](#source-code-layout)
* [Syntax](#syntax)
* [Naming](#naming)
* [Comments](#comments)
  * [Comment Annotations](#comment-annotations)
* [Classes](#classes--modules)
* [Exceptions](#exceptions)
* [Collections](#collections)
* [Strings](#strings)
* [Regular Expressions](#regular-expressions)
* [Percent Literals](#percent-literals)
* [Metaprogramming](#metaprogramming)
* [Misc](#misc)
* [Tools](#tools)

## Source Code Layout

* Use `UTF-8` as the source file encoding.
* Use two **spaces** per indentation level (aka soft tabs). No hard tabs.

  ```Ruby
  # bad - four spaces
  def some_method
      do_something
  end

  # good
  def some_method
    do_something
  end
  ```

* Limit lines to 80 characters (or as close to 80 lines as possible without breaking formatting guidelines).

* Avoid trailing whitespace.

* End each file with a newline.

* Use Unix-style line endings. (*BSD/Solaris/Linux/OS X users are covered by default,
  Windows users have to be extra careful.)
  * If you're using Git you might want to add the following
    configuration setting to protect your project from Windows line
    endings creeping in:

    ```bash
    $ git config --global core.autocrlf true
    ```

* Don't use `;` to separate statements and expressions. As a
  corollary - use one expression per line.

  ```Ruby
  # bad
  puts 'foobar'; # superfluous semicolon

  puts 'foo'; puts 'bar' # two expressions on the same line

  # good
  puts 'foobar'

  puts 'foo'
  puts 'bar'

  puts 'foo', 'bar' # this applies to puts in particular
  ```

* Prefer a single-line format for class definitions with no body.

  ```Ruby
  # bad
  class FooError < StandardError
  end

  # okish
  class FooError < StandardError; end

  # good
  FooError = Class.new(StandardError)
  ```

* Avoid single-line methods. Although they are somewhat popular in the
  wild, there are a few peculiarities about their definition syntax
  that make their use undesirable. At any rate - there should be no more
  than one expression in a single-line method.

  ```Ruby
  # bad
  def too_much; something; something_else; end

  # okish - notice that the first ; is required
  def no_braces_method; body end

  # okish - notice that the second ; is optional
  def no_braces_method; body; end

  # okish - valid syntax, but no ; makes it kind of hard to read
  def some_method() body end

  # good
  def some_method
    body
  end
  ```

  One exception to the rule are empty-body methods.

  ```Ruby
  # good
  def no_op; end
  ```

* Use spaces around operators, after commas, colons and semicolons, around `{`
  and before `}`. Whitespace might be (mostly) irrelevant to the Ruby
  interpreter, but its proper use is the key to writing easily
  readable code.

  ```Ruby
  sum = 1 + 2
  a, b = 1, 2
  [1, 2, 3].each { |e| puts e }
  class FooError < StandardError; end
  ```

  The only exception, regarding operators, is the exponent operator:

  ```Ruby
  # bad
  e = M * c ** 2

  # good
  e = M * c**2
  ```

  `{` and `}` deserve a bit of clarification, since they are used
  for block and hash literals, as well as embedded expressions in
  strings. For hash literals, no space after and before `{` and `}`,
  respectively.

  ```Ruby
  # bad - space after { and before }
  { one: 1, two: 2 }

  # good - no space after { and before }
  {one: 1, two: 2}
  ```

  As far as embedded expressions go, spaces before and after are unacceptable

  ```Ruby
  # good - no spaces
  "string#{expr}"

  # bad - spaces
  "string#{ expr }"
  ```

* No spaces after `(`, `[` or before `]`, `)`.

  ```Ruby
  some(arg).other
  [1, 2, 3].size
  ```

* No space after `!`.

  ```Ruby
  # bad
  ! something

  # good
  !something
  ```

* Indent `when` as deep as `case`. I know that many would disagree
  with this one, but it's the style established in both "The Ruby
  Programming Language" and "Programming Ruby".

  ```Ruby
  # bad
  case
    when song.name == 'Misty'
      puts 'Not again!'
    when song.duration > 120
      puts 'Too long!'
    when Time.now.hour > 21
      puts "It's too late"
    else
      song.play
  end

  # good
  case
  when song.name == 'Misty'
    puts 'Not again!'
  when song.duration > 120
    puts 'Too long!'
  when Time.now.hour > 21
    puts "It's too late"
  else
    song.play
  end
  ```

* When assigning the result of a conditional expression to a variable, preserve
  the usual alignment of its branches.

  ```Ruby
  # bad - pretty convoluted
  kind = case year
    when 1850..1889 then 'Blues'
    when 1890..1909 then 'Ragtime'
    when 1910..1929 then 'New Orleans Jazz'
    when 1930..1939 then 'Swing'
    when 1940..1950 then 'Bebop'
    else 'Jazz'
    end

  result = if some_cond
    calc_something
  else
    calc_something_else
  end

  # good - if character limit allows for it
  kind = case year
         when 1850..1889 then 'Blues'
         when 1890..1909 then 'Ragtime'
         when 1910..1929 then 'New Orleans Jazz'
         when 1930..1939 then 'Swing'
         when 1940..1950 then 'Bebop'
         else 'Jazz'
         end

  result = if some_cond
             calc_something
           else
             calc_something_else
           end

  # good (and a bit more width efficient)
  kind = case year
  when 1850..1889 then 'Blues'
  when 1890..1909 then 'Ragtime'
  when 1910..1929 then 'New Orleans Jazz'
  when 1930..1939 then 'Swing'
  when 1940..1950 then 'Bebop'
  else 'Jazz'
  end

  result = if some_cond
    calc_something
  else
    calc_something_else
  end
  ```

* Use empty lines between method definitions and also to break up a method
  into logical paragraphs internally.

  ```Ruby
  # bad
  def some_method
    data = initialize(options)
    data.manipulate!
    data.result
  end
  def some_method
    result
  end

  # good
  def some_method
    data = initialize(options)

    data.manipulate!

    data.result
  end

  def some_method
    result
  end
  ```

* Avoid comma after the last parameter in a method call, especially when the
  parameters are not on separate lines.

  ```Ruby
  # bad - easier to move/add/remove parameters, but still not preferred
  some_method(
               size,
               count,
               color,
             )

  # bad
  some_method(size, count, color, )

  # good
  some_method(size, count, color)
  ```

* Use spaces around the `=` operator when assigning default values to method parameters:

  ```Ruby
  # good
  def some_method(arg1=:default, arg2=nil, arg3=[])
    # do something...
  end

  # less than ideal (but by no means bad)
  def some_method(arg1 = :default, arg2 = nil, arg3 = [])
    # do something...
  end
  ```

  While several Ruby books suggest the first style, the second is much more prominent
  in practice (and arguably a bit more readable).

* Avoid line continuation `\` where not required. In practice, avoid using
  line continuations for anything but string concatenation.

  ```Ruby
  # bad
  result = 1 - \
           2

  # good (but still ugly as hell)
  result = 1 \
           - 2

  long_string = 'First part of the long string' \
                ' and second part of the long string'

  ```

* When continuing a chained method invocation on
  another line keep the `.` on the second line.

  ```Ruby
  # bad - need to consult first line to understand second line
  one.two.three.
    four

  # good - it's immediately clear what's going on the second line
  one.two.three
    .four
  ```

* Align the parameters of a method call if they span more than one
  line. When aligning parameters is not appropriate due to line-length
  constraints, single indent for the lines after the first is also
  acceptable.

  ```Ruby
  # starting point (line is too long)
  def send_mail(source)
    Mailer.deliver(to: 'bob@example.com', from: 'us@example.com', subject: 'Important message', body: source.text)
  end

  # bad (double indent)
  def send_mail(source)
    Mailer.deliver(
        to: 'bob@example.com',
        from: 'us@example.com',
        subject: 'Important message',
        body: source.text)
  end

  # bad - wastes whitespace
  def send_mail(source)
    Mailer.deliver(to: 'bob@example.com',
                   from: 'us@example.com',
                   subject: 'Important message',
                   body: source.text)
  end

  # good (normal indent)
  def send_mail(source)
    Mailer.deliver(
      to: 'bob@example.com',
      from: 'us@example.com',
      subject: 'Important message',
      body: source.text
    )
  end
  ```

* Align the elements of array literals spanning multiple lines.

  ```Ruby
  # bad - brackets/parentheses should be on separate line
  menu_item = ['Spam', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam',
    'Baked beans', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam']

  # bad
  menu_item =
    ['Spam', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam',
     'Baked beans', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam']

  # good
  menu_item = [
    'Spam', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam',
    'Baked beans', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam'
  ]
  ```

* Add underscores to large numeric literals to improve their readability.

  ```Ruby
  # bad - how many 0s are there?
  num = 1000000

  # good - much easier to parse for the human brain
  num = 1_000_000
  ```
 
* Don't use block comments. They cannot be preceded by whitespace and are not
  as easy to spot as regular comments.

  ```Ruby
  # bad
  =begin
  comment line
  another comment line
  =end

  # good
  # comment line
  # another comment line
  ```

## Syntax

* Use `::` only to reference constants(this includes classes and
  modules) and constructors (like `Array()` or `Nokogiri::HTML()`).
  Never use `::` for regular method invocation.

  ```Ruby
  # bad
  SomeClass::some_method
  some_object::some_method

  # good
  SomeClass.some_method
  some_object.some_method
  SomeModule::SomeClass::SOME_CONST

  # While defining methods using the constant syntax isn't best practice, they
  # exist, this is the best practice for calling them.
  SomeModule::SomeClass()
  ```

* Use `def` with parentheses when there are arguments. Omit the
  parentheses when the method doesn't accept any arguments.

   ```Ruby
   # bad
   def some_method()
     # body omitted
   end

   # good
   def some_method
     # body omitted
   end

   # bad
   def some_method_with_arguments arg1, arg2
     # body omitted
   end

   # good
   def some_method_with_arguments(arg1, arg2)
     # body omitted
   end
   ```

* Never use `for`, unless you know exactly why. Most of the time Enumerable
  should be used instead. `for` is implemented in terms of `each` (so
  you're adding a level of indirection), but with a twist - `for`
  doesn't introduce a new scope (unlike `each`) and variables defined
  in its block will be visible outside it.

  ```Ruby
  arr = [1, 2, 3]

  # bad
  for elem in arr do
    puts elem
  end

  # note that elem is accessible outside of the for loop
  elem #=> 3

  # good
  arr.each { |elem| puts elem }

  # elem is not accessible outside each's block
  elem #=> NameError: undefined local variable or method `elem'
  ```

* Never use `then` for multi-line `if/unless`.

  ```Ruby
  # bad
  if some_condition then
    # body omitted
  end

  # good
  if some_condition
    # body omitted
  end
  ```

* Always put the condition on the same line as the `if`/`unless` in a multi-line conditional.

  ```Ruby
  # bad
  if
    some_condition
    do_something
    do_something_else
  end

  # good
  if some_condition
    do_something
    do_something_else
  end
  ```

* Favor the ternary operator(`?:`) over `if/then/else/end` constructs.
  It's more common and obviously more concise.

  ```Ruby
  # bad
  result = if some_condition then something else something_else end

  # good
  result = some_condition ? something : something_else
  ```

* Use one expression per branch in a ternary operator. This
  also means that ternary operators must not be nested. Prefer
  `if/else` constructs in these cases.

  ```Ruby
  # bad
  some_condition ? (nested_condition ? nested_something : nested_something_else) : something_else

  # good
  if some_condition
    nested_condition ? nested_something : nested_something_else
  else
    something_else
  end
  ```

* Never use `if x: ...` - as of Ruby 1.9 it has been removed. Use
  the ternary operator instead.

  ```Ruby
  # bad
  result = if some_condition: something else something_else end

  # good
  result = some_condition ? something : something_else
  ```

* Never use `if x; ...`. Use the ternary operator instead.

* Leverage the fact that `if` and `case` are expressions which return a result.

  ```Ruby
  # bad
  if condition
    result = x
  else
    result = y
  end

  # good
  result = if condition
    x
  else
    y
  end
  ```

* Use `when x then ...` for one-line cases. The alternative syntax
  `when x: ...` has been removed as of Ruby 1.9.

* Never use `when x; ...`. See the previous rule.

  ```ruby
  # bad
  case
  when x; y
  end

  # good
  case
  when x then y
  end
  ```

* Use `!` instead of `not`.

  ```Ruby
  # bad - braces are required because of op precedence
  x = (not something)

  # good
  x = !something
  ```

* Avoid the use of `!!` in conditional statements. Exceptions are: 1. referencing large objects that would stick around longer than necessary; 2. objects that may not be a boolean but needed to be treated as such.

  ```Ruby
  # bad
  x = 'test'
  # obscure nil check
  if !!x
    # body omitted
  end

  x = false
  # double negation is useless when you know the object is a boolean
  !!x # => false

  # good
  x = 'test'
  if x
    # body omitted
  end
  ```

* Prefer to use `&&` and `||` over `and` and `or`. The only place `and` and `or` are useful is returning a boolean value and not an object.

* Avoid multi-line `?:` (the ternary operator); use `if/unless` instead.

* Favor modifier `if/unless` usage when you have a single-line
  body. Another good alternative is the usage of control flow `&&/||`.

  ```Ruby
  # bad
  if some_condition
    do_something
  end

  # good
  do_something if some_condition

  # another good option
  some_condition && do_something
  ```

* Try to avoid modifier `if/unless` usage at the end of a
  non-trivial multi-line block.

  ```Ruby
  # bad
  10.times do
    # multi-line body omitted
  end if some_condition

  # good
  if some_condition
    10.times do
      # multi-line body omitted
    end
  end
  ```

* Prefer using `if` over `unless` when possible. [reference](http://www.railstips.org/blog/archives/2008/12/01/unless-the-abused-ruby-conditional/)

* Favor `unless` over `if` for negative conditions (or control
  flow `||`).

  ```Ruby
  # bad
  do_something if !some_condition

  # bad
  do_something if not some_condition

  # good
  do_something unless some_condition

  # another good option
  some_condition || do_something
  ```

* Always write the positive case or 'happy path' first. Prefer to not use `unless` with `else`.

  ```Ruby
  # bad
  unless success?
    puts 'failure'
  else
    puts 'success'
  end

  if failure?
    puts 'failure'
  else
    puts 'success'
  end

  # good
  if success?
    puts 'success'
  else
    puts 'failure'
  end

  # less good, but reasonable if there is no reasonable means to determine
  the positive case.
  # Prefer unless condition over ! condition
  unless failure?
    puts 'success'
  else
    puts 'failure'
  end
  ```

* Don't use parentheses around the condition of an `if/unless/while/until`.

  ```Ruby
  # bad
  if (x > 10)
    # body omitted
  end

  # good
  if x > 10
    # body omitted
  end
  ```

* Never use `while/until condition do` for multi-line `while/until`.

  ```Ruby
  # bad
  while x > 5 do
    # body omitted
  end

  until x > 5 do
    # body omitted
  end

  # good
  while x > 5
    # body omitted
  end

  until x > 5
    # body omitted
  end
  ```

* Favor modifier `while/until` usage when you have a single-line
  body.

  ```Ruby
  # bad
  while some_condition
    do_something
  end

  # good
  do_something while some_condition
  ```

* Favor `until` over `while` for negative conditions.

  ```Ruby
  # bad
  do_something while !some_condition

  # good
  do_something until some_condition
  ```

* Use `Kernel#loop` instead of `while/until` when you need an infinite loop.

    ```ruby
    # bad
    while true
      do_something
    end

    until false
      do_something
    end

    # good
    loop do
      do_something
    end
    ```

* Use `Kernel#loop` with `break` rather than `begin/end/until` or `begin/end/while` for post-loop tests.

  ```Ruby
  # bad
  begin
    puts val
    val += 1
  end while val < 0

  # good
  loop do
    puts val
    val += 1
    break unless val < 0
  end
  ```

* Omit parentheses around parameters for methods that are part of an
  internal DSL (e.g. Rake, Rails, RSpec), methods that have
  "keyword" status in Ruby (e.g. `attr_reader`, `puts`) and attribute
  access methods. Use parentheses around the arguments of all other
  method invocations.

  ```Ruby
  class Person
    attr_reader :name, :age

    # omitted
  end

  temperance = Person.new('Temperance', 30)
  temperance.name

  puts temperance.age

  x = Math.sin(y)
  array.delete(e)

  bowling.score.should == 0
  ```

* Omit the outer braces around an implicit options hash (when defintion only accepts one hash)

  ```Ruby
  # bad
  user.set({ name: 'John', age: 45, permissions: { read: true } })

  # good
  user.set(name: 'John', age: 45, permissions: { read: true })
  ```

* Omit both the outer braces and parentheses for methods that are
  part of an internal DSL.

  ```Ruby
  class Person < ActiveRecord::Base
    # bad
    validates(:name, { presence: true, length: { within: 1..10 } })

    # good
    validates :name, presence: true, length: { within: 1..10 }
  end
  ```

* Omit parentheses for method calls with no arguments.

  ```Ruby
  # bad
  Kernel.exit!()
  2.even?()
  fork()
  'test'.upcase()

  # good
  Kernel.exit!
  2.even?
  fork
  'test'.upcase
  ```

* Prefer `{...}` over `do...end` for single-line blocks.  Avoid using
  `{...}` for multi-line blocks (multiline chaining is always
  ugly). Always use `do...end` for "method definitions" (e.g. in Rakefiles and
    certain DSLs).

  ```Ruby
  names = ['Bozhidar', 'Steve', 'Sarah']

  # bad
  names.each do |name|
    puts name
  end

  # good
  names.each { |name| puts name }

  # bad
  names.select do |name|
    name.start_with?('S')
  end.map { |name| name.upcase }

  # good
  names.select { |name| name.start_with?('S') }.map { |name| name.upcase }
  ```

* Always use an explicit block argument literal. Beware of the performance
impact, though, as the block gets converted to a Proc.

  ```Ruby
  require 'tempfile'

  # bad
  def with_tmp_dir
    Dir.mktmpdir do |tmp_dir|
      Dir.chdir(tmp_dir) { |dir| yield dir }  # block just passes arguments
    end
  end

  # good
  def with_tmp_dir(&block)
    Dir.mktmpdir do |tmp_dir|
      Dir.chdir(tmp_dir, &block)
    end
  end

  with_tmp_dir do |dir|
    puts "dir is accessible as a parameter and pwd is set: #{dir}"
  end
  ```

* Avoid `return` where not required for flow of control. 

  ```Ruby
  # bad
  def some_method(some_arr)
    return some_arr.size
  end

  # good
  def some_method(some_arr)
    some_arr.size
  end
  ```

* Avoid `self` where not required.

  ```Ruby
  # bad
  def ready?
    if self.last_reviewed_at > self.last_updated_at
      self.worker.update(self.content, self.options)
      self.status = :in_progress
    end
    self.status == :verified
  end

  # good
  def ready?
    if last_reviewed_at > last_updated_at
      worker.update(content, options)
      self.status = :in_progress
    end
    status == :verified
  end
  ```

* As a corollary, avoid shadowing methods with local variables unless they are both equivalent.

  ```Ruby
  class Foo
    attr_accessor :options

    # bad
    def do_something(options = {})
      unless options[:when] == :later
        output(self.options[:message])
      end
    end

    # ok
    def initialize(options)
      self.options = options
      # both options and self.options are equivalent here
    end

    # good
    def do_something(params = {})
      unless params[:when] == :later
        output(options[:message])
      end
    end
  end
  ```

* Don't use the return value of `=` (an assignment) in conditional
  expressions.

  ```Ruby
  # bad (+ a warning)
  if v = array.grep(/foo/)
    do_something(v)
    ...
  end

  # bad (MRI would still complain, but RuboCop won't)
  if (v = array.grep(/foo/))
    do_something(v)
    ...
  end

  # bad (MRI doesn't explain)
  if (v = array.grep(/foo/)).length.eq(3)
    do_something(v)
    ...
  end

  # good
  v = array.grep(/foo/)
  if v
    do_something(v)
    ...
  end
  ```

* Use shorthand self assignment operators whenever applicable.

  ```Ruby
  # bad
  x = x + y
  x = x * y
  x = x**y
  x = x / y
  x = x || y
  x = x && y

  # good
  x += y
  x *= y
  x **= y
  x /= y
  x ||= y
  x &&= y
  ```

* Use `||=` to initialize variables only if they're not already initialized.

  ```Ruby
  # bad
  name = name ? name : 'Bozhidar'

  # bad
  name = 'Bozhidar' unless name

  # good - set name to Bozhidar, only if it's nil or false
  name ||= 'Bozhidar'
  ```

* Don't use `||=` to initialize boolean variables. (Consider what
  would happen if the current value happened to be `false`.)

  ```Ruby
  # bad - would set enabled to true even if it was false
  enabled ||= true

  # good
  enabled = true if enabled.nil?
  ```

* Use `&&=` to preprocess variables that may or may not exist. Using
  `&&=` will change the value only if it exists, removing the need to
  check its existence with `if`.

  ```Ruby
  # bad
  something = something ? nil : something.downcase

  # bad
  something = something.downcase if something

  # bad
  something = something && something.downcase

  # might be reasonable depending on length of method call
  if something
    something = something.downcase
  end

  # good
  something &&= something.downcase
  ```

* Avoid explicit use of the case equality operator `===`. As its name
  implies it is meant to be used implicitly by `case` expressions and
  outside of them it yields some pretty confusing code.

  ```Ruby
  # bad
  Array === something
  (1..100) === 7
  /something/ === some_string

  # good
  something.is_a?(Array)
  (1..100).include?(7)
  some_string =~ /something/
  ```

* Avoid using Perl-style special variables (like `$:`, `$;`,
  etc. ). They are quite cryptic and their use in anything but
  one-liner scripts is discouraged. Use the human-friendly
  aliases provided by the `English` library.

  ```Ruby
  # bad
  $:.unshift File.dirname(__FILE__)

  # good
  require 'English'
  $LOAD_PATH.unshift File.dirname(__FILE__)
  ```

* Never put a space between a method name and the opening parenthesis.

  ```Ruby
  # bad
  f (3 + 2) + 1

  # good
  f(3 + 2) + 1
  ```

* If the first argument to a method begins with an open parenthesis,
  always use parentheses in the method invocation. For example, write
  `f((3 + 2) + 1)`.

* Always run the Ruby interpreter with the `-w` option so it will warn
  you if you forget either of the rules above!

* Use the new lambda literal syntax for single line body blocks.

  ```Ruby
  # ok
  l = lambda { |a, b| a + b }
  l.call(1, 2)

  # better
  l = ->(a, b) { a + b }
  l.call(1, 2)
  ```

* Prefer `proc.call()` over `proc[]` or `proc.()` for both lambdas and procs.

  ```Ruby
  # bad - looks similar to Enumeration access
  l = ->(v) { puts v }
  l[1]

  # also bad - uncommon syntax
  l = ->(v) { puts v }
  l.(1)

  # good
  l = ->(v) { puts v }
  l.call(1)
  ```

* Prefix with `_` unused block parameters and local variables. It's
  also acceptable to use just `_` (although it's a bit less
  descriptive). This convention is recognized by the Ruby interpreter
  and tools like RuboCop and will suppress their unused variable warnings.

  ```Ruby
  # bad
  result = hash.map { |k, v| v + 1 }

  def something(x)
    unused_var, used_var = something_else(x)
    # ...
  end

  # good
  result = hash.map { |_k, v| v + 1 }

  def something(x)
    _unused_var, used_var = something_else(x)
    # ...
  end

  # good
  result = hash.map { |_, v| v + 1 }

  def something(x)
    _, used_var = something_else(x)
    # ...
  end
  ```

* Use `$stdout/$stderr/$stdin` instead of
  `STDOUT/STDERR/STDIN`. `STDOUT/STDERR/STDIN` are constants, and
  while you can actually reassign (possibly to redirect some stream)
  constants in Ruby, you'll get an interpreter warning if you do so.

* Use `warn` instead of `$stderr.puts`. Apart from being more concise
  and clear, `warn` allows you to suppress warnings if you need to (by
  setting the warn level to 0 via `-W0`).

* Favor the use of `sprintf` and its alias `format` over the fairly
  cryptic `String#%` method.

  ```Ruby
  # bad
  '%d %d' % [20, 10]
  # => '20 10'

  # good
  sprintf('%d %d', 20, 10)
  # => '20 10'

  # good
  sprintf('%{first} %{second}', first: 20, second: 10)
  # => '20 10'

  # good
  format('%d %d', 20, 10)
  # => '20 10'

  # good
  format('%{first} %{second}', first: 20, second: 10)
  # => '20 10'
  ```

* Favor the use of `Array#join` over the fairly cryptic `Array#*` with
  a string argument.

  ```Ruby
  # bad
  %w(one two three) * ', '
  # => 'one, two, three'

  # good
  %w(one two three).join(', ')
  # => 'one, two, three'
  ```

* Use `[*var]` or `Array()` instead of explicit `Array` check, when dealing with a
  variable you want to treat as an Array, but you're not certain it's
  an array.

  ```Ruby
  # bad
  paths = [paths] unless paths.is_a? Array
  paths.each { |path| do_something(path) }

  # good
  [*paths].each { |path| do_something(path) }

  # good (and a bit more readable)
  Array(paths).each { |path| do_something(path) }
  ```

* Use ranges or `Comparable#between?` instead of complex comparison logic when possible.

  ```Ruby
  # bad
  do_something if x >= 1000 && x <= 2000

  # good
  do_something if (1000..2000).include?(x)

  # good
  do_something if x.between?(1000, 2000)
  ```

* Favor the use of predicate methods to explicit comparisons with
  `==`. Numeric comparisons are OK.

  ```Ruby
  # bad
  if x % 2 == 0
  end

  if x % 2 == 1
  end

  if x == nil
  end

  # ok
  if x == 0
  end

  # good
  if x.even?
  end

  if x.odd?
  end

  if x.nil?
  end

  if x.zero?
  end
  ```

* Don't do explicit non-`nil` checks unless you're dealing with boolean values.

    ```ruby
    # bad
    do_something if !something.nil?
    do_something if something != nil

    # good
    do_something if something

    # good - dealing with a boolean
    def value_set?
      !@some_boolean.nil?
    end
    ```

* Avoid the use of `BEGIN` blocks.

* Never use `END` blocks. Use `Kernel#at_exit` instead.

  ```ruby
  # bad
  END { puts 'Goodbye!' }

  # good
  at_exit { puts 'Goodbye!' }
  ```

* Avoid the use of flip-flops.

* Avoid use of nested conditionals for flow of control.

  Prefer a guard clause when you can assert invalid data. A guard clause
  is a conditional statement at the top of a function that bails out as
  soon as it can.

  ```Ruby
  # bad
  def compute_thing(thing)
    if thing[:foo]
      update_with_bar(thing)
      if thing[:foo][:bar]
        partial_compute(thing)
      else
        re_compute(thing)
      end
    end
  end

  # good
  def compute_thing(thing)
    return unless thing[:foo]
    update_with_bar(thing[:foo])
    return re_compute(thing) unless thing[:foo][:bar]
    partial_compute(thing)
  end
  ```

  Prefer `next` in loops as a guard clause instead of conditional blocks.

  ```Ruby
  # bad
  [0, 1, 2, 3].each do |item|
    if item > 1
      puts item
    end
  end

  # good
  [0, 1, 2, 3].each do |item|
    next unless item > 1

    puts item
  end
  ```

## Naming

> The only real difficulties in programming are cache invalidation and
> naming things. <br/>
> -- Phil Karlton

* Name identifiers in English.

  ```Ruby
  # bad - identifier using non-ascii characters
  заплата = 1_000

  # bad - identifier is a Bulgarian word, written with Latin letters (instead of Cyrillic)
  zaplata = 1_000

  # good
  salary = 1_000
  ```

* Use `snake_case` for symbols, methods and variables. Note: Ruby itself violates this.

  ```Ruby
  # bad
  :'some symbol'
  :SomeSymbol
  :someSymbol

  someVar = 5

  def someMethod
    ...
  end

  def SomeMethod
   ...
  end

  # good
  :some_symbol

  def some_method
    ...
  end
  ```

* Use `CamelCase` for classes and modules.  (Keep acronyms like HTTP,
  RFC, XML uppercase.)

  ```Ruby
  # bad
  class Someclass
    ...
  end

  class Some_Class
    ...
  end

  class SomeXml
    ...
  end

  # good
  class SomeClass
    ...
  end

  class SomeXML
    ...
  end
  ```

* Use `snake_case` for naming files, e.g. `hello_world.rb`.

* Use `snake_case` for naming directories, e.g. `lib/hello_world/hello_world.rb`.

* Aim to have just a single class/module per source file. Name the file name as
  the class/module, but replacing CamelCase with snake_case.

* Use `SCREAMING_SNAKE_CASE` for other constants.

  ```Ruby
  # bad
  SomeConst = 5

  # good
  SOME_CONST = 5
  ```

* The names of predicate methods (methods that return a boolean value)
  should end in a question mark.
  (i.e. `Array#empty?`). Methods that don't return a boolean, shouldn't
  end in a question mark.

* The names of potentially *dangerous* methods (i.e. methods that
  modify `self` or the arguments, `exit!` (doesn't run the finalizers
  like `exit` does), etc.) should end with an exclamation mark if
  there exists a safe version of that *dangerous* method.

  ```Ruby
  # bad - there is no matching 'safe' method
  class Person
    def update!
    end
  end

  # good
  class Person
    def update
    end
  end

  # good
  class Person
    def update!
    end

    def update
    end
  end
  ```

* Prefer `map` over `collect`, `detect` over `find`, `select` over
  `find_all`, `reduce` over `inject`. This is
  not a hard requirement; if the use of the alias enhances
  readability, it's ok to use it. The rhyming methods are inherited from
  Smalltalk and are not common in other programming languages. The
  reason the use of `select` is encouraged over `find_all` is that it
  goes together nicely with `reject` and its name is pretty self-explanatory.

* Don't use `count` as a substitute for `size`. For `Enumerable`
  objects other than `Array` it will iterate the entire collection in
  order to determine its size.

  ```Ruby
  # bad
  some_hash.count

  # good
  some_hash.size
  ```

* Use `flat_map` instead of `map` + `flatten`.
  This does not apply for arrays with a depth greater than 2, i.e.
  if `users.first.songs == ['a', ['b','c']]`, then use `map + flatten` rather than `flat_map`.
  `flat_map` flattens the array by 1, whereas `flatten` flattens it all the way.

  ```Ruby
  # bad
  all_songs = users.map(&:songs).flatten.uniq

  # good
  all_songs = users.flat_map(&:songs).uniq
  ```

* Use `reverse_each` instead of `reverse.each`. `reverse_each` doesn't
  do a new array allocation and that's a good thing.

  ```Ruby
  # bad
  array.reverse.each { ... }

  # good
  array.reverse_each { ... }
  ```

## Classes & Modules

* Use a consistent structure in your class definitions.

  ```Ruby
  class Person
    # constants that may be overridden by modules via prepend
    SOME_CONSTANT = 20

    # extend and include go first
    prepend SomeModule
    extend AnotherModule
    include SomeOtherModule

    # constants are next
    SOME_CONSTANT = 20

    # inner public class
    CustomErrorKlass = Class.new(StandardError)

    # afterwards we have attribute macros
    attr_reader :name

    # followed by other macros (if any)
    validates :name

    # public class methods are next in line
    def self.some_method
    end

    # followed by public instance methods
    def some_method
    end

    # protected and private methods are grouped near the end
    protected

    def some_protected_method
    end

    private

    def some_private_method
    end

    def private_inner_class
      Class.new(StandardError)
    end
  end
  ```

* Don't nest multi line classes within classes. Try to have such nested
  classes each in their own file in a folder named like the containing class.

  ```Ruby
  # bad

  # foo.rb
  class Foo
    class Bar
      # 30 methods inside
    end

    class Car
      # 20 methods inside
    end

    # 30 methods inside
  end

  # good

  # foo.rb
  class Foo
    # 30 methods inside
  end

  # foo/bar.rb
  class Foo
    class Bar
      # 30 methods inside
    end
  end

  # foo/car.rb
  class Foo
    class Car
      # 20 methods inside
    end
  end
  ```

* Prefer modules to classes with only class methods. Classes should be
  used only when it makes sense to create instances out of them.

  ```Ruby
  # bad
  class SomeClass
    def self.some_method
      # body omitted
    end

    def self.some_other_method
    end
  end

  # good
  module SomeClass
    module_function

    def some_method
      # body omitted
    end

    def some_other_method
    end
  end
  ```

* Favor the use of `module_function` over `extend self` when you want
  to turn a module's instance methods into class methods.

  ```Ruby
  # bad
  module Utilities
    extend self

    def parse_something(string)
      # do stuff here
    end

    def other_utility_method(number, string)
      # do some more stuff
    end
  end

  # good
  module Utilities
    module_function

    def parse_something(string)
      # do stuff here
    end

    def other_utility_method(number, string)
      # do some more stuff
    end
  end
  ```

* Avoid the usage of class (`@@`) variables due to their behavior in inheritance.

  ```Ruby
  class Parent
    @@class_var = 'parent'

    def self.print_class_var
      puts @@class_var
    end
  end

  class Child < Parent
    @@class_var = 'child'
  end

  Parent.print_class_var # => will print "child"
  ```

  As you can see all the classes in a class hierarchy actually share one
  class variable. Class instance variables should usually be preferred
  over class variables.

* Assign proper visibility levels to methods (`private`, `protected`)
  in accordance with their intended usage. Don't go off leaving
  everything `public` (which is the default). After all we're coding
  in *Ruby* now, not in *Python*.

* Indent the `public`, `protected`, and `private` methods as much the
  method definitions they apply to. Leave one blank line above the
  visibility modifier
  and one blank line below in order to emphasize that it applies to all
  methods below it.

  ```Ruby
  class SomeClass
    def public_method
      # ...
    end

    private

    def private_method
      # ...
    end

    def another_private_method
      # ...
    end
  end
  ```

* Use `protected` only if you actually need sibling instances to access the protected method

* Use `def self.method` to define singleton methods. This makes the code
  easier to refactor since the class name is not repeated.

  ```Ruby
  class TestClass
    # bad
    def TestClass.some_method
      # body omitted
    end

    # good
    def self.some_other_method
      # body omitted
    end
  end
  ```

## Exceptions

* Never return from an `ensure` block. If you explicitly return from a
  method inside an `ensure` block, the return will take precedence over
  any exception being raised, and the method will return as if no
  exception had been raised at all. In effect, the exception will be
  silently thrown away.

  ```Ruby
  def foo
    begin
      fail
    ensure
      return 'very bad idea'
    end
  end
  ```

* Use *implicit begin blocks* where possible.

  ```Ruby
  # bad
  def foo
    begin
      # main logic goes here
    rescue
      # failure handling goes here
    end
  end

  # good
  def foo
    # main logic goes here
  rescue
    # failure handling goes here
  end
  ```

* Don't suppress exceptions unless you can actually handle the problem.

  ```Ruby
  # bad
  begin
    # an exception occurs here
  rescue SomeError
    # the rescue clause does absolutely nothing
  end

  # bad
  do_something rescue nil
  ```

* Avoid using `rescue` in its modifier form.

  ```Ruby
  # bad - this catches exceptions of StandardError class and its descendant classes
  read_file rescue handle_error($!)

  # good - this catches only the exceptions of Errno::ENOENT class and its descendant classes
  def foo
    read_file
  rescue Errno::ENOENT => ex
    handle_error(ex)
  end
  ```

* Don't use exceptions for flow of control.

  ```Ruby
  # bad
  begin
    n / d
  rescue ZeroDivisionError
    puts 'Cannot divide by 0!'
  end

  # good
  if d.zero?
    puts 'Cannot divide by 0!'
  else
    n / d
  end
  ```

* Avoid rescuing the `Exception` class.  This will trap signals and calls to
  `exit`, requiring you to `kill -9` the process.

  ```Ruby
  # bad
  begin
    # calls to exit and kill signals will be caught (except kill -9)
    exit
  rescue Exception
    puts "you didn't really want to exit, right?"
    # exception handling
  end

  # good
  begin
    # a blind rescue rescues from StandardError, not Exception as many
    # programmers assume.
  rescue => e
    # exception handling
  end

  # also good
  begin
    # an exception occurs here

  rescue StandardError => e
    # exception handling
  end
  ```

## Collections

* Prefer `%w` to the literal array syntax when you need an array of
  words (non-empty strings without spaces and special characters in them).
  Apply this rule only to arrays with two or more elements.

  ```Ruby
  # bad
  STATES = ['draft', 'open', 'closed']

  # good
  STATES = %w(draft open closed)
  ```

* Prefer `%i` to the literal array syntax when you need an array of
  symbols (and you don't need to maintain Ruby 1.9 compatibility). Apply
  this rule only to arrays with two or more elements.

  ```Ruby
  # bad
  STATES = [:draft, :open, :closed]

  # good
  STATES = %i(draft open closed)
  ```

* Avoid comma after the last item of an `Array` or `Hash` literal
  when the items are not on separate lines.

  ```Ruby
  # bad
  VALUES = [1001, 2020, 3333, ]

  # good
  VALUES = [1001, 2020, 3333]
  ```

* When accessing the first or last element from an array, prefer `first` or `last` over `[0]` or `[-1]`.

* Use `Set` instead of `Array` when dealing with unique elements. `Set`
  implements a collection of unordered values with no duplicates. This
  is a hybrid of `Array`'s intuitive inter-operation facilities and
  `Hash`'s fast lookup.

* Prefer symbols instead of strings as hash keys.

  ```Ruby
  # bad
  hash = { 'one' => 1, 'two' => 2, 'three' => 3 }

  # good
  hash = { one: 1, two: 2, three: 3 }
  ```

* Use the Ruby 1.9 hash literal syntax when your hash keys are symbols.

  ```Ruby
  # bad
  hash = { :one => 1, :two => 2, :three => 3 }

  # good
  hash = { one: 1, two: 2, three: 3 }
  ```

* Don't mix the Ruby 1.9 hash syntax with hash rockets in the same
  hash literal. When you've got keys that are not symbols stick to the
  hash rockets syntax.

  ```Ruby
  # bad
  { a: 1, 'b' => 2 }

  # good
  { :a => 1, 'b' => 2 }
  ```

* Use `Hash#key?` instead of `Hash#has_key?` and `Hash#value?` instead
  of `Hash#has_value?`. As noted
  [here](http://blade.nagaokaut.ac.jp/cgi-bin/scat.rb/ruby/ruby-core/43765)
  by Matz, the longer forms are considered deprecated.

  ```Ruby
  # bad
  hash.has_key?(:test)
  hash.has_value?(value)

  # good
  hash.key?(:test)
  hash.value?(value)
  ```

* Use `Hash#fetch` when dealing with hash keys that should be present.

  ```Ruby
  heroes = { batman: 'Bruce Wayne', superman: 'Clark Kent' }
  # bad - if we make a mistake we might not spot it right away
  heroes[:batman] # => "Bruce Wayne"
  heroes[:supermann] # => nil

  # good - fetch raises a KeyError making the problem obvious
  heroes.fetch(:supermann)
  ```

* Introduce default values for hash keys via `Hash#fetch` as opposed to using custom logic.

  ```Ruby
  batman = { name: 'Bruce Wayne', is_evil: false }

  # bad - if we just use || operator with falsy value we won't get the expected result
  batman[:is_evil] || true # => true

  # good - fetch work correctly with falsy values
  batman.fetch(:is_evil, true) # => false
  ```

* Use `Hash#values_at` when you need to retrieve several values consecutively from a hash.

  ```Ruby
  # bad
  email = data['email']
  nickname = data['nickname']

  # good
  email, username = data.values_at('email', 'nickname')
  ```

* Be aware of the fact that as of Ruby 1.9 hashes are ordered and do not re-order.

* Prefer not to modify a collection while traversing it.

## Strings

* Prefer string interpolation and string formatting instead of string concatenation:

  ```Ruby
  # bad
  email_with_name = user.name + ' <' + user.email + '>'

  # good
  email_with_name = "#{user.name} <#{user.email}>"

  # good
  email_with_name = format('%s <%s>', user.name, user.email)
  ```

* Adopt a consistent string literal quoting style. There are two
  popular styles in the Ruby community, both of which are considered
  good - single quotes by default (Option A) and double quotes by default (Option B).

  * Prefer single-quoted strings when you don't need
    string interpolation or special symbols such as `\t`, `\n`, `'`,
    etc.

    ```Ruby
    # bad
    name = "Bozhidar"

    # good
    name = 'Bozhidar'
    ```

* Don't leave out `{}` around instance and global variables being
  interpolated into a string.

  ```Ruby
  class Person
    attr_reader :first_name, :last_name

    def initialize(first_name, last_name)
      @first_name = first_name
      @last_name = last_name
    end

    # bad - valid, but awkward
    def to_s
      "#@first_name #@last_name"
    end

    # good
    def to_s
      "#{@first_name} #{@last_name}"
    end
  end

  $global = 0
  # bad
  puts "$global = #$global"

  # good
  puts "$global = #{$global}"
  ```

* Avoid using `String#+` when you need to construct large data chunks.
  Instead, use `String#<<`. Concatenation mutates the string instance in-place
  and is always faster than `String#+`, which creates a bunch of new string objects.

  ```Ruby
  # good and also fast
  html = ''
  html << '<h1>Page title</h1>'

  paragraphs.each do |paragraph|
    html << "<p>#{paragraph}</p>"
  end
  ```

## Regular Expressions

* Use non-capturing groups when you don't use captured result of parentheses.

  ```Ruby
  /(first|second)/   # bad
  /(?:first|second)/ # good
  ```

* Character classes have only a few special characters you should care about:
  `^`, `-`, `\`, `]`, so don't escape `.` or brackets in `[]`.

* Be careful with `^` and `$` as they match start/end of line, not string endings.
  If you want to match the whole string use: `\A` and `\z` (not to be
  confused with `\Z` which is the equivalent of `/\n?\z/`).

  ```Ruby
  string = "some injection\nusername"
  string[/^username$/]   # matches
  string[/\Ausername\z/] # doesn't match
  ```

* For complex replacements `sub`/`gsub` can be used with block or hash.

## Percent Literals

* Prefer `%r` for regular expressions.

  ```Ruby
  # bad
  /(\s+)/

  # good
  %r|(\s+)|
  ```

* Prefer the the use of `%s`. It seems that the community has decided
  `:"some string"` is the preferred way to create a symbol with
  spaces in it.

* Prefer `|` as delimiters for all `%` literals.

  ```Ruby
  # bad
  %w[one two three]
  %q{"Test's king!", John said.}

  # good
  %w|one two three|
  %q|"Test's king!", John said.|
  ```

## Metaprogramming

* Avoid needless metaprogramming.

* Try to avoid string evaluation as much as possible.

## Misc

* Write `ruby -w` safe code.

* Avoid methods longer than 10 LOC (lines of code). Ideally, most methods will be shorter than
  5 LOC. Empty lines do not contribute to the relevant LOC.

* Avoid parameter lists longer than three or four parameters.

* Code in a functional way, avoiding mutation when that makes sense.

* Do not mutate arguments unless that is the purpose of the method.

* Avoid more than three levels of block nesting.

* Be consistent. In an ideal world, be consistent with these guidelines.

* Use common sense.

![Creative Commons License](http://i.creativecommons.org/l/by/3.0/88x31.png)
This work is licensed under a [Creative Commons Attribution 3.0 Unported License](http://creativecommons.org/licenses/by/3.0/deed.en_US)













Original by [Bozhidar](https://twitter.com/bbatsov)
