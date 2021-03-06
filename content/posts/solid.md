---
title: "SOLID"
date: 2015-07-07T01:53:25+05:30
draft: false
toc: false
images:
tags: 
  - solid
  - software design
---

Well Codeignition has added some new faces recently, and yeah I am one
of them. I just passed out from IIT Jodhpur and got in here. And its
been a huge learning experience since. Writing code is not the same now.
Now I need to impose a great deal of thought on the structure of the
code, and yes, SOLID has been a big help. So let's get back to the basics
and see what the fuss it is.

SOLID is an acronym for the five principles of software design, devised
by Robert C. Martin or well known as Uncle Bob. What are these five
principles, let's find out.

## 1. Single Responsibility Priniciple -
*A class should have only one reason to change, thus it should perform
only one job.*

Probably the most intuitive and easy to understand, but hard in
practice. Its application in code does wonders regarding simplicity and
readability.

Take a look at the code below, we have defined a class `person`, which has
3 attributes. One of them is `email`, which needs to be verified.

    class Person{
      name: string ;
      address: string;
      email: string;
      validate email(){
        //validation logic
      }
    }

One might want to verify the `email` in his own way, whether its syntax
or domain or whatever. These changes do not affect the job of `person`
class, since it is supposed to define a `person` object. If it is also
validating the email, it is then violating SRP.

So, we take the validation step outside, create a new class. That will
leave us with `person` class with only one job - defining a person
object. That should be it.

    class Email{
      email: string;
      validate email(){
        //validation logic
      }
    }

    class Person{
      name: string;
      address: string;
      email: Email;
    }

SRP has following advantages

- it makes refactoring really easy.
- imposes a great deal of structure on the code.
- naming things, which is actually tough, is made easier since classes
  now have one simple job, so one can name them accordingly which can
  make it clear what it does.

Let's move on to the next one.

## 2. Open/Closed Principle
*Modules should be open for extension, but closed for modification.*

Let's take a look at an example -

    class Rectangle{
      width;
      height;
    }

    class AreaCalculator{
      calculateArea(){
        //calculate area
      }
    }

Now suppose, you are instructed that `AreaCalculator` class should
calculate area of a `circle` as well. You might think that's not a big
deal.

You make one `circle` class and edit the `AreaCalculator` accordingly.

    class Circle{
      radius;
    }

    class AreaCalculator(shape){
      calculateArea(){
        if(shape == rectangle) return width * height;
        else return pie*radius*radius
      }
    }

Yay, you did it. But now, you are instructed to add one for `triangle`
as well. Well this is getting bad now, you will now edit the function,
adding another if-else will result in complex code. You do know we don't
like complex code.

So, how do we get it right? Well, OCP wants us to write code, which are
closed for modification but open for extension. It wants you to separate
the behaviours of the system, such that utterly intrinsic behaviours are
behind a wall, that can't be touched and the remainings that are
variable and might change a lot are on other side of the wall. The base
class is behind the wall, untouchable and all the dependencies point
towards the base class.

To resolve the above code, we will be using `interfaces`. Let's see how
that works out.

    interface Area{
      calculateArea(){}
    }

    class Rectangle : Area{
      width;
      height;

      calculateArea(){
        return width*height;
      }
    }

    class Circle : Area{
      radius;

      calculateArea(){
        return pie*radius*radius;
      }
    }

Now, even if you are told to find area of `triangle`, you will simply add
another class, implement the `Area` interface and override the function,
without modifying the existing code. Neat huh.

## 3. Liskov Substitution
*Objects of base class can be replaced by objects of derived class.*

Let's talk about shapes. There are quadrilaterals, rectangles, squares etc.
Let's take up rectangles and squares. A `rectangle` class would posses
`width` and `height` as its attributes and some functions like `area`,
`perimeter`.

    class Rectangle{
      width;
      height;

      setWidth(){};
      setHeight(){};
    }

Now, a square is a rectangle, right. So, a `square` can inherit from a
`rectangle`.

    class Square extends Rectangle{
    }

Good enough? No. We just violated the Liskov Substitution principle.

Think about it, a `rectangle` has two attributes: `width` and `height`, while a
`square` needs just one: `side`. `Square` needs only one variable, but it will
inherit two. A `rectangle` would have methods for `set height` and `set width`
but a `square` won't need those both.

Now, there is a design issue at stake.

Understand that IsA is not inheritance. Square is a rectangle, can't be
more true. But it's in terms of `geometry`, the same can't be said about
those two while programming. It is an example of bad design. A better
design would be to keep rectangle and square different and inheriting
from a shape class.

Liskov Substitution principle saves us from such bad software designs,
apply this and you won't make such mistakes again.

One more example is of circle and ellipse. I am leaving it on you to
think about it.

## 4. Interface Segregation Principle
*A client should never be forced to implement an interface that it does
not use or clients shouldn’t be forced to depend on methods they do not use.*

Let's say we have a class, a big fat class, call it `fat`, and it has
got a whole bunch of methods and there is a group of clients.

The population of clients that does not care about some specific methods
are still forced to, to change because `fat` class changed. In general,
if a source file changes, you have to recompile everybody who depends on
that source file, even if nothing they really cared about changed.

An example should make it more clear:

    interface ShapeInterface {
      area();
      volume();
    }

    class square : ShapeInterface{
    }

Now, we know that square is 2D object, and it does not have any volume
but still it has acquired that function. The function when called upon
will give errors and we surely don't want that.

The way we can avoid this from happening is by taking each of the
clients and creating an interface that contains only the methods that
they care about.

So, our example can be solved like this

    interface ShapeInterfaceTwoD{
      area();
    }

    class square : ShapeInterfaceTwoD{
    }

The square is now not taking any unnecessary methods, and we have achieved
interface segregation. Woohoo!!

## 5. Dependency Inversion
*High level modules should not depend upon low level modules. Both should
depend upon abstractions & abstractions should not depend upon details.
Details should depend upon abstractions.*

DI is a special case of OCP, which is basically used to reduce coupling
between two modules.

An example would be a better way to understand this.

    class PasswordReminder {
      dbConnection;

      __construct(MySQLConnection dbConnection) {
        this->dbConnection = dbConnection;
      }
    }

First, the `MySQLConnection` is the low level module while the
`PasswordReminder` is high level, but according to DI, this snippet above
violates this principle as the `PasswordReminder` class is being forced to
depend on the `MySQLConnection` class.

Later if we were to change the database engine, we would also have to edit
the `PasswordReminder` class and thus violates OCP.

To fix this again we code to an interface, since high level and low level
modules should depend on abstraction, we can create an interface:

    interface DBConnectionInterface {
      public function connect();
    }

    class MySQLConnection implements DBConnectionInterface {
      public function connect() {
        return "Database connection";
      }
    }

    class PasswordReminder {
      private $dbConnection;

      public function __construct(DBConnectionInterface $dbConnection) {
        $this->dbConnection = $dbConnection;
      }
    }

Now, even if we are to change the db engine, we can do that without
changing the `Password Reminder` class. See, we achieved reduced
coupling and we respected our OCP too.

Further Reading -

- [Tuts Plus' tutorial series on SOLID](http://code.tutsplus.com/series/the-solid-principles--cms-634)
- [Uncle Bob's webpage on SOLID](http://butunclebob.com/ArticleS.UncleBob.PrinciplesOfOod)
