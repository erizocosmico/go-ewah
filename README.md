# go-ewah [![GoDoc](https://godoc.org/github.com/erizocosmico/go-ewah?status.svg)](https://godoc.org/github.com/erizocosmico/go-ewah) [![Build Status](https://travis-ci.org/erizocosmico/go-ewah.svg?branch=master)](https://travis-ci.org/erizocosmico/go-ewah) [![codecov](https://codecov.io/gh/erizocosmico/go-ewah/branch/master/graph/badge.svg)](https://codecov.io/gh/erizocosmico/go-ewah) [![Go Report Card](https://goreportcard.com/badge/github.com/erizocosmico/go-ewah)](https://goreportcard.com/report/github.com/erizocosmico/go-ewah) [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

`go-ewah` is a pure Go implementation of the EWAH bitmap compression format with no dependencies other than the assertion library for the tests.

## Goals

- Read and write EWAH bitmaps using the [serialization format used by git](https://github.com/git/git/blob/master/Documentation/technical/bitmap-format.txt#L92).
- Fast read and writes.

## Install

```
go get github.com/erizocosmico/go-ewah
```

## Usage

### Read from bytes

```go
bitmap, err := bitmap.FromBytes(somebytes, binary.BigEndian)
if err != nil {
    // check error
}

value, err := bitmap.Get(6)
if err != nil {
    // check error
}

fmt.Println(value) // either `true` or `false`
```

### Read from a reader

```go
bitmap, err := bitmap.FromReader(somereader, binary.BigEndian)
if err != nil {
    // check error
}
 
// the rest is the same as the previous example
```

### Create bitmap manually

By default all bits are 0, so we only call `Set(bitPos)` to mark the ones. Once a bit N has been set, you can't set a bit M whose index is lower than the index of N.

```go
b := bitmap.New()

if err := b.Set(6); err != nil {
    // handle error
}
```

### Write bitmap

```go
b := bitmap.New()

// fill the bitmap

w := bytes.NewBuffer(nil)
bytesWritten, err := b.Write(w, binary.BigEndian)
if err != nil {
    // handle error
}
```

## Data format

For more details regarding the compression format, please see Section 3 of the following paper:

Daniel Lemire, Owen Kaser, Kamel Aouiche, Sorting improves word-aligned bitmap indexes. Data & Knowledge Engineering 69 (1), pages 3-28, 2010.
http://arxiv.org/abs/0901.3751

Or check the [Java reference implementation](https://github.com/lemire/javaewah).

## Benchmarks

```
$ go test . -bench=. -benchmem
goos: darwin
goarch: amd64
pkg: github.com/erizocosmico/go-ewah
BenchmarkBitmapGet-4            100000000               17.5 ns/op             0 B/op          0 allocs/op
BenchmarkBitmapWrite-4           5000000               338 ns/op              64 B/op          9 allocs/op
BenchmarkBitmapRead-4            3000000               537 ns/op             272 B/op         12 allocs/op
BenchmarkBitmapSet-4            200000000                8.14 ns/op            0 B/op          0 allocs/op
PASS
ok      github.com/erizocosmico/go-ewah 44.797s
```

Benchmarks run on a MacBook Pro Retina (mid-2014) with a 2,6 GHz Intel Core i5 processor running macOS High Sierra 10.13.3.

## License

MIT, see [LICENSE](/LICENSE)