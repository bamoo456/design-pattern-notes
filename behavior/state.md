---
description: State Machine
---

# State \(Object for States\)

## Introduction: {#introduction}

Allow an object to alter its behavior when its internal state changes. The object will appear to change its class.

## Motivation & When should use builder pattern {#motivation-and-when-should-use-builder-pattern}

* ​An object's behavior depends on its state, and it must changes its behavior at runtime depending on that state.
* Operations have large, multipart conditional statements depending on the object states.  _\(means if we use multiple if-else conditional statement to decide the object state and execute its behavior\)._

## Component: {#component}

​

![](../.gitbook/assets/image%20%2815%29.png)

#### Context \(TCP Connection\)

* Defines the interface of interest to clients.
* Maintains an instance of a Concrete State subclass that defines the current state.

#### State \(TCP State\)

* Defines an interface for encapsulating the behavior associated with particular state of the Content.

#### Concrete State subclass

* Each subclass implements a behavior associated with a state of the Context.

## Code Example - Golang {#code-example-golang}

```go
​​package main

import (
	"fmt"
)

type TCPState interface {
	Status() string
	Open(conn *TCPConnection)
	Close(conn *TCPConnection)
	Send(conn *TCPConnection, payload []byte)
}

// TCP Connection
type TCPConnection struct {
	state TCPState
}

func (conn *TCPConnection) Status() string {
	return conn.state.Status()
}

func (conn *TCPConnection) Open() {
	conn.state.Open(conn)
}

func (conn *TCPConnection) Close() {
	conn.state.Close(conn)
}

func (conn *TCPConnection) Send(payload []byte) {
	conn.state.Send(conn, payload)
}

func (conn *TCPConnection) ChangeState(state TCPState) {
	conn.state = state
}

// TCP State
type TCPNull struct{}

func (state TCPNull) Status() string {
	return "NULL"
}

func (state TCPNull) ChangeState(conn *TCPConnection, newState TCPState) {
	conn.ChangeState(newState)
}

func (state TCPNull) Open(conn *TCPConnection) {
	NewTCPStandBy().Open(conn)
}
func (state TCPNull) Close(conn *TCPConnection) {
	standBy := NewTCPOpen()
	standBy.Close(conn)
}

func (state TCPNull) Send(conn *TCPConnection, payload []byte) {
	state.Open(conn)
	conn.Send(payload)
}

// TCP State
type TCPOpen struct {
	TCPNull
}

func (state *TCPOpen) Status() string {
	return "OPEN"
}

func (state *TCPOpen) Open(conn *TCPConnection) {
	fmt.Println("The tcp connection was opened")
}

func (state *TCPOpen) Close(conn *TCPConnection) {
	fmt.Println("Closing the TCP connection ...")
	state.ChangeState(conn, NewTCPClose())
}

func (state *TCPOpen) Send(conn *TCPConnection, payload []byte) {
	fmt.Println("Sending the payload: ", string(payload))
}

// TCP State
type TCPStandBy struct {
	TCPNull
}

func (state *TCPStandBy) Status() string {
	return "STANDBY"
}

func (state *TCPStandBy) Open(conn *TCPConnection) {
	fmt.Println("Opening the tcp connection ...")
	state.ChangeState(conn, NewTCPOpen())
}

func (state *TCPStandBy) Close(conn *TCPConnection) {
	closeState := NewTCPClose()
	state.ChangeState(conn, closeState)
}

func (state *TCPStandBy) Send(conn *TCPConnection, payload []byte) {
	state.Open(conn)
	conn.Send(payload)
}

// TCP State
type TCPClose struct {
	TCPNull
}

func (state *TCPClose) Status() string {
	return "CLOSE"
}

func (state *TCPClose) Open(conn *TCPConnection) {
	NewTCPStandBy().Open(conn)
}

func (state *TCPClose) Close(conn *TCPConnection) {
	fmt.Println("The tcp connection was closed.")
}

func (state *TCPClose) Send(conn *TCPConnection, payload []byte) {
	state.Open(conn)
	conn.Send(payload)
}

func NewTCPNull() TCPState {
	return TCPNull{}
}

func NewTCPStandBy() TCPState {
	return &TCPStandBy{}
}

func NewTCPOpen() TCPState {
	return &TCPOpen{}
}

func NewTCPClose() TCPState {
	return &TCPClose{}
}

func NewTCPConnection() *TCPConnection {
	return &TCPConnection{
		state: NewTCPNull(),
	}
}

func main() {
	//init connection
	conn := NewTCPConnection()

	fmt.Println("Connection:", conn.Status())

	// send payload by current connection
	fmt.Printf("========Send payload to non-init connection==========\n\n")
	conn.Send([]byte(`Hello`))

	fmt.Println("Connection:", conn.Status())

	fmt.Printf("========Open existing connection==========\n\n")

	conn.Open()

	fmt.Printf("========Close existing connection==========\n\n")

	conn.Close()
	fmt.Println("Connection:", conn.Status())

	fmt.Printf("========Send payload to closed connection ==========\n\n")
	conn.Send([]byte(`World`))
	fmt.Println("Connection:", conn.Status())

}

```



{% hint style="info" %}
**Collaborations & Implementation**: 

* Context should maintain its State internally.  _\(e.g. **TCPConnection** should decide what its internal state should be.\)_
* The Context does not maintain its internal State, but the states will transit to each other baed on the state behavior.  _\(e.g. The **TCPOpen** state will transit to the **TCPClose** automatically after we call the **TCPOpen.Close\(\)\)**_ 
{% endhint %}

​

## ​Related Patterns:

#### Flyweight Pattern: 

#### Singleton Pattern:



**NOTE:**  
Based on how we use the state, it can be implemented as **Flyweight** or **Singleton**.

​

