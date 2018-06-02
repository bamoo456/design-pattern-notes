# Flyweight

## Introduction: {#introduction}

​Use sharing to support large numbers of fine-gained objects efficiently.

> The Flyweight Pattern describe how to share objects to allow their use at fine granularities without prohibitive cost.

## Motivation & When should use builder pattern {#motivation-and-when-should-use-builder-pattern}

​A typical example is the document editor; a document may have tens of thousands of characters with different format style, and it's not ideal to make a compound character object for each single character.

![](../.gitbook/assets/image%20%282%29.png)

* An application use a large number of objects.
* Storage costs are high because of the sheer quantity of objects.
* Most object state can be made extrinsic. `(it means the Flyweight object will be tiny, not has many internal states)`
* Many groups of objects may be replaced by relatively few shared objects once extrinsic state is removed. `(those extrinsic states are moved as Flyweight intrinsic states)`
* The application doesn't depend on object identity.  `(because the Flyweight objects are shared, so clients may think they are different object identity, however, they may be the same underneath Flyweight object)`

## Component: {#component}

​

![](../.gitbook/assets/image%20%285%29.png)

#### Flyweight

* Declares an interface through which flyweights can receive and act on extrinsic state.

#### Concrete Flyweight

* Implements the Flyweight interface and adds storage for intrinsic state.
* Must be shareable
* Any state it stores must be intrinsic state. \(can be shared with clients\)

#### Unshared Concrete Flyweight

* Not all Flyweight subclasses need to be shared, for some use cases, some concrete subclass can implements the interface but non-shared.

#### Flyweight Factory

* Create and manages the Flyweight objects.
* For the shared Flyweight objects, the factory will create or return the Flyweight object from the object pool.
* For the non-shared Flyweight objects, the factory will create a new Flyweight object and return it to the client.

#### Client

* Maintains a reference to flyweights.
* computes or stores the extrinsic state of flyweight.

## Code Example - Golang {#code-example-golang}

```go
​​package main

import (
	"bytes"
	"context"
	"errors"
	"fmt"
	"io/ioutil"
	"net/http"
	"strings"
	"sync"
	"time"
)

// Flyweight interface
type Request interface {
	Send(context.Context) ([]byte, error)
}

// Concrete Flyweight object
type HttpRequest struct {
	BaseUrl string
	Method  string
}

func (r HttpRequest) Send(ctx context.Context) ([]byte, error) {
	path := ctx.Value("path").(string)
	switch r.Method {
	case "GET":
		res, err := http.Get(r.BaseUrl + path)
		if err != nil {
			return nil, err
		}
		return ioutil.ReadAll(res.Body)
	case "POST":
		body := ctx.Value("body").([]byte)
		res, err := http.Post(r.BaseUrl+path, "application/json", bytes.NewReader(body))
		if err != nil {
			return nil, err
		}
		return ioutil.ReadAll(res.Body)
	default:
		return nil, errors.New("Not supported method")
	}
}

// Flyweight Factory
type HttpRequestFacory struct {
	sync.Mutex
	pool map[string]*HttpRequest
}

func (f *HttpRequestFacory) NewRequest(baseUrl, method string) *HttpRequest {
	key := fmt.Sprintf("%s-%s", baseUrl, strings.ToUpper(method))
	f.Lock()
	defer f.Unlock()

	if req, ok := f.pool[key]; !ok {
		newReq := &HttpRequest{"https://" + baseUrl, method}
		f.pool[key] = newReq
		fmt.Println("======Creating and returning the new request object======")
		return newReq
	} else {
		fmt.Println("======Returning the existing request object======")
		return req
	}
}

func NewHttpRequestFactory() *HttpRequestFacory {
	return &HttpRequestFacory{
		pool: map[string]*HttpRequest{},
	}
}

// Clients function that use Flyweight interface
func ReqHandler(req Request, ctx context.Context) ([]byte, error) {
	return req.Send(ctx)
}

func main() {
	factory := NewHttpRequestFactory()

	for i := 0; i < 10; i++ {
		res, _ := ReqHandler(
			factory.NewRequest("www.google.com", "GET"),
			context.WithValue(context.Background(), "path", "/"),
		)
		fmt.Println("The total len of response: ", len(string(res)))
		<-time.After(300 * time.Millisecond)
	}

}

```

## Related Patterns: {#related-patterns}

#### Composite

#### State

#### Strategy

​

​

​

