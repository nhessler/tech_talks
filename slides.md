Jump into Recursion with Ruby
=========================================

#### -Nathan Hessler-

---

## The Promise

I have done my best to organize this talk in such a way that everyone will learn something.

While this talk is technical and your attention is required, I try to take small steps so that everyone can follow along.

---

## The Big Picture

* Method Stack
* Recursion
* Tail Optimization
* Trampolines

---

## Method Stack

---

### Stack Definition

Stacks in computing architectures are regions of memory where data is added or removed in a last-in-first-out (LIFO) manner.

https://en.wikipedia.org/wiki/Stack-based_memory_allocation

Stack actions:

* Push - adds to the top of the stack
* Pop - removes from the top of the stack

---

**Real World Example**

<img src="/images/partial_stack_toy.jpg" alt="partial stack toy" height=500>

---

### ruby example
```ruby
class Person
  attr_reader :first_name, :last_name

  #... Person.new takes first and last name as params ...

  def full_name
    first_name.concat(' ').concat(last_name)
  end
end

person = Person.new("Nathan", "Hessler")

# What's happening when this gets run?
person.full_name
```

---
<!-- .slide: data-transition="none" -->
**Push full_name**

<img src="/images/1023_Hanoi_Tower_Set1-01.svg" alt="push full_name" height=500>


---
<!-- .slide: data-transition="none" -->
**Push first_name**

<img src="/images/1023_Hanoi_Tower_Set1-02.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop first_name**

<img src="/images/1023_Hanoi_Tower_Set1-03.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Push concat**

<img src="/images/1023_Hanoi_Tower_Set1-04.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop concat**

<img src="/images/1023_Hanoi_Tower_Set1-05.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Push last_name**

<img src="/images/1023_Hanoi_Tower_Set1-06.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop last_name**

<img src="/images/1023_Hanoi_Tower_Set1-07.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Push concat**

<img src="/images/1023_Hanoi_Tower_Set1-08.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop concat**

<img src="/images/1023_Hanoi_Tower_Set1-09.svg" alt="push full_name" height=500>

---

### Things To Know

* The Stack is not infinite
* What is our stack size?
  * We'll use 10
  * You can find your stack size if you want

```shell
  ulimit -s #=> 8192
```

---

## Recursion

---

### Definition

Recursion is the process a method goes through when one of the steps of the method involves invoking the method itself.

https://en.wikipedia.org/wiki/Recursion
---

### Factorial
**The Hello World of Recursion**

In mathematics, the factorial of a non-negative integer n, denoted by n!, is the product of all positive integers less than or equal to n.

https://en.wikipedia.org/wiki/Factorial

``` shell
3! =     3 * 2 * 1 = 6
4! = 4 * 3 * 2 * 1 = 24

# more formally (using recursion)

n! = n * (n-1)! when n > 1
n! = 1          when n = 0 or n = 1
```

---

### Recursive Factorial

```ruby
def factorial(n)
  return 1 if n <= 1
  n * factorial(n - 1)
end

# What happens when we run this?
factorial(3)
```

---
<!-- .slide: data-transition="none" -->
**Push factorial(3)**

<img src="/images/1023_Hanoi_Tower_Set2-01.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Push factorial(2)**

<img src="/images/1023_Hanoi_Tower_Set2-02.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Push factorial(1)**

<img src="/images/1023_Hanoi_Tower_Set2-03.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop factorial(1)**

<img src="/images/1023_Hanoi_Tower_Set2-04.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop factorial(2)**

<img src="/images/1023_Hanoi_Tower_Set2-05.svg" alt="push full_name" height=500>

---

### Stackoverflow

* Remember:
  * The Stack is not infinite
  * Our stack size is 10
* Recursion is often what *'overflows'* the stack

``` ruby
# What happens when we run this?
factorial(11)
```

---

**The Stack Overfloweth**

<img src="/images/1023_Hanoi_Tower_Overflow.svg" alt="push full_name" height=500>

---

## Tail Optimization

---

### Tail Recursion

a tail call is a method call performed as the **final action** of a method. If a tail call leads to the same method being called again, the method is said to be **tail-recursive**,

https://en.wikipedia.org/wiki/Tail_call

---

### Tail Recursive Factorial

