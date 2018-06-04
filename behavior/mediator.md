# Mediator

## Introduction: {#introduction}

​Define an object that encapsulate show a set o f objects interact Mediator promotes loose coupling by keeping objects from referring to each other explicitly, and itlets you vary their interaction independently.

## Motivation & When should use builder pattern {#motivation-and-when-should-use-builder-pattern}

* A set of objects communicate in well-defined but complex ways.The resulting  interdependencies are unstructured and difficult to understand.
* Reusing an object is difficult because it refers to and communicates with many   other objects.
* A behavior that's distributed between several classes should be customizable  without a lot of subclassing

## Component: {#component}

![](../.gitbook/assets/image%20%284%29.png)

​

#### Mediator:

* Defines an interface for communicating with Colleague objects

#### Concrete Mediator:

* Implements cooperative behavior by coordinating Colleague objects
* Knows and maintains its colleagues

#### Colleague:

* Each Colleague class knows its Mediator object.
* Each colleague communicates with its mediator whenever it would have  otherwise communicated with another colleague

## Code Example - Golang {#code-example-golang}

#### Omitting the abstract Mediator class:

There's no need to define an abstractMediator class when colleagues work with only one mediator.

#### Colleague-Mediator communication.

Colleagues have to communicate withtheir mediator when an event of interest occurs.

```go
​​package main

import (
	"errors"
	"fmt"
)

// Colleague
type Widge interface {
	Name() string
	notifyChange()
}

// Mediator
type WidgeDirector interface {
	WidgeChanged(Widge)
}

// Basic Concrete Colleague
type BasicWidget struct {
	name     string
	director WidgeDirector
}

func (w BasicWidget) Name() string {
	return w.name
}

func (w BasicWidget) notifyChange() {
	w.director.WidgeChanged(w)
}

// Concrete Colleague
type DropDownMenu struct {
	BasicWidget
	items    []string
	selected int
}

func (menu *DropDownMenu) SelectItem(index uint) error {
	if len(menu.items) > int(index) {
		menu.selected = int(index)
		defer menu.notifyChange()
		return nil
	}
	return errors.New("No item exists")
}

func (menu *DropDownMenu) GetSelected() string {
	return menu.items[menu.selected]
}

// Concrete Colleague
type Button struct {
	BasicWidget
	isClicked bool
}

func (btn *Button) Click() {
	if btn.isClicked {
		fmt.Println("The button was clicked")
		return
	}
	btn.isClicked = true
	btn.notifyChange()
}

func (btn *Button) Reset() {
	btn.isClicked = false
}

func (btn *Button) GetClickState() bool {
	return btn.isClicked
}

// Concrete Mediator
type RenderDirector struct {
	menu      *DropDownMenu
	submitBtn *Button
}

func (d *RenderDirector) WidgeChanged(w Widge) {
	switch w.Name() {
	case "DropDownMenu":
		d.submitBtn.Reset()
	case "SubmitButton":
		if d.submitBtn.GetClickState() {
			d.renderView()
		}
	}
}

func (d *RenderDirector) Menu() *DropDownMenu {
	return d.menu
}

func (d *RenderDirector) SubmitButton() *Button {
	return d.submitBtn
}

func (d *RenderDirector) renderView() {
	fmt.Printf("[%s] is selected, trying to render it\n", d.menu.GetSelected())
}

func NewDropDownMenu(director WidgeDirector, name string, items ...string) *DropDownMenu {
	return &DropDownMenu{
		BasicWidget: BasicWidget{name, director},
		items:       items,
		selected:    0,
	}
}

func NewButton(director WidgeDirector, name string, items ...string) *Button {
	return &Button{
		BasicWidget: BasicWidget{name, director},
		isClicked:   false,
	}
}

func NewRenderDirector() *RenderDirector {
	director := &RenderDirector{}
	director.menu = NewDropDownMenu(director, "DropDownMenu", "small", "mid", "big")
	director.submitBtn = NewButton(director, "SubmitButton")
	return director
}

func main() {
	renderDirector := NewRenderDirector()
	menu := renderDirector.Menu()
	btn := renderDirector.SubmitButton()

	menu.SelectItem(0)
	btn.Click()

	menu.SelectItem(2)
	btn.Click()

	// show warning message
	btn.Click()
}

```

**NOTE**:

* It limits subclassing
* It decouples colleagues
* It simplifies object protocols
* It abstracts how objects cooperate
* It centralizes control.\(If the mediator grows too big, it will like a monolith object, and hard to maintain\)

## ​Related Patterns:

#### Facade Pattern

* Facade to it's subsystem is unidirectional, that it, the Facade only send requests to its subsystem.
* Mediator enables cooperative behavior that colleague objects don't or  can't provide, and the protocol is multidirectional.

#### Observer

​

​

