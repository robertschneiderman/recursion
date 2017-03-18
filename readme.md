# Recursion

## Recursive methods

A **recursive** method is one that calls itself. Each time the method
calls itself, it tries to solve a smaller **subproblem**. Think of
this as breaking a large problem into smaller problems. Each of these
may be divided again into still smaller subproblems.

The subproblems keep getting smaller and smaller, until they are small
enough to solve trivially and directly. These small subproblems are
called **base cases**. All recursive methods should eventually reach a
base case, lest they just loop forever.

The factorial operation is one example of a problem that can be solved
recursively. Recall that five factorial is:

    5! = 5 * 4 * 3 * 2 * 1
    n! = n * (n - 1) * (n - 2) * ... * 1

We can also define factorial *recursively*:

    # this is math, not Ruby code.
    0! = 1
    n! = n * (n - 1)!

The first equation is the base case: `0!` is, trivially, `1`. For any
`n > 1`, we can use the second equation to break the problem into a
smaller one.

Let's implement this recursive definition:

```ruby
def factorial(n)
  if n == 0 # the base case
    1
  else # the recursive case
    n * factorial(n - 1)
  end
end
```

Let's walk through how this method is evaluated for `factorial(3)`

```ruby
factorial(3)
=> 3 * factorial(2)
=> 3 * 2 * factorial(1)
=> 3 * 2 * 1 * factorial(0)
=> 3 * 2 * 1 * 1
=> 6
```

## Mathematical induction

Recursion allows us to solve larger problems by breaking them up into similar,
smaller sub-problems that we can solve. Solving a problem using the solution(s)
to these smaller sub-problems uses an important concept called *mathematical
induction*, or simply *induction*.

Generally used in mathematical proofs, mathematical induction tells us that if
we can solve for a base case and we can solve for the general (or nth) case,
then we have solved for all the cases.

Here's an example:
I know that if I can count from `1` to `2`, by incrementing `1` by `1` (`1`
being the base case), I can count to `n+1` by (a) starting at `n` and (b) then
counting one more, `n+1`. Since I know how to count to `2` from `1` (just say
"one"; the base case), I know that I can count to `n` or any other number, no
matter how large.

The key to applying induction to solve problems is to (a)
identify how you can grow a smaller solution into a bigger solution,
and (b) identify base cases which will be the "foundation" of your
tower of recursion.

## Recursion vs iteration

Recursive methods can always be written *iteratively*: using loops
and no recursion. Let's try our hand at the factorial method:

```ruby
def factorial(n)
  result = 1
  (1..n).each do |i|
    result *= i
  end

  result
end
```

While any recursive method can always be written
iteratively, the iterative form can sometimes be
significantly more complicated. When choosing whether to solve a
problem recursively or iteratively, you should think about which
solution is more natural to your own mind.

## Recursion and Infinite Loops

Recursive calls must always make progress toward a base case. For
instance, this is no good:

```ruby
def fibonacci(n)
  case n
  when 1
    [1]
  when 2
    [1, 1]
  else
    # uh-oh. calls the same method!
    fibonacci(n)
  end
end
```

This recursion will never terminate for `n > 2`; it will just call
itself again and again. An infinite chain of recursive calls won't
just spin forever like a normal loop:

```ruby
[2] pry(main)> fibonacci(1)
=> [1]
[3] pry(main)> fibonacci(2)
=> [1, 1]
[4] pry(main)> fibonacci(3)
SystemStackError: stack level too deep
        from: /Users/ruggeri/.rvm/gems/ruby-1.9.3-p194/gems/pry-0.9.10/lib/pry/pry_instance.rb:275
```

Each nested method call you make adds to the **stack** of open method
calls; each time a method ends, the stack size decreases by one. The
elements of the stack are called **stack frames**, and they contain
the local variables used by that method.

If you get caught in a recursive loop, the stack will grow infinitely
until the system runs out of memory. This is because our methods
depend on some method closing to close themselves (i.e., the base
case) in the Fibonacci example. Running out of memory like this is
called a **stack overflow**, and Ruby will tell you that the stack
level got too deep.

## Strategies for Programming Recursively

It helps to have a general strategy with which to tackle recursive
problems.

**Map out a recursive decomposition**: Think about how the problem
breaks down recursively. That is, how will you reduce the problem size
towards the base case.  It's sometimes helpful to even sketch it out.

**Identify the base case(s)**: The base case will be the case when the
stack stops growing - that is, when the chain of deferred operations
will begin to evaluate. What will your method return in the most
trivial of cases? Your recursive decomposition should always be moving
towards that trivial case.