```ruby
def factorial(n) # maintain interface
  factorial_helper(1, n)
end

# never call directly
def factorial_helper(accumulator, n)
  return accumulator if n <= 1
  # make sure the last thing done is the recursive call
  factorial_helper(accumulator * n, n - 1)
end

#What happens when we run this?
factorial(3)
```
---
<!-- .slide: data-transition="none" -->
**Push factorial(3)**

<img src="/images/1023_Hanoi_Tower_Set3-01.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Push factorial_helper(1, 3)**

<img src="/images/1023_Hanoi_Tower_Set3-02.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Push factorial_helper(3, 2)**

<img src="/images/1023_Hanoi_Tower_Set3-03.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Push factorial_helper(6, 1)**

<img src="/images/1023_Hanoi_Tower_Set3-04.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop factorial_helper(6, 1)**

<img src="/images/1023_Hanoi_Tower_Set3-05.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop factorial_helper(3, 2)**

<img src="/images/1023_Hanoi_Tower_Set3-06.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop factorial_helper(1, 3)**

<img src="/images/1023_Hanoi_Tower_Set3-07.svg" alt="push full_name" height=500>

---

### Tail Call Elimination

Tail calls can be implemented without adding a new stack frame to the method stack. Most of the frame of the current method is not needed any more, and it can be replaced by the frame of the tail call, modified as appropriate.

https://en.wikipedia.org/wiki/Tail_call

**Note**: This is usually implemented by functional Languages(Lisp, Erlang, Haskell), and not by OO Languages(Java, Python, Ruby???)

---
<!-- .slide: data-transition="none" -->
**Push factorial(3)**

<img src="/images/1023_Hanoi_Tower_Set4-01.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Push factorial_helper(1, 3)**

<img src="/images/1023_Hanoi_Tower_Set4-02.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop and Push factorial_helper(3, 2)**

<img src="/images/1023_Hanoi_Tower_Set4-03.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop and Push factorial_helper(6, 1)**

<img src="/images/1023_Hanoi_Tower_Set4-04.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop factorial_helper(6, 1)**

<img src="/images/1023_Hanoi_Tower_Set4-05.svg" alt="push full_name" height=500>

---

### Ruby and Tail Call Elimination

* A Muggle's Guide To Tail Call Optimization
* http://rubyconf.org/program#prop_1447
* Summary: Ruby has it
  * Not turned on by default
  * A bit finicky. Rails test fails with it on.

---

## Trampolines

---

### Trampolines 
#### A Functional Pattern mimicking Recursion

``` ruby
def trampoline(meth)
  lambda do |*args|
    thunk = meth.call(*args)
    loop do
      return thunk unless thunk.is_a? Proc
      thunk = thunk.call()
    end
  end
end
```

---

### Trampolined Factorial
``` ruby
def factorial(n)
  trampoline(method(:factorial_helper)).call(1, n)
end

def factorial_helper(acc, n)
  return acc if n <= 1
  lambda { factorial_helper(acc * n, n - 1) }
end

# What happens when we run this?
# Try working it out with what we've learned
factorial(11) #=> 39_916_800
```

---

## Bonus

---

### Insipiration: Clojure

* Clojure is a lisp dialect built on the Java VM
* Java is OO and the VM does not do tail call elimination
* Clojure is functional and really wants it
* `recur` is their solution

```clojure
(defn factorial [n]
  (factorial-helper 1 n)))

(defn factorial-helper [acc cnt]
  (if (zero? cnt)
    acc
    (recur (dec cnt) (* acc cnt)))))
```

---

### How does this work?

* There are a couple possibilities..
* They could be messing with the AST, it's Lisp after all
* They could just be doing some stack management tricks
* All are options in ruby
* we'll focus on stack management tricks

---

### Recurring Factorial

```ruby
def factorial(n) # maintain interface
  factorial_helper(1, n)
end

# never call directly
def factorial_helper(accumulator, n)
  return accumulator if n <= 1
  # replace tail call with recur method call
  recur(accumulator * n, n - 1)
end

```
---

### Recur Defined

```ruby
def recur(*args)
  return [:recur, args] if instance_variable_defined? :@caller
  begin
    @caller = binding.of_caller(1).eval('__method__')
    caller_args = [:start, args]
    loop do
      caller_args = __send__ @caller, *caller_args.last
      return caller_args unless caller_args.respond_to? :first
      return caller_args unless caller_args.first == :recur
    end
  ensure
    remove_instance_variable :@caller if instance_variable_defined? :@caller
  end
end
```

