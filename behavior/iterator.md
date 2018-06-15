# Iterator

## Introduction: {#introduction}

Provide a way to access the element s of an aggregate object sequentially without exposing its underlying representation.

## Motivation & When should use the pattern

* ​To access an aggregate object's contents without exposing its internal representation.
* To support multiple traversals of aggregate objects.
* To provide a uniform interface for traversing different aggregate structures

## Component: {#component}

​

![](../.gitbook/assets/image%20%282%29.png)



#### Iterator:

* Defines an interface for accessing and traversing elements.

#### Concrete Iterator:

* Implements the Iterator interface.
* Keeps track of the current position in the traversal of the aggregate.

#### Aggregate:

* Defines an interface for creating an Iterator object.

#### Concrete Aggregate:

* Implements the Iterator creation interface to return an instance of the prober Concrete Iterator.

## Code Example - Golang {#code-example-golang}

```go
package main

import (
	"errors"
	"fmt"
	"reflect"
)

type Iterator interface {
	First()
	Next()
	IsDone() bool
	Result(v interface{}) error
}

type Product struct {
	Name string
}

type BasicIterator struct {
	data  []interface{}
	index int
}

func (iter *BasicIterator) First() {
	iter.index = 0
}

func (iter *BasicIterator) Next() {
	if !iter.IsDone() {
		iter.index++
	}
}

func (iter *BasicIterator) IsDone() bool {
	return iter.index >= len(iter.data)
}

func (iter *BasicIterator) Result(v interface{}) error {
	if len(iter.data) == 0 {
		return errors.New("No stored data exist")
	}
	storedData := iter.data[iter.index]

	vValue := reflect.ValueOf(v)

	if vValue.Kind() != reflect.Ptr {
		return errors.New("Must be a pointer")
	}

	vValueElem := vValue.Elem()
	storedDataValue := reflect.ValueOf(storedData)

	if vValueElem.Kind() != storedDataValue.Kind() {
		return errors.New("Mismatch value")
	}

	if !vValueElem.CanSet() {
		return errors.New("Can not update the target value")
	}

	vValueElem.Set(storedDataValue)
	return nil
}

func NewIntIterator(data []int) Iterator {
	storedData := make([]interface{}, len(data))

	for i, v := range data {
		storedData[i] = v
	}
	return &BasicIterator{
		data: storedData,
	}
}

func NewProductIterator(data []Product) Iterator {
	storedData := make([]interface{}, len(data))

	for i, v := range data {
		storedData[i] = v
	}
	return &BasicIterator{
		data: storedData,
	}
}

func main() {
	iterator := NewIntIterator([]int{1, 2, 3})

	var data int
	for !iterator.IsDone() {
		err := iterator.Result(&data)
		if err != nil {
			panic(err.Error())
		}
		fmt.Println("Int iterator data:", data)
		iterator.Next()
	}

	pIter := NewProductIterator([]Product{
		{"shoes"},
		{"nb"},
		{"chair"},
	})
	var p Product
	for !pIter.IsDone() {
		err := pIter.Result(&p)
		if err != nil {
			panic(err.Error())
		}
		fmt.Println("Product iterator data:", p)
		pIter.Next()
	}

}
​​
```

**NOTE**:

* It supports variations in the traversal of an aggregate.
* Iterators simplify the Aggregate interface.
* More than one traversal can be pending on an aggregate.

## ​Related Patterns:

#### Composite

Iterators are often applied to recursive structures such as Composites.

#### Factory Method

Polymorphic iterators rely on factory methods to instantiatethe appropriate Iterator subclass.

`NOTE: Use Factory Method to create suitable iterator (integer/string/float...) for different use cases.`

#### Memento

An iteratorcan use a memento to capture the state of an iteration. The iterator stores thememento internally

​

​

​

​

