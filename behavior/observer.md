# Observer \(Dependents, Publish-Subscribe\)

## Introduction: {#introduction}

​Define a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically.

## Motivation & When should use builder pattern {#motivation-and-when-should-use-builder-pattern}

* ​When an abstraction has two aspects, one dependent on the other. Encapsulating   these aspects in separate objects lets you vary and reuse them independently.
* When a change to one object requires changing others, and you don't know   how many objects need to be changed.
* When an object should be able to notify other objects without making assumptions   about who these objects are.

## Component:​ {#component}

![](../.gitbook/assets/image%20%2818%29.png)

![](../.gitbook/assets/image%20%287%29.png)

#### Subject

* Knows its observers.
* Provides an interface for attaching and detaching Observer objects.

#### Observer

* Define an updating interface for objects that should be notified of changes in a subject

#### Concrete Subject

* Store state of interest to Concrete Observer objects
* Sends a notification to its observers when its state changes.

#### Concrete Observer

* Maintains a reference to a Concrete Subject object.
* Stores state that should stay consistent with the subject's.
* Implements Observer updating interface to keep its state consistent with the subject's.

## Code Example - Golang {#code-example-golang}

```go
​​package main

import (
	"fmt"
	"math/rand"
	"reflect"
	"sync"
	"time"
)

type MessageSubject interface {
	Attach(observer MessageObserver)
	Detach(observer MessageObserver)
}

type MessageObserver interface {
	Update(message Message)
}

// Concrete Subject
type Message struct {
	content   string
	UpdatedAt time.Time
	observers []MessageObserver
}

func (msg *Message) Attach(observer MessageObserver) {
	msg.observers = append(msg.observers, observer)
}

func (msg *Message) Detach(observer MessageObserver) {
	for i, v := range msg.observers {
		if reflect.ValueOf(v).String() == reflect.ValueOf(observer).String() {
			msg.observers = append(msg.observers[:i], msg.observers[i+1:]...)
			return
		}
	}
}

func (msg *Message) Notify() {
	for _, o := range msg.observers {
		o.Update(*msg)
	}
}

func (msg *Message) SetContent(str string) {
	msg.content = str
	msg.UpdatedAt = time.Now()
	msg.Notify()
}

// Concrete Observer
type MessageView struct {
	id  string
	msg Message
}

func (view MessageView) Update(msg Message) {
	fmt.Println("Got updated message: ", msg)
}

func NewMessageView(subject MessageSubject) MessageView {
	view := MessageView{
		id: fmt.Sprintf("view-%d", rand.Int()),
	}
	subject.Attach(view)
	return view
}

func NewEmptyMessage() *Message {
	return &Message{}
}

func main() {
	messageSubject := NewEmptyMessage()
	var view MessageObserver = NewMessageView(messageSubject)
	var wg sync.WaitGroup
	done := make(chan struct{})

	wg.Add(1)
	// simulate subject keep updated
	go func() {
		for i := 0; i < 5; i++ {
			messageSubject.SetContent(
				fmt.Sprintf("New Message: %d", rand.Int()),
			)
			<-time.After(time.Second)
		}
		wg.Done()
	}()

	go func() {
		wg.Wait()
		messageSubject.Detach(view)
		messageSubject.SetContent("View should not print this message")
		close(done)
	}()

	<-done

	fmt.Println("Demo DONE")

}

```

**NOTE**:

#### Push based Implementation: _\(make observer less reusable\)_

The subject sends the detailed information about the change in the _**Update\(\)**_

#### Pull based Implementation: _\(inefficient\)_

​The subject sends nothing but most minimal notification message, and observers ask for the details explicitly thereafter.

## ​Related Patterns:

#### Mediator:  

By encapsulating complex update semantics, the Change Manager acts as Mediator between subjects and observers.

#### Singleton:

The Change Manager​ may use the Singleton pattern make it unique and globally accessible

![](../.gitbook/assets/image%20%2811%29.png)