---

### 3 Phases to Recur

---

### Phase 1: Setup

* setup trampoline
* start trampoline loop
* "recur"

```ruby[3-7,10]
def recur(*args)
  return [:recur, args] if instance_variable_defined? :@caller
  begin
    @caller = binding.of_caller(1).eval('__method__')
    caller_args = [:start, args]
    loop do
      caller_args = __send__ @caller, *caller_args.last
      return caller_args unless caller_args.respond_to? :first
      return caller_args unless caller_args.first == :recur
    end
  ensure
    remove_instance_variable :@caller if instance_variable_defined? :@caller
  end
end
```

---

### Phase 2: Bounce
* add state management args to arg list and return
* short curcuit the recursive call
* allows the stack to pop to the first recur call

```ruby[2]
def recur(*args)
  return [:recur, args] if instance_variable_defined? :@caller
  begin
    @caller = binding.of_caller(1).eval('__method__')
    caller_args = [:start, args]
    loop do
      caller_args = __send__ @caller, *caller_args.last
      return caller_args unless caller_args.respond_to? :first
      return caller_args unless caller_args.first == :recur
    end
  ensure
    remove_instance_variable :@caller if instance_variable_defined? :@caller
  end
end
```

---

### Phase 3: Cleanup
* Never gets to recur
* return results from loop
* clean up management args

```ruby[8-9,11-13]
def recur(*args)
  return [:recur, args] if instance_variable_defined? :@caller
  begin
    @caller = binding.of_caller(1).eval('__method__')
    caller_args = [:start, args]
    loop do
      caller_args = __send__ @caller, *caller_args.last
      return caller_args unless caller_args.respond_to? :first
      return caller_args unless caller_args.first == :recur
    end
  ensure
    remove_instance_variable :@caller if instance_variable_defined? :@caller
  end
end
```

---

**Will The Stack Overflow?**

``` ruby
# What happens when we run this?
factorial(11)
```

---

### Recurring Factorial

```ruby
def factorial(n) # maintain interface
  factorial_helper(1, n)
end

# never call directly
def factorial_helper(accumulator, n)
  return accumulator if n <= 1
  # replace tail call with recur method call
  recur(accumulator * n, n - 1)
end

```

---
<!-- .slide: data-transition="none" -->
**Push factorial(11)**

<img src="/images/1023_Hanoi_Tower_Set5-01.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Push factorial_helper(1, 11)**

<img src="/images/1023_Hanoi_Tower_Set5-02.svg" alt="push full_name" height=500>

---

### Phase 1: Setup

```ruby[3-7,10]
def recur(*args)
  return [:recur, args] if instance_variable_defined? :@caller
  begin
    @caller = binding.of_caller(1).eval('__method__')
    caller_args = [:start, args]
    loop do
      caller_args = __send__ @caller, *caller_args.last
      return caller_args unless caller_args.respond_to? :first
      return caller_args unless caller_args.first == :recur
    end
  ensure
    remove_instance_variable :@caller if instance_variable_defined? :@caller
  end
end
```

---
<!-- .slide: data-transition="none" -->
**Push recur(11, 10)** *(setup)*

<img src="/images/1023_Hanoi_Tower_Set5-03.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Push factorial_helper(11, 10)**

<img src="/images/1023_Hanoi_Tower_Set5-04.svg" alt="push full_name" height=500>

---

### Phase 2: Bounce

```ruby[2]
def recur(*args)
  return [:recur, args] if instance_variable_defined? :@caller
  begin
    @caller = binding.of_caller(1).eval('__method__')
    caller_args = [:start, args]
    loop do
      caller_args = __send__ @caller, *caller_args.last
      return caller_args unless caller_args.respond_to? :first
      return caller_args unless caller_args.first == :recur
    end
  ensure
    remove_instance_variable :@caller if instance_variable_defined? :@caller
  end
end
```

---
<!-- .slide: data-transition="none" -->
**Push recur(110, 9)** *(bounce)*

<img src="/images/1023_Hanoi_Tower_Set5-05.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop recur(110, 9)** *(bounce)*

<img src="/images/1023_Hanoi_Tower_Set5-06.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop factorial_helper(10, 11)**

<img src="/images/1023_Hanoi_Tower_Set5-07.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Push factorial_helper(110, 9)**

