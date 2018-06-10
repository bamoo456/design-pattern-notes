# Proxy

## Introduction: {#introduction}

Provide a surrogate or placeholder for another object to control access to it.

#### Remote Proxy: \(Access remote space\)

Provides a local representative for an object in a different address space.

#### Virtual Proxy:

Creates expensive objects on demand.   
`( e.g. A ImageProxy for client to do some image related operations without open image file)`

![](../.gitbook/assets/image%20%2812%29.png)

#### Protection Proxy:

Provide some access controls for the original object.  
`(e.g. For some system objects, we may need to do some access controls before using them).`

## Motivation & When should use builder pattern {#motivation-and-when-should-use-builder-pattern}

* ​You want to use any of _**remote proxy**_, _**virtual proxy**_ or _**protection proxy**_.
* To provide a smart reference for doing below optimizations: - counting the number of references to the real objects, so that it can be freed automatically if there   are no more references. `(like javascript object reference)` - loading a persistent object into memory when it's first reference. - checking that a real object is locked before it is accessed. `(locked control)`

## Component: {#component}



![](../.gitbook/assets/image%20%286%29.png)



#### Proxy

* Maintains a reference that lets the proxy access the real objects.
* Provides an interface **identical** to Subject's so that a proxy can by substituted for the real object.
* Controls access to the real subject and may be responsible for creating and deleting it.
* Proxy can only know the **Subject** interface, but not concrete subject type.

#### Subject 

* Defines the common interface for **Real Subject** and **Proxy.**

#### Real Subject

* Defines the real object that the **proxy** represents.

{% hint style="info" %}
The proxy pattern can do a optimization called _**"copy-on-write"**_ or _**"creation-on-demand"**_
{% endhint %}

## Code Example - Golang {#code-example-golang}

```go
​​package main

import (
	"fmt"
	"os"
	"path/filepath"
)

// Subject interface
type TextFile interface {
	Name() string
	Write(string) error
	Save() error
	Close() error
}

// Real Subject
type TextFileImpl struct {
	file *os.File
}

func (impl TextFileImpl) Name() string {
	return impl.Name()
}

func (impl TextFileImpl) Write(content string) error {
	fmt.Println(">>>Write to File")
	_, err := impl.file.WriteString(content)
	return err
}

func (impl TextFileImpl) Save() error {
	return nil
}

func (impl TextFileImpl) Close() error {
	return impl.file.Close()
}

// Proxy Pattern
type TextFileProxy struct {
	impl TextFile
	data string
}

func (proxy *TextFileProxy) Name() string {
	return proxy.impl.Name()
}

func (proxy *TextFileProxy) Write(content string) error {
	fmt.Println(">>>TextFileProxy.Write")
	proxy.data = proxy.data + content
	return nil
}

func (proxy *TextFileProxy) Save() error {
	return proxy.impl.Write(proxy.data)
}

func (proxy *TextFileProxy) Close() error {
	return proxy.impl.Close()
}

func NewTextFileImpl(filename string) (TextFile, error) {
	if f, err := os.Open(filename); err != nil {
		return nil, err
	} else {
		return &TextFileImpl{f}, nil
	}
}

func NewTextFileProxy(filename string) (TextFile, error) {
	if f, err := NewTextFileImpl(filename); err != nil {
		return nil, err
	} else {
		return &TextFileProxy{f, ""}, nil
	}
}

func GetRuntimeDir() string {
	path, _ := filepath.Abs(filepath.Dir(os.Args[0]))
	return path
}

func main() {
	filename := fmt.Sprintf("%s/temp.txt", GetRuntimeDir())
	doc, err := NewTextFileProxy(filename)
	if err != nil {
		panic("Failed to open the file:" + filename)
	}

	doc.Write("Hello World\n")
	doc.Write("Hello World\n")
	doc.Write("Hello World\n")

	if saveErr := doc.Save(); saveErr != nil {
		panic(saveErr.Error())
	}
	doc.Close()
}

```

## Related Patterns: {#related-patterns}

#### Adapter

#### Decorator



#### 

​

​

​