**Think one level up from the base case**: What will happen when you
call your recursive method with a value that will require a single
recursive call - that is, with a value one away from the base
case. How will you have to manipulate that value in order to return
the right thing? Ensure your manipulation generalizes.

**Ensure that your return values from any case (base case or
otherwise) are always of the same type**. If you need an array as the
final return value then the intermediate return values must also be
arrays. Why? Because each of those intermediate values are returning
from the same method!

**Get a stack trace**. Ruby won't normally print a stack trace for stack
overflow errors, because this might print several thousand lines to the screen.
If you're having a hard time debugging a stack overflow, you can paste the
following code snippet at the top of your file. The snippet will intentionally
crash your program before the stack overflow occurs, allowing you to read the
stack trace.

```rb
MAX_STACK_SIZE = 200
tracer = proc do |event|
  if event == 'call' && caller_locations.length > MAX_STACK_SIZE
    fail "Probable Stack Overflow"
  end
end
set_trace_func(tracer)
```






Recursion
Estimated time: 5hrs

Learning Goals

Know how to reason about recursive programs
Be able to write a base case for a recursive method
Be able to write the inductive step for a recursive method
Be able to explain the benefits of writing a method recursively vs. iteratively
Be able to trace a recursive method and figure out how many recursive steps it will take
Know how to write recursive sorting and searching algorithms
Warmup

Write a recursive method, range, that takes a start and an end and returns an array of all numbers between. If end < start, you can return the empty array.
Write both a recursive and iterative version of sum of an array.
Exponentiation

Write two versions of exponent that use two different recursions:

# this is math, not Ruby methods.

# recursion 1
exp(b, 0) = 1
exp(b, n) = b * exp(b, n - 1)