<img src="/images/1023_Hanoi_Tower_Set5-08.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Push recur(990, 8)** *(bounce)*

<img src="/images/1023_Hanoi_Tower_Set5-09.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop recur(990, 8)** *(bounce)*

<img src="/images/1023_Hanoi_Tower_Set5-10.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop factorial_helper(110, 9)**

<img src="/images/1023_Hanoi_Tower_Set5-11.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Push factorial_helper(990, 8)**

<img src="/images/1023_Hanoi_Tower_Set5-12.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Push recur(7920, 7)** *(bounce)*

<img src="/images/1023_Hanoi_Tower_Set5-13.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop recur(7920, 7)** *(bounce)*

<img src="/images/1023_Hanoi_Tower_Set5-14.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop factorial_helper(990, 8)**

<img src="/images/1023_Hanoi_Tower_Set5-15.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Push factorial_helper(7920, 7)**

<img src="/images/1023_Hanoi_Tower_Set5-16.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Push recur(55440, 6)**

<img src="/images/1023_Hanoi_Tower_Set5-17.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop recur(55440, 6)**

<img src="/images/1023_Hanoi_Tower_Set5-18.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**Pop factorial_helper(7920, 7)**

<img src="/images/1023_Hanoi_Tower_Set5-19.svg" alt="push full_name" height=500>

---

### Lets move a bit faster
**Can you see the bouncing up and down the stack?**

---
<!-- .slide: data-transition="none" -->
**factorial_helper does some work, recur catches**

<img src="/images/1023_Hanoi_Tower_Set5-20.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**and we bounce back down**

<img src="/images/1023_Hanoi_Tower_Set5-21.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**factorial_helper does some work, recur catches**

<img src="/images/1023_Hanoi_Tower_Set5-22.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**and we bounce back down**

<img src="/images/1023_Hanoi_Tower_Set5-23.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**factorial_helper does some work, recur catches**

<img src="/images/1023_Hanoi_Tower_Set5-24.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**and we bounce back down**

<img src="/images/1023_Hanoi_Tower_Set5-25.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**factorial_helper does some work, recur catches**

<img src="/images/1023_Hanoi_Tower_Set5-26.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**and we bounce back down**

<img src="/images/1023_Hanoi_Tower_Set5-27.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**factorial_helper does some work, recur catches**

<img src="/images/1023_Hanoi_Tower_Set5-28.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**and we bounce back down**

<img src="/images/1023_Hanoi_Tower_Set5-29.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**factorial_helper does some work**

<img src="/images/1023_Hanoi_Tower_Set5-30.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**and we bounce back down**

<img src="/images/1023_Hanoi_Tower_Set5-31.svg" alt="push full_name" height=500>

---

### Phase 3: Cleanup

```ruby[8-9,11-13]
def recur(*args)
  return [:recur, args] if instance_variable_defined? :@caller
  begin
    @caller = binding.of_caller(1).eval('__method__')
    caller_args = [:start, args]
    loop do
      caller_args = __send__ @caller, *caller_args.last
      return caller_args unless caller_args.respond_to? :first
      return caller_args unless caller_args.first == :recur
    end
  ensure
    remove_instance_variable :@caller if instance_variable_defined? :@caller
  end
end
```

---
<!-- .slide: data-transition="none" -->
**We're Done, return the result**

<img src="/images/1023_Hanoi_Tower_Set5-32.svg" alt="push full_name" height=500>

---
<!-- .slide: data-transition="none" -->
**We Did It! We Beat The Stack!**

<img src="/images/1023_Hanoi_Tower_Set5-33.svg" alt="push full_name" height=500>

---

### Torc Gem 0.2.0
##### Tail Optimized Recursive Caller

* The code we just reviewed was 0.1.0
* 0.2.0 added guards against collisions
* and some new tail call optimization techniques
* Possible Cool things coming in 0.3.0
  * guards against non tail-recursive methods
  * real functional trampolines
  * write your tail-recursive method normally
* Probably not production worthy. Use at own risk
* and really, you could and probably should just use a loop

---

### Thank You!
##### please hold applause

**Nathan Hessler**

* twitter: @spune
* github: nhessler
* work: Hessler Consulting

---

**Shout Outs**

* Josh Cheek
* Sean McBeth
* Reginald Braithwaite (raganwald)
* Kat Hubbs

---

## The End

> This is slide 99! hopefully I finished on time
