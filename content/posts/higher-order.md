+++
title = "Higher Order Functions, Closures in Go"
date = 2020-02-02T16:53:14+04:00
images = []
tags = []
categories = []
draft = true
+++
## Intro
According to wiki a Higher-order function is something that does at least one of the following:-
* takes one or more functions as arguments
* returns a function as its result

Why would we need a HOF? Well think about all the repetetive tasks we do when iterating over any data set. We could be filtering the data set by applying different filters. It could be about mapping an old list to a new list etc. Let's start by filtering an array, where we need to find all the even elements.

```golang
func even(list []int) []int {
	result := make([]int, 0)
	for _, i := range list {
		if i%2 == 0 {
			result = append(result, i)
		}
	}
	return result
}
```
We could write a generic functino filter that can take different filters as functional arguements and do the same for us.

```golang
func filter(list []int, applyFilter func(elem int) bool) []int {
    result := make([]int, 0)
    for _, i := range list {
        if applyFilter(i) {
            result = append(result, i)
        }
    }
    return result
}
```
So applyFilter is the filter that want to be applied on the list. We can pass the even filter by rewriting it as a function which returns true or false. Since functions are first class citizens in Go which means they can be passed as normal arguements in Go. Let's take a look at evenFilter here we assign the variable evenFilter the 
```golang
func main() {
	evenFilter := func(e int) bool {
		if e%2 == 0 {
			return true
		}
		return false
	}

	b := []int{10, 22, 31, 41, 58, 61, 72, 80}
	fmt.Println(even(b))
	fmt.Println(filter(b, evenFilter))
}
```
Both print the same output 
```bash
➜  hof go run hof.go
[10 22 58 72 80]
[10 22 58 72 80]
```


Next we could have a filter where we need all integers greater than a certain value let's say 36. We could write certain filter like
```golang
 
func greaterThan36(list []int) []int {
	result := make([]int, 0)
	for i := range list {
		if i > 36 {
			result = append(result, i)
		}
	}
	return result
}
```
# Closures
We can remove the cumbersome greaterThan36 by something cooler and generic. Let's write a higher order function which uses closures property and returns a dynamic function. We can have greaterThanN function which takes **N** as parameter and returns a function which we will be using.
```golang
//Anonymous function returns a new generic function
greaterThanN := func(n int) func(int) bool {
    f := func(i int) bool {
        if i > n {
            return true
        }
        return false
    }
    return f
}
```
We can now create as many greater than functions using this generic function. We can rewrite the application code again as following
```golang
package main

import (
	"fmt"
)

func main() {
	b := []int{10, 22, 31, 41, 58, 61, 72, 80}

	//Anonymous function returns a new generic function
	greaterThanN := func(n int) func(int) bool {
		f := func(i int) bool {
			if i > n {
				return true
			}
			return false
		}
		return f
	}

	//Anonymous function which returns bool based on args value
	evenFilter := func(e int) bool {
		if e%2 == 0 {
			return true
		}
		return false
	}

	fmt.Println(even(b))
	fmt.Println(filter(b, evenFilter))

	fmt.Println(greaterThan36(b))
	//creating a new function from another HOF
	greaterThan36Func := greaterThanN(36)
	fmt.Println(filter(b, greaterThan36Func))
}

func even(list []int) []int {
	result := make([]int, 0)

	for _, i := range list {
		if i%2 == 0 {
			result = append(result, i)
		}
	}
	return result
}

func greaterThan36(list []int) []int {
	result := make([]int, 0)
	for _, i := range list {
		if i > 36 {
			result = append(result, i)
		}
	}
	return result
}

func filter(list []int, applyFilter func(elem int) bool) []int {
	result := make([]int, 0)
	for _, i := range list {
		if applyFilter(i) {
			result = append(result, i)
		}
	}
	return result
}

```