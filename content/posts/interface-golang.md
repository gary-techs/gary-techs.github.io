+++
title = "Interfaces and Reflection in Go "
date = 2020-01-31T16:53:14+04:00
images = []
tags = []
categories = []
draft = true
+++

## Interfaces
Interfaces are abstract data types. They are used to describe all the methods for a particular **method set** and the signatures for the methods. Suppose we have couple of _structs_ Banana and Apple. They both implement an interface called Fruit. A Fruit can have a color and lets say price.

```golang
//Fruit Interface
type Fruit interface {
    getColor() string
    getPrice() int
}
```

In order for Banana struct to be a fruit it has to implement two methods getColor and getPrice.

```golang
//Banana Struct
type Banana struct {
    color string
    price int
}

func (b Banana) getColor() string {
    return ("Color of a Banana is " + b.color)
}

func (b Banana) getPrice() int {
    return b.price
}
```

Same goes for Apple Struct
```golang
//Apple Struct
type Apple struct {
	color string
	price int
}

func (a Apple) getColor() string {
	return ("Color of an Apple is " + a.color)
}

func (a Apple) getPrice() int {
	return a.price
}
```

We can test this by following code 
```golang
func main() {
	var f1 Fruit = Apple{"Red", 10}
	fmt.Println(f1.getColor())

	var f2 Fruit = Banana{"Yellow", 12}
	fmt.Println(f2.getColor())
}
```
We get the following result

```bash
➜  interfaces go run interface.go
Color of an Apple is Red
Color of a Banana is Yellow
```
## Reflection 
How about infering the type of the passing struct dynamically.  We can do that using the Reflection, it's the ability of a language to extract dynamic type information by calling TypeOf. A call to ValueOf returns a Value representing the run-time data. Zero takes a Type and returns a Value representing a zero value for that type.

```golang
func formattedPrice(f Fruit) string {

	price := strconv.Itoa(f.getPrice())
	var result string
	
	//Infer the type of Fruit by using reflection
	switch fruitName := (reflect.TypeOf(f)).Name(); fruitName {
	case "Apple":
		result = "Price of an Apple is " + price
	case "Banana":
		result = "Price of a Banana is " + price
	}
	return result
}

```
TypeOf tells the type and Name will give us the name of the struct. In order to determine the type of objects we can use reflection.

Here is the full code 
```golang
package main

import (
	"fmt"
	"reflect"
	"strconv"
)

//Fruit Interface
type Fruit interface {
	getColor() string
	getPrice() int
}

//Banana Struct
type Banana struct {
	color string
	price int
}

func (b Banana) getColor() string {
	return ("Color of a Banana is " + b.color)
}

func (b Banana) getPrice() int {
	return b.price
}

//Apple Struct
type Apple struct {
	color string
	price int
}

func (a Apple) getColor() string {
	return ("Color of an Apple is " + a.color)
}

func (a Apple) getPrice() int {
	return a.price
}

func formattedPrice(f Fruit) string {

	price := strconv.Itoa(f.getPrice())
	var result string
	
	//Infer the type of Fruit by using reflection
	switch fruitName := (reflect.TypeOf(f)).Name(); fruitName {
	case "Apple":
		result = "Price of an Apple is " + price
	case "Banana":
		result = "Price of a Banana is " + price
	}
	return result
}

func main() {
	var f1 Fruit = Apple{"Red", 10}
	fmt.Println(f1.getColor())

	var f2 Fruit = Banana{"Yellow", 12}
	fmt.Println(f2.getColor())

	fmt.Println(formattedPrice(f1))
	fmt.Println(formattedPrice(f2))
}

```