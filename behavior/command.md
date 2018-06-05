# Command

## Introduction: {#introduction}

​Encapsulate a request as an object, thereby letting you parameterize clients with different requests, queue or log requests, and support undoable operations

## Motivation & When should use builder pattern {#motivation-and-when-should-use-builder-pattern}

* ​Parameterize objects by an action to perform. \(callback\)
* Specify, queue, and execute requests at different times.
* Support undo. \(Command history: Commands + Composite\)
* Support logging changes so that they can be reapplied in case of a system crash.
* Structure a system around high-level operations built on primitives operations.

## Component: {#component}

​

![](../.gitbook/assets/image%20%289%29.png)

#### Command:

* Declares an interface for executing operation.

#### Concrete Command:

* Defines a binding between a Receiver Object and an action.
* Implements Execute by invoking the corresponding operations on Receiver.

#### Client:

* Creates a Concrete Command object and sets its receiver.

#### Invoker:

* Asks the command to carry out the requests.

#### Receiver:

* Knows how to perform the operations associated with carrying out a request. \(Any class may serve as a Receiver\)

## Code Example - Golang {#code-example-golang}

```go
package main

import (
	"bytes"
	"fmt"
	"net"
	"net/http"
	"sync"
	"time"
)

type Command interface {
	Execute() error
}

type HttpPostCommand struct {
	uri     string
	payload []byte
}

func (cmd HttpPostCommand) Execute() error {
	fmt.Println("Executing HttpPostCommand ...", cmd.uri)
	_, err := http.Post(cmd.uri, "application/json", bytes.NewReader(cmd.payload))
	return err
}

type TcpSendCommand struct {
	conn    net.Conn
	payload []byte
}

func (cmd TcpSendCommand) Execute() error {
	fmt.Println("Executing TcpSendCommand ...", cmd.conn.RemoteAddr)
	_, err := cmd.conn.Write(cmd.payload)
	return err
}

func main() {
	var wg sync.WaitGroup
	execCh := make(chan Command)
	done := make(chan struct{})

	wg.Add(2)
	go func() {
		defer wg.Done()
		conn, _ := net.Dial("tcp", "golang.org:80")
		for i := 0; i < 3; i++ {
			execCh <- TcpSendCommand{
				conn:    conn,
				payload: []byte("Hello"),
			}
			<-time.After(500 * time.Millisecond)
		}
	}()

	go func() {
		defer wg.Done()
		uri := "https://google.com"
		for i := 0; i < 3; i++ {
			execCh <- HttpPostCommand{
				uri:     uri,
				payload: []byte("World"),
			}
			<-time.After(500 * time.Millisecond)
		}
	}()

	go func() {
		wg.Wait()
		close(execCh)
	}()

	go func() {
		defer func() { done <- struct{}{} }()
		for cmd := range execCh {
			cmd.Execute()
		}
	}()

	<-done
	fmt.Println("Demo Done")
}

```

## Related Patterns: {#related-patterns}

#### Composite

#### Memento

#### Prototype



​

​

