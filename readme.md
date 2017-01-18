# Intermediate Object-Oriented Programming in Ruby

## Learning Objectives

- Explain the use of self in Ruby
- Explain the difference between local, instance and class variables
- Define and differentiate between class and instance methods
- Define inheritance in the context of OOP
- Write a Ruby class that inherits from another

## Framing

This morning you started to learn about object-oriented programming in Ruby and how we can use classes to organize our code into objects.

This afternoon we will focus on two things...
  1. Reviewing what you learned this morning by building a class together.
  2. Introduce the concept of **inheritance** and how classes can pass attributes and methods to each other.

## We Do: Let's Build A Class (2:30 - 3:10, 40 minutes)

Let's collaboratively create a `Person` class!

--------

<details>
  <summary><strong>What's the difference between a class and an instance?</strong></summary>
  <br/>

  A class is a blueprint. An instance is an object generated from that blueprint.
  * A class' name is capitalized (e.g., `Person`) and is a template for objects of that class.
  * An instance is one specific object created using a class (e.g. `bob = Person.new("Bob", 10)`).
</details>
<br/>
<details>
  <summary><strong>How do we begin defining a <code>Person</code> class?</strong></summary>

  ```rb
  class Person
  end
  ```

</details>

--------

<details>
  <summary><strong>How can we set up our Person class so that it has <code>name</code> and <code>age</code> attributes.</strong></summary>

  ```rb
  class Person
    # This is run every time we call Person.new
    # Think of it as a "constructor method"
    def initialize(initial_name, initial_age)
      @name = initial_name
      @age = initial_age
    end
  end
  ```

</details>
<br/>
<details>
  <summary><strong>Why did we place an <code>@</code> in front of our variables? How else could we have written our variables?</strong></summary>
  <br/>

  **Local:** normal variable name, limited in scope and used for temporary values.

  **Instance:** starts with `@`, used to store attributes for a given instance and can be used in any instance method.

  **Class:** starts with `@@`, shared between all instances.

  > Class variable are not used very often. We'll explain why later...

</details>
<br/>
<details>
  <summary><strong>How can we generate an instance of this <code>Person</code> class?</strong></summary>

  ```rb
  adrian = Person.new("Adrian", 29)
  ```
</details>

--------

<details>
  <summary><strong>We currently can't access a person's name in the REPL. How would we go about defining a method that allows us to do that?</strong></summary>

  ```rb
  class Person
    def initialize(initial_name, initial_age)
      @name = initial_name
      @age = initial_age
    end

    def get_name
      return @name
    end

  end
  ```

  ```rb
  adrian = Person.new("Adrian", 29)
  adrian.get_name
  # => "Adrian"
  ```

</details>
<br/>
<details>
  <summary><strong>What about a method that allows us to redefine a person's name via the REPL?</strong></summary>

  ```rb
  class Person
    def initialize(initial_name, initial_age)
      @name = initial_name
      @age = initial_age
    end

    def get_name
      return @name
    end

    def set_name(name)
     @name = name
    end

  end
  ```

  ```rb
  adrian = Person.new("Adrian", 29)
  adrian.get_name = "Chad"
  adrian.set_name
  # => "Chad"
  ```

</details>
<br/>
<details>
  <summary><strong>What are shortcuts we can use in place of these getter/setter methods? We should be able to read and modify <code>name</code>, but only read <code>age</code>.</strong></summary>

  ```rb
  class Person
    attr_accessor :name
    attr_reader :age

    def initialize(initial_name, initial_age)
      @name = initial_name
      @age = initial_age
    end
  end
  ```

</details>

--------

<details>
  <summary><strong>Let's give our <code>Person</code> class a <code>say_name</code> method that prints his/her name to the console.</strong></summary>

  ```rb
  class Person
    attr_accessor :name
    attr_reader :age

    def initialize(initial_name, initial_age)
      @name = initial_name
      @age = initial_age
    end

    def say_name
      puts "Hi, my name is #{@name}."
    end
  end

  adrian = Person.new("Adrian", 29)
  adrian.say_name
  # => "Hi, my name is Adrian."
  ```

</details>

<details>
  <summary><strong>What type of method is <code>say_name</code>? What other types of methods are there?</strong></summary>

  `say_name` is an instance method. It is a method that we would call on an instance of `Person`.

  Ruby classes can define two types of methods...
  * **Instance:** called on on single instances.
  * **Class:** called on the class, deal with the set of objects.

  > Instance and Class methods are both common and okay to use. This is unlike class variables, which should be used sparingly.

