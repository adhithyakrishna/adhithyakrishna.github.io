---
title: "Embedding Interfaces"
date: 2021-02-08T01:22:47-05:00
draft: false
description : "An article about embedding in golang"
weight: 10
---

This article explains embedding interfaces concept in golang. We first begin by writing the main crux of the code.

```Go
type Animal struct {
	Dog
}

type Dog struct {
}

func (d Dog) speak() {
	fmt.Println("woof")
}

func main() {
	d := Animal{Dog{}}
	d.speak()

	v := Animal{}
	v.speak()
}
```

In the above code, we declare a struct type named Dog. There is a method defined on the Dog struct called `speak`.

We declare a new struct Animal, which has Dog as one of its fields. Now, all the methods defined on the Dog struct can be accessed by creating a variable of type Animal (line 25).

Though this works fine, the problem is that the struct Animal has the Dog type hardcoded into it. If we want to include a Cat, we must alter the struct Animal to include Cat. It doesn't end there—we also have to modify the variable initialization to include Cat. Since both Dog and Cat have the `speak` method, we must explicitly specify which function we intend to call. Code including Cat is shown below:

```Go
type Animal struct {
	Dog
	Cat
}

type Dog struct {
}

func (d Dog) speak() {
	fmt.Println("woof")
}

type Cat struct {
}

func (c Cat) speak() {
	fmt.Println("meow")
}

func main() {
	d := Animal{
		Dog{},
		Cat{},
	}
	d.Cat.speak()
}
```

To make it easy for us to swap between different animals, or include multiple animals, we can make use of **Interfaces**.

## Interfaces

#### Step 1: Create a type of interface that encompasses the common functionalities

```Go
type Language interface {
	speak()
}
```

Now, any type that implements the `speak` method is an implementation of the Language interface.

#### Step 2: Create a struct type Dog and implement the methods of the interface

```Go
type Dog struct{}

func (d Dog) speak() {
	fmt.Println("Woof")
}
```

#### Step 3: Include the interface as a field in the Animal struct

```Go
type Animal struct {
	Language
}
```

Now, any struct that implements the `speak` method can be assigned to the Language field during initialization. We don't need to modify the Animal struct again.

Invoking the `speak` method for Dog is straightforward—just initialize the Animal variable with any implementation of the Language interface:

```Go
d := Animal{Dog{}}
d.speak()
```

Including a new animal is easy. All we have to do is create a new struct (e.g., Cat) and implement the `speak()` method on it:

```Go
type Cat struct{}

func (d Cat) speak() {
	fmt.Println("Meow")
}
```

The real world advantage to doing this is that, now any time we decide to replace a functionality, say, we have included some customer specific logic, it is as easy as swapping it with the new struct that implements the interface.

Full code can be found below:

```Go
package main

import "fmt"

type Animal struct {
	Language
}

type Dog struct{}

func (d Dog) speak() {
	fmt.Println("Woof")
}

type Cat struct{}

func (d Cat) speak() {
	fmt.Println("Meow")
}

type Language interface {
	speak()
}

func main() {
	d := Animal{Dog{}}
	d.speak()

	c := Animal{Cat{}}
	c.speak()
}
```

In the main function (lines 27-32), Dog can be replaced by Cat, and by simply swapping the types, we can include Cat's functionality instead of Dog's.

The output is:
{{< boxmd >}}
Woof
Meow
{{< /boxmd >}}

{{< alert theme="success" dir="ltr" >}}
Find the source code here https://gist.github.com/adhithyakrishna/ac72d0d4af806b764d66fda8efec8728
{{< /alert >}}