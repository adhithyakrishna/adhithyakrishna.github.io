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


Now, Let us say, we work for a client who wants add a functionality. It may be something like, adding a prefix to the language spoken by the animal.

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

Now Inititator struct implements the language interface. Since it includes, Language interface as one of its fields, chaining is very easy to do.


```Go
    c := Animal{Initiator{Cat{}}}
    c.speak()
```

The Initiator functionality has been chained into the variable declaration. Now everytime a speak function is called on the intialised variable, first, the speak function in the initiator struct would be called and then, the speak function in the Cat struct would take place.


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

The output is 
{{< boxmd >}}
Woof
The animal says : Meow
{{< /boxmd >}}

{{< alert theme="success" dir="ltr" >}}
Find the source code here https://gist.github.com/adhithyakrishna/ad5cb9b6f4ce1d5bb98407f9502e51ee
{{< /alert >}}
