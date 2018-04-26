# Builder Pattern

### Instruction:

Separate the construction of a complex object from its representation so that the same construction process can create different representation.

{% hint style="info" %}
 Builder pattern usually used with other **Creational Pattern**
{% endhint %}



### Motivation & When should use builder pattern

* Decouple the logic of representation and construction. 
* If the algorithm for creating complex object should be independent, and the client does not need to know how a object should be created. 
* The construction process \(A Director\) allow different representation for the object that's created.

### Component:

![](../.gitbook/assets/screen-shot-2018-04-26-at-9.38.47-am.png)

#### Builder:

The abstract interface for creating part of **Product** object.

#### Concrete Builder:

The subclass of Builder class, which implements the real logic of creating parts of **Product.**

#### Director:

It decides how to use the builder which client provides for building the entire **Product.**

> it hides how the **parts of a Product** get assembled

#### Product

The complex object, which is the target object the client wants to create.

{% hint style="info" %}
The **Builder** provides a serious of interfaces to build the **parts of** **Product**, and the **Director** will choose how to assemble these interfaces to build the whole **Product** for the **Client**.
{% endhint %}



### Golang Example:

```go
package main

import "fmt"

type Product struct {
	partA int
	partB string
	partC []byte
}

type Builder interface {
	BuildPartA(int)
	BuildPartB(string)
	BuildPartC(string)
	GetProduct() Product
}

type NormalBuilder struct {
	partA int
	partB string
	partC []byte
}

func (b *NormalBuilder) BuildPartA(v int) {
	b.partA = v
}

func (b *NormalBuilder) BuildPartB(str string) {
	b.partB = "Part B:" + str
}

func (b *NormalBuilder) BuildPartC(str string) {
	b.partC = []byte(str)
}

func (b *NormalBuilder) GetProduct() Product {
	return Product{
		partA: b.partA,
		partB: b.partB,
		partC: b.partC,
	}
}

func fakeProductDirector(builder Builder) Product {
	builder.BuildPartA(10)
	builder.BuildPartB("Hello World")
	builder.BuildPartC("Hello World Again")
	return builder.GetProduct()
}

func main() {
	normalBuilder := &NormalBuilder{}
	fakeProduct := fakeProductDirector(normalBuilder)

	fmt.Println("The Product is:", fakeProduct)
}

```



