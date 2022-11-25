---
title: "Function types"
date: 2021-02-08T18:50:23-05:00
draft: false
description : "An article about function types in golang"
weight : 20
---

Golang lets us declare a variable of type functions. In Golang functions are first class citizens. In this article we are going to see how the functions can be used a type in golang.

#### Declaration of the variable

```Go
type validator func(*User) error
```

validator is the name of the variable that has a type function which takes the struct User as its argument and returns an error.


#### Declaration of struct and functions on the struct

```Go
type userValidator struct {}

func (uv *userValidator)validateEmail(u *User) error {
	matched, _ := regexp.MatchString(`^[a-z0-9._%+\-]+@[a-z0-9.\-]+\.[a-z]{2,16}$`, u.email)
	if !matched {
		return ErrEmailInvalid
	}
	return nil
}

func (uv *userValidator) isEmpty(u *User) error {
	if u.email == "" {
		return ErrEmailEmpty
	}
	return nil
}
```

If we observe, we can note that the function argument as well the return type matches the variable we declared above. Now we can pass validator as an argument to different functions and execute them. Below is an example.

```Go
func runUserValidations(user *User, fns ...validator) error {
	for _, fns := range fns {
		if err:=fns(user); err != nil {
			return err
		}
	}
	return nil
}
```

You can see that, validator is passed as an argument to the runUserValidations functions. The function can be executed by passing an instance of user struct as an argument.

I have included an example below of how using function types will come in handy while doing multiple functions that validates if an email is valid.

```Go
package main

import (
	"errors"
	"fmt"
	"regexp"
)

var (
	ErrEmailInvalid = errors.New("email address is not valid")
	ErrEmailEmpty = errors.New("email address is empty")
)
type User struct {
	email string
}

type userValidator struct {}

type validator func(*User) error

func (uv *userValidator)validateEmail(u *User) error {
	matched, _ := regexp.MatchString(`^[a-z0-9._%+\-]+@[a-z0-9.\-]+\.[a-z]{2,16}$`, u.email)
	if !matched {
		return ErrEmailInvalid
	}
	return nil
}

func (uv *userValidator) isEmpty(u *User) error {
	if u.email == "" {
		return ErrEmailEmpty
	}
	return nil
}

func runUserValidations(user *User, fns ...validator) error {
	for _, fns := range fns {
		if err:=fns(user); err != nil {
			return err
		}
	}
	return nil
}

func main() {
	u := User{
		email: "adhithya.awesome@gmail.com",
	}

	uv := userValidator{}

	err:= runUserValidations(&u, uv.isEmpty, uv.validateEmail)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("valid email")
}
```

If you note line number 52 in the above code you can see how an instance of user struct, initialised with an email is passed to different functions, validated and if there is an error in the validation it prints the error or it prints the email is valid.

We can included multiple such functions and calling them is a matter of including them to the runUserValidations functions.