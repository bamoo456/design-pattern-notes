# Chain of Responsibility

## Introduction: {#introduction}

Avoid coupling the sender of a request to its receiver by giving more than one object a chance to handle the request.  
Chain the receiving objects and pass the request along the chain until an object handles it.

{% hint style="info" %}
Objects in the chain need to share a same common interface.
{% endhint %}

## Motivation & When should use builder pattern {#motivation-and-when-should-use-builder-pattern}

* ​Use the Chain of Responsibility when more than one object may handle a request, and the handler isn't known a `priority`.
* You want to issue a request to one of several objects without specify the receiver directly.
* The set of objects that can handle a request should be specified dynamically.
* You want to make the handler shareable for different kind of request. `(e.g. Http middleware)`

## Component: {#component}

​

![](../.gitbook/assets/image%20%286%29.png)

#### Handler

* Define a interface for handling a request
* \(optional\) Implement a successor link.

#### Concrete Handler

* Handles requests it is responsible for.
* Can access its successor.
* Based on the request it receives, it will handle it or pass the request to the successor.

#### Client

* initiates the requests to a Concrete Handler object on the chain.

## Consequences: {#code-example-golang}

#### Reduced coupling

#### Added flexibility in assigning responsibilities to objects.

#### Receipt isn't guaranteed

## Code Example - Golang {#code-example-golang}

```go
​​package main

import "fmt"

type Request struct {
	Type string
}

type Handler interface {
	Handle(*Request)
	Next(*Request)
}

type BasicHandler struct {
	successor Handler
}

func (h BasicHandler) Next(r *Request) {
	if h.successor != nil {
		h.successor.Handle(r)
	}
}

func (h BasicHandler) Handle(r *Request) {
	if r.Type == "Basic" {
		fmt.Println("[BasicHandler] handling this request")
	} else {
		fmt.Println("[BasicHandler] skip this request")
	}
	h.Next(r)
}

type AuthHandler struct {
	Handler
}

func (h AuthHandler) Handle(r *Request) {
	if r.Type == "Auth" {
		fmt.Println("[AuthHandler] handling this request")
	} else {
		fmt.Println("[AuthHandler] skip this request")
	}
	h.Next(r)
}

func NewBasicHandler(successorHandler Handler) Handler {
	return BasicHandler{
		successor: successorHandler,
	}
}

func NewAuthHandler(successorHandler Handler) Handler {
	return AuthHandler{
		Handler: NewBasicHandler(successorHandler),
	}
}

func main() {
	req := &Request{Type: "Basic"}

	chain := NewBasicHandler(NewAuthHandler(nil))
	chain.Handle(req)
}

```

**NOTE**:

[https://sourcemaking.com/design\_patterns/chain\_of\_responsibility](https://sourcemaking.com/design_patterns/chain_of_responsibility)

## ​Related Patterns:

#### Composite {#adapter}



​

​

