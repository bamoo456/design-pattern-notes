# Inheritence

```go
package main

import "fmt"

type Service struct{}

func (s Service) init() {
	fmt.Println("Do some initialization ...")
}

func (s Service) run() {
	fmt.Println("_ running something ...")
}

func (s Service) Start() {
	fmt.Println("Start the default service")
}

func (s Service) Stop() {
	fmt.Println("Stop the default service")
}

func (s Service) Runner() {
	s.init()
	s.Start()
	s.Stop()
}

type WatchDog struct {
	Service
}

func (dog WatchDog) init() {
	fmt.Println("Init the watch dog ...")
}

func (dog WatchDog) run() {
	fmt.Println("running dog service...")
}

type InternalService interface {
	init()
	run()
	Start()
	Stop()
}

// Golang style runner
func Runner(svc InternalService) {
	svc.init()
	svc.Start()
	svc.run()
}

func main() {
	dog := WatchDog{}

	fmt.Println("----- normal OOP Way -----")
	dog.Runner()

	fmt.Println("----- Golang Way -----")
	Runner(dog)
}

```

