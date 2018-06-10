# Template Method

## Introduction: {#introduction}

​Define the skeleton of an algorithm in an operation, deferring some steps to subclasses. Template Method lets subclasses redefine certain steps of an algorithm without changing the algorithm's structure.

`Template methods are a fundamental technique for code reuse. They are particularly important in class libraries.`

## Motivation & When should use builder pattern {#motivation-and-when-should-use-builder-pattern}

* ​To implement the invariant parts of an algorithm once and leave it up to subclasses to implement the behavior that can vary.
* When common behavior among subclass should be factored and localized in a common class to avoid code duplication. \(Common class only implement the common logic, and subclass need to implement the vary parts.\)
* To control subclasses extensions.

## Component: {#component}

​

![](../.gitbook/assets/image%20%2814%29.png)

#### 

#### Abstract Class:

* Defines abstract **primitive operations** that concrete subclasses define to implement steps of an algorithm.
* Implements a template method defining the skeleton of an algorithm.
* The template method calls primitive operations as well as operations defined in Abstract Class.

#### Concrete Class:

* Implements the primitive operations to carry out subclass-specific steps of the algorithm.

{% hint style="info" %}
NOTE:

It's important for template methods to specify which operations are hooks **\(may be overridden\)** and which are abstract operations **\(must be overridden\)**
{% endhint %}

## Code Example - Golang {#code-example-golang}

```go
​​package main

import (
	"fmt"
	"time"
)

type DataService interface {
	Init()
	Get() []byte
}

// Primitive Template Method
type BaseDataService struct {
	cached   []byte
	getCache func() []byte
	getDisk  func() []byte
}

func (ds *BaseDataService) hasCached() bool {
	return len(ds.cached) > 0
}

func (ds BaseDataService) getFromCache() []byte {
	return ds.cached
}

func (ds *BaseDataService) getFromDisk() []byte {
	return []byte(`Not implemented`)
}

func (ds *BaseDataService) Init() {
	if ds.getCache == nil {
		ds.getCache = ds.getFromCache
	}
	if ds.getDisk == nil {
		ds.getDisk = ds.getFromDisk
	}
}

func (ds *BaseDataService) Get() []byte {
	if ds.hasCached() {
		return ds.getCache()
	}
	data := ds.getDisk()
	defer func() { ds.cached = data }()
	return data
}

// Subclass that implements the Primitive Template Method
type UserDataService struct {
	*BaseDataService
	userType string
}

func (us *UserDataService) getFromCache() []byte {
	fmt.Println("Fetching user cache...")
	return us.BaseDataService.getFromCache()
}

func (us *UserDataService) getFromDisk() []byte {
	fmt.Println("Opening user disk...")
	fmt.Println("Fetching user disk...")
	return []byte(fmt.Sprintf("%s: %s", us.userType, time.Now().Format(time.RFC3339)))
}

func (us *UserDataService) Init() {
	us.getCache = us.getFromCache
	us.getDisk = us.getFromDisk
	us.userType = "USER-DS"
}

func NewUserDataService() *UserDataService {
	ds := &UserDataService{
		BaseDataService: &BaseDataService{},
	}
	ds.Init()
	return ds
}

func main() {
	var service DataService = NewUserDataService()

	fmt.Println("=====First time to call Get()=====")
	res := service.Get()
	fmt.Println(string(res))
	fmt.Println("=====Second time to call Get()=====")
	res = service.Get()
	fmt.Println(string(res))
}

```

{% hint style="info" %}
NOTE:

Since **Golang** does not provide the inheritance directly, we need to use Init function to make it work as expected.
{% endhint %}

## ​Related Patterns:

#### Factory Method: 

It is often called by Template methods.

#### Strategy:

Template methods use inheritance to vary part of an algorithm. Strategies use delegation to vary the entire algorithm

​

​

​

