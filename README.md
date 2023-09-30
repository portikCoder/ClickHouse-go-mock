
# ClickHouse-go-mock

A mock library implementing support for [clickhouse-go/v2/lib/driver](https://pkg.go.dev/github.com/ClickHouse/clickhouse-go/v2/lib/driver)


## Install

```go
go get github.com/srikanthccv/ClickHouse-go-mock
```

## Quick Start

```go

package main

import (
	"context"
	"log"

	"github.com/ClickHouse/clickhouse-go/v2"
	cmock "github.com/srikanthccv/ClickHouse-go-mock"
)

type Video struct {
	Name    string `db:"name"`
	Title   string `db:"title"`
	Content string `db:"content"`
}

func fetchVideos(conn clickhouse.Conn) (*Video, error) {
	var video Video
	if _, err := conn.Query(context.TODO(), "SELECT name, title, content FROM videos WHERE name LIKE ?", "%Cocomelon%"); err != nil {
		return nil, err
	}
	return &video, nil
}

func main() {
	mock, err := cmock.NewClickHouseNative(nil)
	if err != nil {
		log.Fatalf("an error '%s' was not expected when opening a stub database connection", err)
	}

	mock.ExpectQuery("SELECT name, title, content FROM videos WHERE name LIKE ?").WithArgs("%Cocomelon%")
	_, err = fetchVideos(mock)
	if err != nil {
		log.Fatalf("an error '%s' was not expected when querying a statement", err)
	}

	if err := mock.ExpectationsWereMet(); err != nil {
		log.Fatalf("there were unfulfilled expectations: %s", err)
	}
}
```

## Documentation

Please see the package documentation at [godoc.org](https://pkg.go.dev/github.com/srikanthccv/ClickHouse-go-mock).

And tests are the best documentation, see [clickconnmock_test.go](clickconnmock_test.go).

## License

The Apache License, Version 2.0 - see [LICENSE](LICENSE) for more details.

## Credits

This library is built on top of [clickhouse-go](https://github.com/ClickHouse/clickhouse-go) and [sqlmock](https://github.com/DATA-DOG/go-sqlmock)