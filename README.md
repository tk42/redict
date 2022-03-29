# redict

The Redis handy CRUD manipulation module with io interface managed by namespace.

This wrapper is based on [Redis Best Practices](https://redis.com/redis-best-practices/introduction/)

## Introduction

```
import "github.com/tk42/redict"
```

## How to use

```golang
package redict

const (
    COUNTRY int = iota
    NATIONARITY
)

c := Client(
	ClientConfig{
        Client.Redigo,
		RedisNamespace(COUNTRY, "western"),
		// RedisNamespace(COUNTRY), // No key. This case is prohibited.
		// RedisNamespace("countiry"), // OK
        host,
		port,
		isMock,
        expire,
		maxIdleConnections,
		maxActiveConnections,
	}
)

province := c.SortMap("province")
province.Create({
	"us": 51,
	"jp": 47,
}) // if you set string in values, the map is sorted by the dictional order
province.Get("us") // 51
province.Sort(FIELD_ACENDING) // {"jp": 47, "us": 51} // j < u
province.Sort(VALUE_ACENDING) // {"jp": 47, "us": 51} // 47 < 51

president := c.HashMap("president")
president.CreateEx({
	"us": "Joe Biden",
	"jp": "Humio Kishida",
}, 86400 * time.Second)
president.UpdateEx("us", "Donald Trump", time.Second)

foundation := c.HashMap("foundation") // HashMap can be created by struct also.
type Foundation {
    Founder: string,
    Year: int,
}

foundation.CreateEx({
	"us": Foundation{
        Founder: "G. Washington",
        Year: 1776,
    },
	"jp": Foundation{
        Founder: "Emperor Jinmu",
        Year: -660,
    }
}, 86400 * time.Second) // io.Write inside

data := make([]byte, 300)
len, err := foundation.GetAll(data) // io.Read inside
str := data[:len].(map[str]Foundation) // {"us": {"founder": "G. Wachington", "year": 1776}, "jp": {"founder": "Emperor Jinmu", "year": -660}}

foundation.Get("us") // Foundation{Founder: "G. Wachington", Year: 1776}

c.List("name").Create(["United States of America", "Japan"])
c.List("name").Append("Canada") // ["United States of America", "Japan", "Canada"]
c.List("name").Replace("Japan", "United Kingdom") // ["United States of America", "United Kingdom", "Canada"]

c.Set("ethnicity").Create(["White", "Asian", "Black"])
c.Set("ethnicity").Add("Latin") // ["White", "Asian", "Black", "Latin"]

president.Delete("us") // {"jp": "Humio Kishida"}
president.Delete() // {}
```
