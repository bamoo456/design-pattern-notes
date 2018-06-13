# Visitor

## Introduction: {#introduction}

Represent an operation to be performed on the elements of an object structure.Visitor lets you define a new operation without changing the classes of the elements on which it operates.​

## Motivation & When should use the pattern

* ​A object structure contains many classes of objects with differing interfaces, and you want to perform operations on these objects that depend on their concrete classes. \(e.g. A **Machine Object** may contain **Disk, RAM, CPU ..etc**, you want to do something on those concrete object\)
* Many distinct and unrelated operations need to be performed on objects in an object structure, and you want to avoid _**polluting**_ their classes with these operations. \(Without _**Visitor Pattern**_, we may need to add extra operations on those concrete element of the object structure\)
* The classes defining the object structure rarely change, but you often want to define new operations over the structure.

{% hint style="info" %}
NOTE:

* If the elements of the object structure are frequently changed, it may not suitable for applying the _**Visitor Pattern**_.
{% endhint %}

## Component: {#component}

![](../.gitbook/assets/image%20%285%29.png)

#### Visitor:

* Declares a Visit operation for each class of Concrete Element in the Object Structure.

#### Concrete Visitor:

* Implements each operations declared by **Visitor**.  Each operation implements a fragment of the algorithm defined for the corresponding class of element object in the structure.

#### Element:

* Defines an **Accept** operations that takes a visitor as an argument.

#### Concrete Element:

* Implements an Accept operation that takes a **Visitor** as an argument.

#### Object Structure: \(Composite Elements or Collection such as a List\)

* Can enumerate its elements.
* May provide a high-level interface to allow the visitor to visit its elements.

## Code Example - Golang {#code-example-golang}

![NOTE: Check the example code for the reference.](../.gitbook/assets/image%20%2820%29.png)

```go
​​package main

import "fmt"

// Equipment interface
type Equipment interface {
	Accept(EquipmentVisitor)
}

// Visitor interface
type EquipmentVisitor interface {
	VisitDisk(Disk)
	VisitUSB(USB)
}

// Concrete Equipment
type USB struct {
	price int
}

func (usb USB) Price() int {
	return usb.price
}

func (usb USB) Accept(visitor EquipmentVisitor) {
	visitor.VisitUSB(usb)
}

// Concrete Equipment
type Disk struct {
	price    float64
	discount float64
}

func (disk Disk) Price() float64 {
	return disk.price * disk.discount
}

func (disk Disk) Accept(visitor EquipmentVisitor) {
	visitor.VisitDisk(disk)
}

// Concrete Equipment (composite)
type CompositeEquipement struct {
	equipments []Equipment
}

func (composite *CompositeEquipement) Accept(visitor EquipmentVisitor) {
	for _, e := range composite.equipments {
		e.Accept(visitor)
	}
}

func (composite *CompositeEquipement) Add(e Equipment) {
	composite.equipments = append(composite.equipments, e)
}

// Concrete Visitor
type PricingVisitor struct {
	total float64
}

func (v *PricingVisitor) VisitDisk(disk Disk) {
	v.total += disk.Price()
}

func (v *PricingVisitor) VisitUSB(usb USB) {
	v.total += float64(usb.Price())
}

func (v *PricingVisitor) Amount() float64 {
	return v.total
}

func (v *PricingVisitor) Reset() {
	v.total = 0
}

func NewUSB(currentPrice int) *USB {
	return &USB{
		price: currentPrice,
	}
}

func NewDisk(currentPrice float64) *Disk {
	return &Disk{
		price:    currentPrice,
		discount: 0.8,
	}
}

func NewCompositeEquipement() *CompositeEquipement {
	return &CompositeEquipement{}
}

func NewPriceVisitor() *PricingVisitor {
	return &PricingVisitor{}
}

func main() {
	pricing := NewPriceVisitor()
	equipments := NewCompositeEquipement()

	equipments.Add(NewUSB(30))
	equipments.Add(NewUSB(29))
	equipments.Add(NewUSB(31))
	equipments.Add(NewDisk(29.9))
	equipments.Add(NewDisk(29.8))

	equipments.Accept(pricing)

	fmt.Println("Total price: ", pricing.Amount())

	pricing.Reset()

	equipments.Add(NewDisk(29.9))
	equipments.Add(NewDisk(29.88))

	equipments.Accept(pricing)
	fmt.Println("New Total price: ", pricing.Amount())

	NewDisk(33).Accept(pricing)

	fmt.Println("Add extra - Total price: ", pricing.Amount())
}

```

**NOTE**:

* The key consideration in applying the **Visitor Pattern** is whether you are mostly likely to change the algorithm applied over an object structure or the classes of objects that make up the structure. 
* **Visitor** Interface is difficult to maintain when new Concrete Element is added frequently. 
* If the **Element class** hierarchy is stable, but yo are continually adding operations or changing algorithm, then the **Visitor Pattern** will help you manage the changes.
* Visitors can accumulate state as they visit each element in the object structure. \(Check the above **PricingVisitor** \)
* _`Who is responsible for the traversing the object structure ?`_ &gt; Often the object structure is responsible for iteration. A composite will commonly traverse itself by       having each Accept operation traverse the element's children and call Accept on each of them     recursively. &gt; Another solution is to use an iterator to visit the elements.

## ​Related Patterns:

#### Composite

Visitors can be used to apply an operation over an object structure defined by the Composite pattern.

#### Interpreter

Visitor may be applied to do the interpretation.

​

​

​

