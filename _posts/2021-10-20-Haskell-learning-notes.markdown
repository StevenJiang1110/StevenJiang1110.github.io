---
layout: post
title:  "Haskell learning notes"
authors: Jianfeng Jiang
categories: Haskell functional-programming-language 
---

This is brief learning notes of the first six chapters of [Learn-You-Haskell](http://learnyouahaskell.com).

### A brief introduction to Haskell

Haskell is a functional programming language. Compared with imperative languages, executing functions in Haskell will neither change global state nor change the value of parameters. No matter how many times you execute the same function with the same parameters, you will get the same results. So, functions in Haskell is more like functions in Math, rather than in C language. Functions are limited to compute some results and return the results. We may call these functions side-effect-free functions.  

Haskell is lazily computed. Haskell will only compute the result when it's time to show results. That's why infinite list can exist in Haskell.

Haskell is statically typed. All types can be inferred(or manually labelled) by the compiler when compiling Haskell programs.  

### Basic Haskell syntax

I will skip syntax which is common in imperative languages.

- function definition: To define a function is similar to other languages.
    ```Haskell
    -- <function-name> <parameter-list> = <function-context>
    doubleUs x y = x*2 + y*2
    ```
- if statement: In Haskell, if statement is an expression, it must return values of the same type for each branch. So, we cannot miss the else branch.
    ```Haskell
    if x > 100 then x else x*2
    ```
- list and string: string is only syntactic sugar for list of chars.
  There can only be items of the same type in one list.  
  concat two lists:
      ```Haskell
      [1,2,3] ++ [4,5,6] -- [1,2,3,4,5,6]
      'A': " SMALL CAT" -- "A SMALL CAT"
      ```
  get element of index:
      ```Haskell
      "Steve Buscemi" !! 6 -- 'B'
      ```
  infix functions:
      ```Haskell
      4 `elem` [3, 4, 5, 6] -- True
      ```
- ranges:
    `[1..20], ['a'..'z']`  
  indicate steps:
    ```Haskell  
    [2,4..20] -- step=2  
    [20,19..1] -- step=-1
    ```  
  infinite range:  
    `[1..]`
- list comprehension: List comprehension in Haskell is like in Math. 
    ```Haskell
    [x*2 | x <- [1..3], odd x] -- [2,6] 
    [x*y | x <- [1,3], y <- [2,4]] -- [2, 4, 6, 12]  
    ```
- tuples: There can be values of multiple types in a tuple, but of only one type in a list.  

### Types and Typeclasses in Haskell  

Primitive types in Haskell is like those in imperative languages.  
- Function types: split by `->`, the last one is type of return value, the rest ones are types of parameters.  
  ```Haskell
  addThree :: Int -> Int -> Int -> Int  
  addThree x y z = x + y + z  
  ```
- Type variables: like generic types in Rust  
- Type classes: like trait in Rust  
  function defininion with type variable and type classes, `Eq` is used to bind `a`:  
    `(==) :: (Eq a) => a -> a -> Bool`  

### Syntax in Functions
- pattern matching: Pattern matching is used to specify different behaviors based on the input parameter. Unlike rust, pattern matching is not forced to exhaust all possible values. If no branch is satisfied, pattern match will fail and return an exception. So for safety reason, there should be a branch to cover all remaining cases. Like rust, pattern matching can also be used to extract elements from tuples or list comprehension.
    ```Haskell
    lucky :: (Integral a) => a -> String  
    lucky 7 = "LUCKY NUMBER SEVEN!"  
    lucky x = "Sorry, you're out of luck, pal!" 
    ```
- guards: Guards are used to test whether a value conforms to some form. Compared with pattern matching, guards can have more complex conditions, e.g., check the range of float numbers. Guard is like a big if-else tree, while guards are far more readable. The `otherwise` branch is used to return default values. `where clause` is used to bind some computation results to a variable.
    ```Haskell
    bmiTell :: (RealFloat a) => a -> a -> String  
    bmiTell weight height  
        | bmi <= 18.5 = "You're underweight, you emo, you!"  
        | bmi <= 25.0 = "You're supposedly normal. Pffft, I bet you're ugly!"  
        | bmi <= 30.0 = "You're fat! Lose some weight, fatty!"  
        | otherwise   = "You're a whale, congratulations!"  
        where bmi = weight / height ^ 2
    ```
- let clause: the common use is `let <bingdings> in <expression>`. `let clause` is also used to bind some computation results to a variable. The difference between `let bingings` and `where bindings` is that `let bindings` are expressions itself, while `where bindings` are only syntactic construct. That is to say, the `let bindings` will have the same type as that the `<expression>` behind the keyword `in`.
- case expression: `case expression` is used for test . Pattern matching is only syntactic sugar of `case expression`. The difference is that `case expression` can be some part of another expression, while pattern matching cannot.
    ```Haskell
    case <expression> of <pattern> -> <result>  
                         <pattern> -> <result>  
                         <pattern> -> <result>  
                         ...  
    ```

### Recursion

Recursion in haskell is the same as that in imperative languages. Rucursion means that the function will be applied inside its own definition. In Haskell, recursion is a recommended practice of writing codes (stack overflow is seldom witnessed in Haskell programs). The most classical case is quick sort, which is rather simple in Haskell.
    ```Haskell
    quicksort :: (Ord a) => [a] -> [a]  
    quicksort [] = []  
    quicksort (x:xs) =   
        let smallerSorted = quicksort [a | a <- xs, a <= x]  
            biggerSorted = quicksort [a | a <- xs, a > x]  
        in  smallerSorted ++ [x] ++ biggerSorted  
    ```

### Higher order functions

A higher order function is a function that takes a function as a parameter or return a function.

- curried functions: In Haskell, each function can officially only take one parameter. The mechanism that a function can accept multiple parameters is **curried functions**. That is to say, the function receives the first parameter, and then returns a new function. The new function will accept the next parameter and so on until all parameters are accepted.
Functions can be partially applied, that is to say, we can omit the same variable on the right hand on both sides of the equation. For example, the below two statements are equal.
    ```Haskell
    -- definition 1:
    compareWithHundred :: (Num a, Ord a) => a -> Ordering  
    compareWithHundred x = compare 100 x  
    -- definition 2:
    compareWithHundred :: (Num a, Ord a) => a -> Ordering  
    compareWithHundred = compare 100 
    ```
Infix functions can also be partially applied.

- higher-orderism: Type definition is naturally right-associative. However, if some part of type definition is a function, using parentheses will make code more readable. For example,
    ```Haskell
    applyTwice :: (a -> a) -> a -> a  
    applyTwice f x = f (f x)  
    ```
- Maps and filters: `map` and `filter` are powerful functions in Haskell. `map` receives a list and a function, then perform the function on each item of the list, and then collects all results to return a new list. `filter` receives a list and a predicate and then return only elements that satisfy the predicate in the list as a new list. `takeWhile` takes a predicate and a list and then goes from the beginning of the list and returns its elements while the predicate holds true

- Lambdas: lambdas are anonymous functions that are used only once. Lambda begins with `\`, then parameters seperated by space. After that comes a `->` and then the function body. A typical lambda is like `\<parameters> -> <function context>`.

- fold: **Fold** is also a common function in Haskell. **Fold** accepts a list, an init value(acc) and a binary function. **Fold** will call the function on the first item and acc to produce a new acc. Then, the function is called again on the second item and the new acc, and so on until we walk over the whole list. **Fold** will reduce the whole list to a single value.

- function appilication with `$`: The `$` is defined as follows.
    ```Haskell
    ($) :: (a -> b) -> a -> b  
    f $ x = f x
    ```  
Normal function application has a high precedence, while the `$` function has the lowest precedence. So, function application with a space is left-associative (so `f a b c` is the same as `((f a) b) c))`, function application with `$` is right-associative. The `$` is useful in change the order of function combination without parentheses. 
    ```Haskell
    -- equal expression
    sum (map sqrt [1..130])
    sum $ map sqrt [1..130]
    -- equal expression
    sqrt (3 + 4 + 9)
    sqrt $ 3 + 4 + 9
    ```
- function composition: function composition will compose several functions to return a new function. It is similer to function composition in Math. 
    ```Haskell
    (.) :: (b -> c) -> (a -> b) -> a -> c  
    f . g = \x -> f (g x) 
    ```
A simple example is
    ```Haskell
    -- equal expression
    map (\x -> negate (abs x)) [5,-3,-19,24]
    map (negate . abs) [5,-3,-19,24]
    ```