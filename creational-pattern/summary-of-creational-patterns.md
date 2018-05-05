# Summary of Creational Patterns

## Summary:

* Abstract Factory, Builder, Prototype all involve creating new "factory object". 
* Abstract Factory has the factory object producing objects of several classes.
* Builder has the factory object building a complex object using specific protocols
* Prototype has the factory object building a product by copying a prototype object.
* Decide which pattern to be used depends on many factors, often, designs start with the Factory Method and involve toward the other Creation Patterns.
* Singleton normally used with other Creation Patterns as well.
* Creational Patterns are not alway competitive with each other, sometimes they can work together.

## Brief Example:

#### Factory Method:

```go
package main

type GraphicFactory struct {}

func (f GraphicFactory) NewLine() {}

func (f GraphicFactory) NewCircle() {}

func (f GraphicFactory) NewSquare() {}

// ...


func main() {
    factory := GraphicFactory{}
    f.NewLine()
    f.NewCircle()
    f.NewSquare()
}

```



#### Abstract Factory:

```go
package main

type LineFactory interface {
    NewLine() Line
}

type CircleFactory interface {
    NewLine() Circle
}

type SquareFactory interface {
    NewSquare() Square
}

type NormalLineFactory struct {}

func (f NormalLineFactory) NewLine() Line {
    return Line
}

func NormalCircleFactory struct {}

func (f NormalCircleFactory) NewCircle() Circle {
    return Circle
}

type NormalSquareFactory struct {}

func (f NormalSquareFactory) NewSquare() Square {
    return Square
}
```



#### Builder:

```go
package main

type ProductBuilder interface {
	BuildContent()
	BuildBox()
	Product()
}

type Product interface {
	Ship()
}

type Shoses struct{}

type Box struct{}

type ShoesProduct struct {
	shoses Shoses
	box    Box
}

func (p ShoesProduct) Ship() {}

type ShosesBuilder struct {
	product ShoesProduct
}

func (b *ShosesBuilder) BuildContent() {
	b.product.shoses = Shoses{}
}

func (b *ShosesBuilder) BuildBox() {
	b.product.box = Box{}
}

func (b *ShosesBuilder) Product() Product {
	return b.product
}

```



#### Prototype:

```go
package main

type LinePrototype interface {
    Clone() Line
}

type CirclePrototype interface {
    Clone() Circle
}

type SquarePrototype interface {
    Clone() Square
}

type Line struct {}

type Circle struct {}

type Square struct {}

type GraphicFactory struct {
    line   LinePrototype
    circle CirclePrototype
    square SquarePrototype
}

func (f GraphicFactory) Init(l LinePrototype,c CirclePrototype,s SquarePrototype) {
    f.line = l
    f.circle = c
    f.square = s
}

func (f GraphicFactory) NewLine() Line {
    return f.line.Clone()
}

func (f GraphicFactory) NewCircle() Circle {
    return f.circle.Clone()
}

func (f GraphicFactory) NewSquare() Square {
    return f.square.Clone()
}


```



#### Singleton

```text

```



