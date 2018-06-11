# Temp

```go
package main
​
import (
	"context"
	"fmt"
)
​
type Service interface {
	Start()
	Stop()
}
​
type ServiceFactory interface {
	// Factory Method
	CreateService() Service
	ExecuteService(Service)
}
​
//
// Concrete Factories
//
type MyFactory struct {
	ctx context.Context
}
​
// Implements the Factory Method
func (f MyFactory) CreateService() Service {
	return &DefaulService{}
}
​
func (f MyFactory) ExecuteService(svc Service) {
	run := func() chan bool {
		out := make(chan bool)
		go func() {
			svc.Start()
			out <- true
		}()
		return out
	}
​
	select {
	case <-f.ctx.Done():
		svc.Stop() // make the service stop, then out channel will be inserted
		return
	case <-run(): // keep service running in the background
		fmt.Println("Service is stopped")
	}
}
​
type ExtendedFactory struct {
	MyFactory
}
​
// Implements the Factory Method
func (f ExtendedFactory) CreateService() Service {
	return &CustomizedService{}
}
​
//
// Factory dependent services
//
type DefaulService struct{}
​
func (s *DefaulService) Start() {
	fmt.Println("DefaultService Start() ...")
}
​
func (s *DefaulService) Stop() {
	fmt.Println("DefaultService Stop() ...")
}
​
type CustomizedService struct{}
​
func (s *CustomizedService) Start() {
	fmt.Println("-------Customized Service Running--------")
}
​
func (s *CustomizedService) Stop() {
	fmt.Println("-------Customized Service Stopped--------")
}
​
func daemonRunner(f ServiceFactory) {
	svc := f.CreateService()
	f.ExecuteService(svc)
}
​
func main() {
	myF := &MyFactory{context.Background()}
	daemonRunner(myF)
​
	fmt.Println("-------------------------")
​
	extF := &ExtendedFactory{*myF}
	daemonRunner(extF)
}
```