</details>

--------

<details>
  <summary><strong>Now we want to keep track of how many people have been created using the <code>Person</code> class. What tools can we use to do this? How would we use them?</strong></summary>

  ```rb
  class Person
    attr_accessor :name
    attr_reader :age
    # We create a class variable that is an array contains all existing instances of `Person`
    @@people = []

    def initialize(initial_name, initial_age)
      @name = initial_name
      @age = initial_age
      # Whenever we create a new person, we push it into the `@@people` array
      @@people.push(self)
    end

    def say_name
      puts "Hi, my name is  #{@name}."
    end

    # Here we're creating a method that retrieves the value of `@@people`
    def self.get_people
      return @@people
    end
  end

  adrian = Person.new("Adrian", 29)
  puts adrian.get_people
  # => error
  puts Person.get_people
  # => [adrian]

  keith = Person.new("Keith", 100)
  puts Person.get_people
  # => [adrian, keith]
  ```

  > We could also use `self.name` in place of the instance variable `@name`. This is actually preferable. We'll explain why later...

</details>

### Common Practice: No Class Variables

Developers tend to avoid using class variables (e.g. `@@some_variable`). As we'll see later in this lesson, class variables often will not work as intended when inheritance is brought into the mix, or introduce unnecessary complications.

Instead, a more common practice is to create a **helper class** that introduces the same functionality that a class variable would, but without headaches introduced by inheritance. From [wikipedia](https://en.wikipedia.org/wiki/Helper_class):
> In object-oriented programming, a helper class is used to assist in providing some functionality, which isn't the main goal of the application or class in which it is used

For example, in the case of `Person`, we could have a `Group` class. Each instance of `Group` would have an instance variable `@people` which would contain each instance of `Person`.



```rb
class Group
  attr_accessor :people

  def initialize(initial_people = [])
    @people = initial_people
  end

  def add_person(person)
    @people.push(person)
  end
end

class Person
  attr_accessor :name
  attr_reader :age

  def initialize(initial_name, initial_age)
    @name = initial_name
    @age = initial_age
  end

  def say_name
    puts "Hi, my name is #{@name}."
  end
end
```

```rb
# Create instances of Group and Person
instructors = Group.new
adrian = Person.new("Adrian", 29)

# Keep track of how many instructors there are by adding `adrian` (Person) to `instructors` (Group)
instructors.add_person(adrian)

# To check how many instructors there are we can run this...
instructors.people
# => [adrian]
```

We could also add other attributes and methods to the `Group` class. It doesn't have to just be a container for instances of the `Person` class.

To illustrate the use of class variables and methods, we're going to move forward without `Group` in this lesson.

### When Might You Use Class Variables?

Sometimes we don't want to go through the trouble of creating a higher class like `Group`. This is especially the case when dealing with application configuration values. [Take a look at this SASS example...](https://github.com/sass/sass/blob/bbc7663c1e24ed7f8e7014f356af1f6c4209aac9/lib/sass/version.rb#L48-L82)

### Self

<details>
  <summary><strong>What does `self` mean in Ruby?</strong></summary>

  > It contains the current instance of an object.

</details>

<details>
  <summary><strong>What is the equivalent of `self` in Javascript?</strong></summary>

  > `this`

</details>

<details>
  <summary><strong>What are two ways we can use `self` in our `Person` class?</strong></summary>

  ```rb
  # In a class method definition. We've already done this...
  def self.get_people
    return @@people
  end
  ```

  > When used in a method name, `self` = the class.

  ```rb
  # In place of an instance variable...
  def say_name
    puts "Hi, my name is " + self.name + "."
  end
  ```

  > When used inside of a method, `self` = the instance.

</details>

One benefit of Ruby is that it is much easier to determine what context we are working in. Unlike Javascript, we cannot redefine what context we are working with using methods like `.bind` `.call` or `.apply`.

### You Do: Keep Building `Person` (3:10 - 3:25, 15 minutes)

> 10 minutes exercise. 5 minutes review.

Make the following additions to our `Person` class. It is up to you to determine whether you should use an instance or class method.

* A method that checks to see if a `Person` is old enough to vote.
* A method that returns the number of `Person` instances that have been created.
* A method that counts the number of `Person` instances that have been created.
* A method that compares two people and determines which one is older.

## Break (3:25 - 3:35, 10 minutes)

## Inheritance (3:35 - 3:50, 15 minutes)

Just like we get traits from our parents, we can use a feature called **inheritance** to create multiple classes (children) that share properties and methods from their parents.

You won't need to write parent / child classes much in this class, but we will use inheritance to give some of our classes additional functionality, especially with rails in a few weeks.

We've added a `say_age` method to `Person` below to better illustrate how inheritance works in Ruby.

```rb
class Person
  attr_accessor :name
  attr_reader :age
  @@people = []

  def initialize(initial_name, initial_age)
    @name = initial_name
    @age = initial_age
    @@people.push(self)
  end

  def say_name
    puts "Hi, my name is " + @name + "."
  end

  def say_age
    puts "I am #{@age} years old."
  end

  def self.get_people
    return @@people
  end
end

# Inheritance is indicated using the `<` symbol after the child class' name.
class LoudPerson < Person
  # LoudPerson's `say_name` overrides that of the `Person` class.
  def say_name
    puts "HEY YOU, MY NAME IS #{@name.upcase}!"
  end
end

jill = Person.new("Jill", 10)
bob = LoudPerson.new("Bob", 20)

# Both `say_age` methods produce the same result since it was not overwritten in the `LoudPerson` class.
jill.say_age
# => "I am 10 years old."
bob.say_age
# => "I am 20 years old."

# The `say_name` methods produce different results because we overwrote the original in the `LoudPerson` class.
jill.say_name
# => "My name is Jill."
bob.say_name
# => "HEY YOU, MY NAME IS BOB!"
```

### Inheritance & Class Variables

Earlier we mentioned that we tend to avoid using class variables because of how they are affected by inheritance. Let's illustrate a potential issue using `Person` and `LoudPerson`...

```rb
class Person
  attr_accessor :name
  attr_reader :age
  @@people = []
  @@class_name = "Person"

  def initialize(initial_name, initial_age)
    @name = initial_name
    @age = initial_age
    @@people.push(self)
  end

  def get_class_name
    puts @@class_name
  end

  # We're hiding all the methods we created earlier...
end

class LoudPerson < Person
  @@class_name = "LoudPerson"
  def say_name
    puts "HEY YOU, MY NAME IS #{@name.upcase}!"
  end

  def get_class_name
    puts @@class_name
  end
end
```

Note that we have given both `Person` and `LoudPerson` a class variable called `@@class_name`. Each class has their own value for that variable. We've also given each class a `get_class_name` method to access those values.

Let's see what happens when we try to access those values.

```rb
bob.get_class_name
# => "LoudPerson"

jill.get_class_name
# => "LoudPerson"
```

<details>
  <summary><strong>What's the issue here?</strong></summary>

  > When parent and child classes share a class variable of the same name, the value of the child class variable overrides that of the parent.

</details>

### You Do: Inheritance (3:50 - 4:05, 15 minutes)

> 10 minutes exercise. 5 minutes review.

* Create a `QuietPerson` class that has their own version of `say_name`.
* Create a `VeryLoudPerson` class that...
  1. Shares the same attributes as `Person` and `LoudPerson`.
  2. Uses the same `say_name` method as `LoudPerson`.
  3. Has their own loud version of the `say_age` method.
* Create a `TwoHeadedPerson` class that shares the same methods as `Person` but also has a second name attribute (for the other head).


## Sample Questions

* Explain the use of self in Ruby.
* Explain the difference between local, instance and class variables.
* Define and differentiate between class and instance methods.
* What does it mean for a class to inherit from another?
* How do we overwrite methods inherited from a parent class?

## Vocabulary

* **class**: an object blueprint
* **instance**: a constructed class instance
* **sub-class**: a class derived through inheritance.
* **extends**: a class is "extended" into a sub-class.
* **self**: an instance's way of referring to itself.
* **super**: a sub-class' way of referring to its parent class.
* **public**: methods available outside of the class.
* **private**: methods available only within the class.
* **protected**: methods available to only the class and its descendants.

## Resources

* [Getters and Setters](http://andrewsunglaekim.github.io/Get-set-ruby/)
* [Visibility](http://stackoverflow.com/questions/9882754/what-are-the-differences-between-private-public-and-protected-methods)
* [Private/Protected](http://matthodan.com/2010/08/08/ruby-private-methods-vs-protected-methods.html)
