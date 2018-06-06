# Facade

## Introduction: {#introduction}

​Provide a unified interface to a set of interfaces in a subsystem. Facade defines a higher interface that makes the subsystem easier to use.

## Motivation & When should use builder pattern {#motivation-and-when-should-use-builder-pattern}

* ​You want to provide a simple interface to a complex subsystem. Subsystem often get more complex as they evolve.
* Introduce a facade to decouple the subsystem from clients and other subsystems, thereby promoting subsystem independence and portability.
* You want to layer your subsystems. Use a facade to define an entry point to each subsystem level.

## Component: {#component}

​

![](../.gitbook/assets/image%20%287%29.png)

#### Facade

* knows which subsystem classes are responsible for a request.
* delegate client requests to appropriate subsystem objects.

#### Subsystem classes

* implement subsystem functionality.
* handle request assigned by the Facade object.
* have no knowledge of the facade.

{% hint style="info" %}
* Components in the subsystem may get strong coupled.
* Decouple Client and the subsystem
* Facade Object should not take too many parameters, must keep it simple.
* Usually Only one Facade object required. \(Singletons\)
{% endhint %}

## Code Example - Golang {#code-example-golang}

```go
​​package main

import (
	"fmt"
)

type Product struct {
	ID        string
	OwnerID   string
	FactoryID string
}

type Factory struct {
	ID       string
	Location string
}

type User struct {
	ID        string
	FirstName string
	LastName  string
	Email     string
	Key       string
	Token     string
}

type ProductDetail struct {
	Product        Product
	ProductFactory Factory
	Owner          User
}

type SqlConn interface {
	Query(string) ([]byte, error)
}

// Facade Object
type DataStore struct {
	mysql mysqlConn
	pgsql pgConn
	redis redisConn
}

func (ds DataStore) FindProductDetail(id string) ProductDetail {
	p, _ := ds.mysql.findProduct(id)
	factory, _ := ds.mysql.findFactory(p.FactoryID)
	user, _ := ds.pgsql.findUser(p.OwnerID)
	user.Token = ds.redis.getToken(user.Key)

	return ProductDetail{p, factory, user}
}

// subsystem - mysql
type mysqlConn struct {
	SqlConn
}

func (conn mysqlConn) findProduct(ID string) (Product, error) {
	conn.SqlConn.Query(
		fmt.Sprintf("SELECT * FROM Product WHERE id=%s", ID),
	)
	return Product{ID: ID}, nil
}

func (conn mysqlConn) findFactory(ID string) (Factory, error) {
	conn.SqlConn.Query(
		fmt.Sprintf("SELECT * FROM Factory WHERE id=%d", ID),
	)
	return Factory{Location: "New York"}, nil
}

// subsystem - postgres
type pgConn struct {
	SqlConn
}

func (conn pgConn) findUser(ID string) (User, error) {
	conn.SqlConn.Query(
		fmt.Sprintf("SELECT * FROM User WHERE id=%d", ID),
	)
	return User{ID: ID, FirstName: "George"}, nil
}

// subsystem - redis
type redisConn struct{}

func (redis redisConn) saveToken(userKey, token string) {
	fmt.Printf("Saving the %s's token: [%s] \n", userKey, token)
}

func (redis redisConn) getToken(userKey string) string {
	fmt.Printf("Getting the %s's token: \n", userKey)
	return "adjfasdjfoiajsdofjasdifj"
}

```



## Related Patterns:

​Abstract Factory

Mediator

​Singleton

