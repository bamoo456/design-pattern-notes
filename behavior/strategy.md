# Strategy \(Policy\)

## Introduction: {#introduction}

​Define a family of algorithm, encapsulate each one, and make them interchangeable. Strategy lets the algorithm vary independently from clients that use it.

## Motivation & When should use the pattern

* ​Many related classes differ only in their behavior. Strategies provide a way to configure a class with one of many behaviors.
* You need different variants of an algorithm. \(e.g. based on different speed/memory trade-off, we may consider to use different algorithm at runtime\)
* An algorithm use data that clients should not know about. \(strategies hide those complex data structure\)
* A class defines many behavior with multiple conditional statements in its operations. Instead of many conditionals, move related conditional branches into their own Strategy class.

## Component:​ {#component}

![](../.gitbook/assets/image%20%284%29.png)

#### Strategy

* Declares an interface common to all supported algorithms.

#### Concrete Strategy

* Implements the algorithm using the Strategy interface.

#### Context

* Configured with a Concrete Strategy object.
* Maintains a reference to a Strategy object.
* May defines an interface that lets Strategy access its data.

## Code Example - Golang {#code-example-golang}

```go
​​package main

import "fmt"

// Sort Strategy interface
type SortStrategy interface {
	Sort()
}

type array []int

func (data array) Swap(i, j int) {
	data[i], data[j] = data[j], data[i]
}

// Insertion Sort
type InsertionSortStrategy struct {
	data []int
}

func (hs InsertionSortStrategy) Sort() {
	fmt.Println("Sorting by [Insertion Sort Algorithm]")
	for i := 1; i < len(hs.data); i++ {
		comparedValue := hs.data[i]
		for j := i - 1; j >= 0 && hs.data[j] > comparedValue; j-- {
			array(hs.data).Swap(j, j+1)
		}
	}
}

// Heap Sort
// http://alrightchiu.github.io/SecondRound/comparison-sort-heap-sortdui-ji-pai-xu-fa.html
type HeapSortStrategy struct {
	data []int
}

func (hs HeapSortStrategy) buildHeap(data []int) {
	for keepCheck := true; keepCheck; {
		keepCheck = false
		for i := 0; i < len(data); i++ {
			if hs.maxHeap(data, i, i+1) || hs.maxHeap(data, i, i+2) {
				keepCheck = true
				continue
			}
		}
	}
}

func (hs HeapSortStrategy) maxHeap(data []int, rootIdx, leafIdx int) bool {
	size := len(data)
	if rootIdx < size && leafIdx < size && data[rootIdx] < data[leafIdx] {
		array(data).Swap(rootIdx, leafIdx)
		return true
	}
	return false
}

func (hs HeapSortStrategy) Sort() {
	fmt.Println("Sorting by [Heap Sort Algorithm]")
	for heapSize := len(hs.data) - 1; heapSize > 1; heapSize-- {
		hs.buildHeap(hs.data[0:heapSize])
		array(hs.data).Swap(0, heapSize)
	}
}

func findSortStrategy(data []int) SortStrategy {
	if len(data) <= 10 {
		return InsertionSortStrategy{data}
	}
	return HeapSortStrategy{data}
}

func main() {
	arr := []int{5, 28, 27, 0, 4, 9, 2, 1, 11, 23, 99, 31, 76, 17}

	findSortStrategy(arr).Sort()

	fmt.Println(arr)
}

```

**NOTE**:

* A context forwards requests from its clients to its strategy.​
* Strategy and Context interact to implement the chosen algorithm

## ​Related Patterns:

#### Flyweight:

Strategy object often make good flyweights

​

​

​

