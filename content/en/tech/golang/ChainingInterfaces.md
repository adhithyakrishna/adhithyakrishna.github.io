---
title: "Chaining Interfaces"
date: 2021-02-08T03:40:59-05:00
draft: false
description : "An article about Chaining interfaces in golang"
weight : 15
---

This article is a continuation to the previous article about Embedding interfaces found [here](/articles/golang/embeddinginterfaces/)	

We start from the crux of the previous code.

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
Now, let's say we work for a client who wants to add a functionality. It may be something like adding a prefix to the language spoken by the animal.

To do that, we can make use of the concept called interface chaining.

#### Declare a type struct that includes the interface as one of its field. It should also include implementations of the speak function.

```Go
type Initiator struct {
	Language
}

func (i Initiator) speak() {
	fmt.Print("The animal says : ", i)
	i.Language.speak()
}
```

Now the Initiator struct implements the Language interface. Since it includes the Language interface as one of its fields, chaining is very easy to do.
```Go
    c := Animal{Initiator{Cat{}}}
    c.speak()
```

The Initiator functionality has been chained into the variable declaration. Now every time a speak function is called on the initialized variable, the speak function in the Initiator struct is called first, and then the speak function in the Cat struct is called.


Full code is given below:
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

type Initiator struct {
	Language
}

func (i Initiator) speak() {
	fmt.Print("The animal says : ")
	i.Language.speak()
}

func main() {
	d := Animal{Dog{}}
	d.speak()

	c := Animal{Initiator{Cat{}}}
	c.speak()

}
```

The output is:
{{< boxmd >}}
Woof
The animal says : Meow
{{< /boxmd >}}

{{< alert theme="success" dir="ltr" >}}
Find the source code here https://gist.github.com/adhithyakrishna/ad5cb9b6f4ce1d5bb98407f9502e51ee
{{< /alert >}}
