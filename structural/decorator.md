# Decorator \(Wrapper\)

## Introduction: {#introduction}

Attach additional responsibilities to an object dynamically. Decorators provide a flexible alternative to subclassing for extending functionality.

![](../.gitbook/assets/image%20%287%29.png)

## Motivation & When should use builder pattern {#motivation-and-when-should-use-builder-pattern}

* ​Add responsibilities to individual objects dynamically and transparently.
* You don't want to use subclassing for extending target object's functionality.
* When extension by subclassing is impractical.  \(Sometimes a large number of independent extensions are possible and would produce an explosion of subclasses to support every combination. Or a class definition may be hidden or otherwise unavailable for subclassing\)

## Component: {#component}

![](../.gitbook/assets/image%20%285%29.png)

#### Component

* Defines the interface for objects that can have responsibilities added to them dynamically

#### Concrete Component

* Defines an object to which additional responsibilities can be attached.

#### Decorator

* Maintains a reference to a Component object and defines an interface that conforms to Component's interface.  

#### Concrete Decorator

* adds responsibilities to the comonent.

## Code Example - Golang {#code-example-golang}

```go
​​package main

import (
	"fmt"
	"math/rand"
	"time"
)

// Component
type Product interface {
	ID() string
	Type() string
	MadeFrom() string
	MadeAt() time.Time
}

// Concrete Component
type BasicProduct struct {
	id       string
	pType    string
	madeFrom string
	madeTime time.Time
}

func (p BasicProduct) ID() string        { return p.id }
func (p BasicProduct) Type() string      { return p.pType }
func (p BasicProduct) MadeFrom() string  { return p.madeFrom }
func (p BasicProduct) MadeAt() time.Time { return p.madeTime }

// Decorator
type ProductShipper interface {
	Product
	Ship()
}

// Concrete Decorator
type ProdductShipDecorator struct {
	Product
}

func (d ProdductShipDecorator) Ship() {
	fmt.Printf("Shipping the Product: %s, Made in: %s, Made Date: %s\n",
		d.Product.ID(), d.Product.MadeFrom(), d.Product.MadeAt().String(),
	)
}

func NewBasicProduct(pType string) BasicProduct {
	return BasicProduct{
		id:       fmt.Sprintf("%s-%d", pType, rand.Int()),
		pType:    pType,
		madeFrom: "TW",
		madeTime: time.Now(),
	}
}

func NewProdductShipDecorator(p Product) ProdductShipDecorator {
	return ProdductShipDecorator{p}
}

func main() {
	decoratingQueue := make(chan Product)
	shippingQueue := make(chan ProductShipper)

	go func() {
		for s := range shippingQueue {
			s.Ship()
		}
	}()

	go func() {
		for p := range decoratingQueue {
			shippingQueue <- NewProdductShipDecorator(p)
		}
	}()

	decoratingQueue <- NewBasicProduct("PC")
	decoratingQueue <- NewBasicProduct("PC")
	decoratingQueue <- NewBasicProduct("PC")
	decoratingQueue <- NewBasicProduct("Phone")
	decoratingQueue <- NewBasicProduct("Phone")
	decoratingQueue <- NewBasicProduct("TV")
}

```

{% hint style="info" %}
**NOTE**:

* Decorator may result in lots of little objects that all look alike.
* Decorator and its component are not identical.
* More flexibility that static inheritance.
* The decorator must have the same interface with its parent component.
* Changing the interface \(Decorator\) vs Changing the guts \(Strategy\)
{% endhint %}



## Related Patterns

#### ​Adapter

#### Composite

#### Strategy

​