# recursion 2
exp(b, 0) = 1
exp(b, 1) = b
exp(b, n) = exp(b, n / 2) ** 2             [for even n]
exp(b, n) = b * (exp(b, (n - 1) / 2) ** 2) [for odd n]
Note that for recursion 2, you will need to square the results of exp(b, n / 2) and (exp(b, (n - 1) / 2). Remember that you don't need to do anything special to square a number, just calculate the value and multiply it by itself. So don't cheat and use exponentiation in your solution.

Suggestion: Break the methods down into parts.

Suggestion 2: Try walking through your code with Simple Examples.

If the n == 256, about how many nested recursive steps will we run in the first case?

How deep will we need to recurse for the second? Keep in mind that the first reduces the exponent by one for each recursion, while the second reduces it by half.

In addition to testing your methods by running the code, try the following exercise:

On paper, write out the value of each variable for every line in the code:

Write out what happens with base 0 and power 0 as inputs (should be easy). e.g., if you had run exp(0,0).
Write out what happens for base 0 and power 1. e.g., if you had run exp(0,1).
Write out what happens for base 1 and power 0.
Write out what happens for base 1 and power 1.
Write out what happens for base 1 and power 2.
Write out what happens for base 2 and power 0.
Write out what happens for base 2 and power 1.
Write out what happens for base 2 and power 2.
Make sure you can trace from the very beginning to the very end in these examples.

How many examples do you need to walk through to be confident that it works?

Deep dup

The #dup method doesn't make a deep copy:

robot_parts = [
  ["nuts", "bolts", "washers"],
  ["capacitors", "resistors", "inductors"]
]

robot_parts_copy = robot_parts.dup

# shouldn't modify robot_parts
robot_parts_copy[1] << "LEDs"
# but it does
robot_parts[1] # => ["capacitors", "resistors", "inductors", "LEDs"]
When we dup an Array, it creates a new array to hold the elements, but doesn't recursively dup any arrays contained therein. So the dup method creates one new array, but just copies over references to the original interior arrays.

Sometimes you want a shallow dup and sometimes you want a deep dup. Ruby keeps things simple by giving you shallow dup, and letting you write deep dup yourself.

Using recursion and the is_a? method, write an Array#deep_dup method that will perform a "deep" duplication of the interior arrays.

Note: For simplicity's sake, we are only going to ensure the deep duplication of arrays. Don't worry about deep-duping (or regular-duping) other types of mutable objects (like strings, hashes, instances of custom classes, etc.), since this would require that we implement a deep dup method for each of those classes, as well.

It's okay to iterate over array elements using the normal each for this one :-)

You should be able to handle "mixed" arrays. For instance: [1, [2], [3, [4]]].

Fibonacci

Write a recursive and an iterative Fibonacci method. The method should take in an integer n and return the first n Fibonacci numbers in an array.

You shouldn't have to pass any arrays between methods; you should be able to do this just passing a single argument for the number of Fibonacci numbers requested.

Array Subsets

Write a method subsets that will return all subsets of an array.

subsets([]) # => [[]]
subsets([1]) # => [[], [1]]
subsets([1, 2]) # => [[], [1], [2], [1, 2]]
subsets([1, 2, 3])
# => [[], [1], [2], [1, 2], [3], [1, 3], [2, 3], [1, 2, 3]]
You can implement this as an Array method if you prefer.

Hint: For subsets([1, 2, 3]), there are two kinds of subsets:

Those that do not contain 3 (all of these are subsets of [1, 2]).
For every subset that does not contain 3, there is also a corresponding subset that is the same, except it also does contain 3.
Permutations

Write a recursive method permutations(array) that calculates all the permutations of the given array. For an array of length n there are n! different permutations. So for an array with three elements we will have 3 * 2 * 1 = 6 different permutations.

permutations([1, 2, 3]) # => [[1, 2, 3], [1, 3, 2],
                        #     [2, 1, 3], [2, 3, 1],
                        #     [3, 1, 2], [3, 2, 1]]
You can use Ruby's built in Array#permutation method to get a better understanding of what you will be building.

[1, 2, 3].permutation.to_a  # => [[1, 2, 3], [1, 3, 2],
                            #     [2, 1, 3], [2, 3, 1],
                            #     [3, 1, 2], [3, 2, 1]]
Binary Search

The binary search algorithm begins by comparing the target value to the value of the middle element of the sorted array. If the target value is equal to the middle element's value, then the position is returned and the search is finished. If the target value is less than the middle element's value, then the search continues on the lower half of the array; or if the target value is greater than the middle element's value, then the search continues on the upper half of the array. This process continues, eliminating half of the elements, and comparing the target value to the value of the middle element of the remaining elements - until the target value is either found (and its associated element position is returned), or until the entire array has been searched (and "not found" is returned).

Write a recursive binary search: bsearch(array, target). Note that binary search only works on sorted arrays. Make sure to return the location of the found object (or nil if not found!). Hint: you will probably want to use subarrays.

This your first problem which is half a PITA to solve iteratively.

Make sure that these test cases are working:

bsearch([1, 2, 3], 1) # => 0
bsearch([2, 3, 4, 5], 3) # => 1
bsearch([2, 4, 6, 8, 10], 6) # => 2
bsearch([1, 3, 4, 5, 9], 5) # => 3
bsearch([1, 2, 3, 4, 5, 6], 6) # => 5
bsearch([1, 2, 3, 4, 5, 6], 0) # => nil
bsearch([1, 2, 3, 4, 5, 7], 6) # => nil
Merge Sort

Implement a method merge_sort that sorts an Array:

The base cases are for arrays of length zero or one. Do not use a length-two array as a base case. This is unnecessary.
You'll want to write a merge helper method to merge the sorted halves.
To get a visual idea of how merge sort works, watch this gif and check out this diagram.
Make Change

RubyQuiz: Make change.

Here's a game plan for solving the problem:

First, write a 'greedy' version called greedy_make_change:

Take as many of the biggest coin as possible and add them to your result.
Add to the result by recursively calling your method on the remaining amount, leaving out the biggest coin, until the remainder is zero.
Once you have a working greedy version, talk with your partner about refactoring this to make_better_change. What's wrong with greedy_make_change?

Consider the case of greedy_make_change(24, [10,7,1]). Because it takes as many 10 pieces as possible, greedy_make_change misses the correct answer of [10,7,7] (try it in pry).

To make_better_change, we only take one coin at a time and never rule out denominations that we've already used. This allows each coin to be available each time we get a new remainder. By iterating over the denominations and continuing to search for the best change, we assure that we test for 'non-greedy' uses of each denomination.

Discuss the following game plan and then work together to implement your new method:

Iterate over each coin.
Grab only one of that one coin and recursively call make_better_change on the remainder using coins less than or equal to the current coin.
Add the change for the remainder to the single coin you originally grabbed to obtain a possible solution.
Keep track of the best solution and return it at the end.
N.B. Don't generate every possible permutation of coins and then compare them. Remember that a permutation is not the same thing as a combination - we will need to check every combination of coins that add up to our target, we just don't want to check the same combination in different orders. If you get stuck you can start by writing a solution that calculates and compares all of the permutations without storing them in an array. Then go back and refactor your solution so that it only calculates and compares all of the different combinations.

Make sure you and your partner understand each line before moving on.

