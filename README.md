deployng to k8s running via docker for mac

`docker build -t signalsciences/example-helloworld .`

the kube yaml is in examples

`kubectl -n sigsci apply -f examples/example-helloworld.yaml`

verbose debugging is enabled

make a few requests for things to appear in sigsci 200 is fine

`kubectl -n sigsci port-forward helloworld-798df58cdf-ngpwd 8000:8000`

[![grc][grc-img]][grc] [![GoDoc][doc-img]][doc]

# sigsci-module-golang

The Signal Sciences module in Golang allows for integrating your Golang
application directly with the Signal Sciences agent at the source code
level. It is written as a `http.Handler` wrapper. To
integrate your application with the module, you will need to wrap your
existing handler with the module handler.

## Installation
`go get github.com/signalsciences/sigsci-module-golang`

## Example Code Snippet
```go
// Example existing http.Handler
mux := http.NewServeMux()
mux.HandleFunc("/", helloworld)

// Wrap the existing http.Handler with the SigSci module handler
wrapped, err := sigsci.NewModule(
    // Existing handler to wrap
    mux,

    // Any additional module options:
    //sigsci.Socket("unix", "/var/run/sigsci.sock"),
    //sigsci.Timeout(100 * time.Millisecond),
    //sigsci.AnomalySize(512 * 1024),
    //sigsci.AnomalyDuration(1 * time.Second),
    //sigsci.MaxContentLength(100000),
)
if err != nil {
    log.Fatal(err)
}

// Listen and Serve as usual using the wrapped sigsci handler
s := &http.Server{
    Handler: wrapped,
    Addr:    "localhost:8000",
}
log.Fatal(s.ListenAndServe())
```

## Examples

The [examples/helloworld](examples/helloworld) directory contains complete example code.

To run the simple [helloworld](examples/helloworld/main.go) example:
```shell
# Syntax:
#   go run ./examples/helloworld <listener-address:port> [<sigsci-agent-rpc-address>]
#
# Run WITHOUT sigsci enabled
go run ./examples/helloworld localhost:8000
# Run WITH sigsci-agent listening via a UNIX Domain socket file
go run ./examples/helloworld localhost:8000 /var/run/sigsci.sock
# Run WITH sigsci-agent listening via a TCP address:port
go run ./examples/helloworld localhost:8000 localhost:9999
```

The above will run a HTTP listener on `localhost:8000`, which will send any
traffic to this listener to a running sigsci-agent for inspection (if
an agent address is configured).

[doc-img]: https://godoc.org/github.com/signalsciences/sigsci-module-golang?status.svg
[doc]: https://godoc.org/github.com/signalsciences/sigsci-module-golang
[grc-img]: https://goreportcard.com/badge/github.com/signalsciences/sigsci-module-golang 
[grc]: https://goreportcard.com/report/github.com/signalsciences/sigsci-module-golang
