# Golang-Challenge
Deviget Golang-Challenge

![Build Status](https://github.com/famartinez80/Golang-Challenge/actions/workflows/go.yml/badge.svg?branch=master)

## New features

- *TODO: check that the price was retrieved less than "maxAge" ago!*

The struct **Price** represent the price value and expiration time with these we can validate if the price is too old or not.

```go
type Price struct {
    priceValue float64
    expiration time.Time
}
```

If *price.expiration* is after *time.Now* it means price is not too old, and we can use it, otherwise we should get it from the service again.

```go
if price.expiration.After(time.Now()) {
    return price.priceValue, nil
}
```

- *TODO: parallelize this, it can be optimized to not make the calls to the external service sequentially*

To achieve this feature we use *goroutines* to process in parallel all *itemCodes* requested, for each itemCode we open a *goroutine*,
also we create a buffered channel 10 slots this to process 10 request in parallel

```go
queue := make(chan float64, 10)

for _, itemCode := range itemCodes {
    wg.Add(1)
    go func(itemCode string) {
        defer wg.Done()
        price, _ := c.GetPriceFor(itemCode)
        queue <- price
    }(itemCode)
}
```

## Technical decisions

We change *prices* type from `map[string]float64` to `*sync.Map` due to the parallelism feature because `*sync.Map` is 
thread safe warranting the correct behavior.

[map.go documentation](https://golang.org/src/sync/map.go)

```go
type TransparentCache struct {
    actualPriceService PriceService
    maxAge             time.Duration
    prices             *sync.Map
}
```

## Coverage

Coverage is 100% you can check that in the pipeline status *Coverage Job*

## Author

**Name: Fredy Andres Martinez**  
**Email: famartinez80@gmail.com**  
**Phone: +57 3132109072**