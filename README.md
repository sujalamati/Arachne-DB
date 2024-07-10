# ArachneDB

[![made-with-Go](https://img.shields.io/badge/Made%20with-Go-1f425f.svg)](http://golang.org)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)

ArachneDB is a  Key-Value Store built from scratch using Golang taking care of necessary features such as concurrency, persistence and isolated transactions.

## Installing

To start using ArachneDB, install Go and run `go get`:

```sh
go get -u github.com/sujalamati/ArachneDB
```

## Basic usage
```go
package main

import "github.com/sujalamati/ArachneDB"

func main() {
	path := "arachne.adb"
	db, _ := ArachneDB.Open(path, ArachneDB.DefaultOptions)

	tx := db.WriteTx()
	name := []byte("test")
	collection, _ := tx.CreateCollection(name)

	key, value := []byte("key1"), []byte("value1")
	_ = collection.Put(key, value)

	_ = tx.Commit()
}
```
## Transactions
Read-only and read-write transactions are supported. ArachneDB allows multiple read transactions or one read-write 
transaction at the same time. Transactions are goroutine-safe.

ArachneDB has an isolation level: [Serializable](https://en.wikipedia.org/wiki/Isolation_(database_systems)#Serializable).
In simpler words, transactions are executed one after another and not at the same time.This is the highest isolation level.

### Read-write transactions

```go
tx := db.WriteTx()
...
if err := tx.Commit(); err != nil {
    return err
}
```
### Read-only transactions
```go
tx := db.ReadTx()
...
if err := tx.Commit(); err != nil {
    return err
}
```

## Collections
Collections are a grouping of key-value pairs. Collections are used to organize and quickly access data as each
collection is B-Tree by itself. All keys in a collection must be unique.
CRUD operations on collections are possible using the methods `tx.CreateCollection` `tx.GetCollection` `tx.DeleteCollection`
```go
tx := db.WriteTx()
collection, err := tx.CreateCollection([]byte("test"))
if err != nil {
	return err
}

collection, err := tx.GetCollection([]byte("test"))
if  err != nil {
    return err
}

err := tx.DeleteCollection([]byte("test"))
if err != nil {
	return err
}
_ = tx.Commit()
```

## Key-Value Pairs
Key/value pairs reside inside collections. CRUD operations are possible using the methods `Collection.Put` 
`Collection.Find` `Collection.Remove` as shown below.   
```go
tx := db.WriteTx()
collection, err := tx.GetCollection([]byte("test"))
if  err != nil {
    return err
}

key, value := []byte("key1"), []byte("value1")
if err := collection.Put(key, value); err != nil {
    return err
}
if item, err := collection.Find(key); err != nil {
    return err
}

if err := collection.Remove(key); err != nil {
    return err
}
_ = tx.Commit()
```