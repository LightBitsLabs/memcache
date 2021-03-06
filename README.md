Memcache Client in Go (golang)
=============================

## Installing

    $ go get github.com/danielmt/memcache

After this command *memcache* is ready to use. Its source will be in:

    $GOPATH/src/github.com/danielmt/memcache

You can use `go get -u -a` for update all installed packages.

## Example

    import (
            "github.com/danielmt/memcache"
    )

    func main() {
         options := memcache.ConnectionOptions{
             Timeout: time.Duration(100) * time.Millisecond,
             MaxIdleConnsPerAddr: 200,
             ShardFunc: myCustomSharding}
         mc := memcache.New([]string{"10.0.0.1:11211", "10.0.0.2:11211", "10.0.0.3:11212"}, options)
         mc.Set(&memcache.Item{Key: "foo", Value: []byte("my value")})

         it, err := mc.Get("foo")
         ...
    }
    
    // example implementation (not for use in production.)
    func myCustomSharding(key string, addrs []*memcache.Addr) (*Addr, error) {
        if len(addrs) == 0 {
            return nil, memcache.ErrNoServers
        }
        cs := crc32.ChecksumIEEE([]byte(key))
        return addrs[cs%uint32(len(addrs))], nil
    }

## About

This is a memcache client library for the Go programming language
(http://golang.org/). This is a high performance fork of the original
library at http://github.com/bradfitz/gomemcache, from
http://github.com/rainycape/memcache, adding connection options to
instantiation and exposing the sharding algorithm, so you can use your own.
The following is a comparison between the original library and this one:

    benchmark                               old ns/op    new ns/op    delta
    BenchmarkSetGet                            214443       138200  -35.55%
    BenchmarkSetGetLarge                       262164       146594  -44.08%
    BenchmarkConcurrentSetGetSmall10_100     82561221     51282962  -37.88%
    BenchmarkConcurrentSetGetLarge10_100     96067285     63887183  -33.50%
    BenchmarkConcurrentSetGetSmall20_100    152834658     75607154  -50.53%
    BenchmarkConcurrentSetGetLarge20_100    202574186     96010615  -52.60%

    benchmark                                old MB/s     new MB/s  speedup
    BenchmarkSetGet                              0.03         0.04    1.33x
    BenchmarkSetGetLarge                         4.82         8.62    1.79x
    BenchmarkConcurrentSetGetSmall10_100         0.07         0.12    1.71x
    BenchmarkConcurrentSetGetLarge10_100        13.16        19.78    1.50x
    BenchmarkConcurrentSetGetSmall20_100         0.08         0.16    2.00x
    BenchmarkConcurrentSetGetLarge20_100        12.48        26.33    2.11x

    benchmark                              old allocs   new allocs    delta
    BenchmarkSetGet                                18            6  -66.67%
    BenchmarkSetGetLarge                           19            6  -68.42%
    BenchmarkConcurrentSetGetSmall10_100        58469         6199  -89.40%
    BenchmarkConcurrentSetGetLarge10_100        59848         6196  -89.65%
    BenchmarkConcurrentSetGetSmall20_100       117177        12432  -89.39%
    BenchmarkConcurrentSetGetLarge20_100       120173        12413  -89.67%

    benchmark                               old bytes    new bytes    delta
    BenchmarkSetGet                              2479          170  -93.14%
    BenchmarkSetGetLarge                         7537         1184  -84.29%
    BenchmarkConcurrentSetGetSmall10_100      3101520       187245  -93.96%
    BenchmarkConcurrentSetGetLarge10_100      8330341      1197783  -85.62%
    BenchmarkConcurrentSetGetSmall20_100      6318072       374977  -94.07%
    BenchmarkConcurrentSetGetLarge20_100     16884200      2398491  -85.79%
