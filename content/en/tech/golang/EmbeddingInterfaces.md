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
	d.speak();

	v := Animal{}
	v.speak();
}
```

In the above code we declare a type of struct named Dog. There is a method embedded to the Dog struct called speak.

We declare a new struct Animal, which has Dog as one of if its fields. Now, All the methods embedded on the struct Dog can be accessed by creating a variable for the struct type Animal (Line 14).

Though this works fine, the problem here is, the struct Animal has a field Dog hardcoded to it. Suppose we have to include a cat, we have to then alter the struct Animal to include cat. It doesnt end there, we'll also have to modify the variable initialisation to include Cat. Now, since both dog and cat has the function speak we will have to explicitly specify which function are we intending to call. Code including cat is given below.

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
	d.Cat.speak();

}
```

To make it easy for us to swap between different animals, or include multiple animals, we can make use of **Interfaces**.

## Interfaces

#### Step 1: Create a type of interface that encompasses the common functionalites.

```Go
type Language interface {
	speak()
}
```
Now, any type that implements speak function is an implementation of the Language interface.

#### Step 2: Create a type of struct Dog, and implement the functions of the interface.

```Go
type Dog struct {}

func (d Dog) speak() {
	fmt.Println("Woof")
}
```

#### Step 3: Include the interface as a field to the Animal struct

```Go
type Animal struct {
	Language
}
```

Now, any struct that implements the speak function can be initialised to the Language field during declaration. We need not disturb the Animal struct again.

Invoking the speak function for Dog is just a matter of initialising the variable with the struct Animal with any one of the implementations of the Language interface as below.

```Go
d := Animal{Dog{}}
d.speak();
```

Including a new animal is easy. All we have to do is, create a new struct (Ex. Cat), embed a function speak() to the struct.

```Go
type Cat struct {}

func (d Cat) speak() {
	fmt.Println("Woof")
}
```

The realworld advantage to doing this is that, now any time we decide to replace a funcionality, say, we have included some customer specific logic, it is as easy as swapping it with the new struct that implements the interface.

Full code can be found below

```Go
import "fmt"

type Animal struct {
	Language
}

type Dog struct {}

func (d Dog) speak() {
	fmt.Println("Woof")
}

type Cat struct {}

func (d Cat) speak() {
	fmt.Println("Meow")
}

type Language interface {
	speak()
}

func main() {
	d := Animal{Dog{}}
	d.speak();

	c := Animal{Cat{}}
	c.speak();
}
```

In line 23, Dog can be replaced by Cat and just by swapping, we can include functionality of cat instead of dog.

The output is 
{{< boxmd >}}
Woof
Meow
{{< /boxmd >}}

{{< alert theme="success" dir="ltr" >}}
Find the source code here https://gist.github.com/adhithyakrishna/ac72d0d4af806b764d66fda8efec8728
{{< /alert >}}