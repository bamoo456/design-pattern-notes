# Summary of Creational Patterns

## Summary:

* Abstract Factory, Builder, Prototype all involve creating new "factory object". 
* Abstract Factory has the factory object producing objects of several classes.
* Builder has the factory object building a complex object using specific protocols
* Prototype has the factory object building a product by copying a prototype object.
* Decide which pattern to be used depends on many factors, often, designs start with the Factory Method and involve toward the other Creation Patterns.

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

