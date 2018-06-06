# Composite \(Nested components\)

## Introduction: {#introduction}

​Compose objects into tree structures to represent part-whole hierarchies.  
Composite lets clients treat individual objects and compositions of objects uniformly.

## Motivation & When should use builder pattern {#motivation-and-when-should-use-builder-pattern}

* ​You want to represent part-whole hierarchies of objects.
* You want clients to be able to ignore the difference between compositions of objects and individual objects. \(Clients will treat all objects in the composite structure uniformly\)

## Component: {#component}

![](../.gitbook/assets/image%20%2817%29.png)

#### Component

* Defines the interface for objects in the compositions.
* Implements the default behavior for the interface common to all classes.

#### Leaf

* Represents the leaf object which has no children.
* Defines the behavior for primitive objects in the compositions.

#### Composite

* Defines the behavior for components having children.
* Stores child components.
* Also implements the child related operations.

#### Client​

* Manipulates objects in the composition through the Component interface.

## Code Example - Golang {#code-example-golang}

```go
package main

import (
	"fmt"
	"math/rand"
)

type Equipment interface {
	Name() string
	Add(Equipment)
	Remove(Equipment)
	NetPrice() int
}

type GeneralEquipment struct {
	name string
}

func (e GeneralEquipment) Name() string { return e.name }
func (e GeneralEquipment) Add(Equipment) {
	fmt.Println("Not allowed for adding equipment")
}
func (e GeneralEquipment) Remove(Equipment) {
	fmt.Println("Not allowed for removing equipment")
}
func (e GeneralEquipment) NetPrice() int { return 0 }

type Disk struct {
	*GeneralEquipment
}

func (d Disk) NetPrice() int { return 10 }

type Cable struct {
	*GeneralEquipment
}

func (c Cable) NetPrice() int { return 3 }

type CompositeEquipment struct {
	*GeneralEquipment
	childEquipments []Equipment
}

func (e *CompositeEquipment) Add(child Equipment) {
	e.childEquipments = append(e.childEquipments, child)
}

func (e *CompositeEquipment) Remove(child Equipment) {
	newChilds := make([]Equipment, 0, len(e.childEquipments))
	// do nothing
	for _, c := range e.childEquipments {
		if c.Name() != child.Name() {
			newChilds = append(newChilds, c)
		}
	}
	e.childEquipments = newChilds
}

func (e *CompositeEquipment) NetPrice() int {
	sum := 0
	for _, child := range e.childEquipments {
		sum += child.NetPrice()
	}
	return sum
}

type Chassis struct {
	*CompositeEquipment
}

func newGeneralEquipment(name string) *GeneralEquipment {
	return &GeneralEquipment{name}
}

func newCompositeEquipment(name string) *CompositeEquipment {
	return &CompositeEquipment{
		GeneralEquipment: newGeneralEquipment(name),
		childEquipments:  []Equipment{},
	}
}

func NewEquipment(eType string) Equipment {
	name := fmt.Sprintf("%s-%d", eType, rand.Int())
	switch eType {
	case "Disk":
		return &Disk{newGeneralEquipment(name)}
	case "Cable":
		return &Cable{newGeneralEquipment(name)}
	case "Chassis":
		return &Chassis{newCompositeEquipment(name)}
	default:
		return newGeneralEquipment(name)
	}
}

func printPrice(e Equipment) {
	fmt.Printf("%s: $%d\n", e.Name(), e.NetPrice())
}

func main() {
	disk := NewEquipment("Disk")
	cable := NewEquipment("Cable")
	chassis := NewEquipment("Chassis")
	printPrice(disk)
	printPrice(cable)
	printPrice(chassis)

	chassis.Add(disk)
	chassis.Add(cable)
	printPrice(chassis)

	chassis.Remove(disk)
	printPrice(chassis)
}
​​
```

**Implementation options:**

* Explicit parent references:

\(Maintain the parent references of  when Add/Remove the composite component\)

* Sharing components

\(Composite + Flyweight\) ?

* Maximizing the Component interface

\(Ensure the Component interface define both parent and child operations, however, this will conflict some of OOP principle\) 

* Declaring the child management operations

\(Define the Add/Remove operations in the general Component Class or Define those operations in the Composite Class\).







​

​

​

