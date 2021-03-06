# Docker Distribution JSON Canonicalization

## Introduction

To provide consistent content hashing of JSON objects throughout Docker
Distribution APIs, a canonical JSON format has been defined. Adopting such a
canonicalization also aids in caching JSON responses.

## Rules

Compliant JSON should conform to the following rules:

1. All generated JSON should comply with [RFC
   7159](http://www.ietf.org/rfc/rfc7159.txt).
2. Resulting "JSON text" shall always be encoded in UTF-8.
3. Unless a canonical key order is defined for a particular schema, object
   keys shall always appear in lexically sorted order.
4. All whitespace between tokens should be removed.
5. No "trailing commas" are allowed in object or array definitions.

## Examples

The following is a simple example of a canonicalized JSON string:

```json
{"asdf":1,"qwer":[],"zxcv":[{},true,1000000000,"tyui"]}
```

## Reference

### Other Canonicalizations

The OLPC project specifies [Canonical
JSON](http://wiki.laptop.org/go/Canonical_JSON). While this is used in
[TUF](http://theupdateframework.com/), which may be used with other
distribution-related protocols, this alternative format has been proposed in
case the original source changes. Specifications complying with either this
specification or an alternative should explicitly call out the
canonicalization format. Except for key ordering, this specification is mostly
compatible.

### Go

In Go, the [`encoding/json`](http://golang.org/pkg/encoding/json/) library
will emit canonical JSON by default. Simply using `json.Marshal` will suffice
in most cases:

```go
incoming := map[string]interface{}{
    "asdf": 1,
    "qwer": []interface{}{},
    "zxcv": []interface{}{
        map[string]interface{}{},
        true,
        int(1e9),
        "tyui",
    },
}

canonical, err := json.Marshal(incoming)
if err != nil {
  // ... handle error
}
```

To apply canonical JSON format spacing to an existing serialized JSON buffer, one
can use
[`json.Indent`](http://golang.org/src/encoding/json/indent.go?s=1918:1989#L65)
with the following arguments:

```go
incoming := getBytes()
var canonical bytes.Buffer
if err := json.Indent(&canonical, incoming, "", ""); err != nil {
	// ... handle error
}
```
