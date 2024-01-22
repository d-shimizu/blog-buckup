---
layout: post
Categories:  ["tech"]
Description:  " Golang の http パッケージを使って HTTP Server を作るに当たっていろいろな書き方があるけど、例えば以下のようなコードを書いたとき、登場するの関数が何をしているのかあんまりわかっていなかったので調べた。 "
Tags: ["Golang", "tech"]
date: "2020-11-23T16:53:00+09:00"
title: "Golang の http パッケージの Handle や HandleFunc に関するコードを何となく読んでみた時のメモ"
author: "dshimizu"
archive: ["2020"]
draft: "true"
---

<body>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Golang">Golang</a> の http パッケージを使って HTTP Server を作るに当たっていろいろな書き方があるけど、例えば以下のようなコードを書いたとき、登場するの関数が何をしているのかあんまりわかっていなかったので調べた。</p>
</body>

<!-- more -->

<body>
<pre class="terminal"> package main

import (
    "fmt"
    "net/http"
)

type apiHandler struct{}

func (apiHandler) ServeHTTP(http.ResponseWriter, *http.Request) {}

func main() {
    mux := http.NewServeMux()
    mux.Handle("/api/", apiHandler{}) 
    mux.HandleFunc("/", func(w http.ResponseWriter, req *http.Request) {
        // The "/" pattern matches everything, so we need to check
        // that we're at the root here.
        if req.URL.Path != "/" {
            http.NotFound(w, req)
            return
        }
        fmt.Fprintf(w, "Welcome to the home page!")
    })
}
 </pre>




<h4>NewServeMux</h4>


<p>NewServeMux は ServeMux の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>を生成している。
ServeMux は、受け付けた HTTP リク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トの URL とそれに対応するハンドラを登録するために利用される構造体。</p>

<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://go.googlesource.com/go/+/go1.14.12/src/net/http/server.go#2221">src/net/http/server.go - go - Git at Google</a></li>
</ul>
<pre class="terminal"> // ServeMux is an HTTP request multiplexer.
// It matches the URL of each incoming request against a list of registered
// patterns and calls the handler for the pattern that
// most closely matches the URL.
//
// Patterns name fixed, rooted paths, like "/favicon.ico",
// or rooted subtrees, like "/images/" (note the trailing slash).
// Longer patterns take precedence over shorter ones, so that
// if there are handlers registered for both "/images/"
// and "/images/thumbnails/", the latter handler will be
// called for paths beginning "/images/thumbnails/" and the
// former will receive requests for any other paths in the
// "/images/" subtree.
//
// Note that since a pattern ending in a slash names a rooted subtree,
// the pattern "/" matches all paths not matched by other registered
// patterns, not just the URL with Path == "/".
//
// If a subtree has been registered and a request is received naming the
// subtree root without its trailing slash, ServeMux redirects that
// request to the subtree root (adding the trailing slash). This behavior can
// be overridden with a separate registration for the path without
// the trailing slash. For example, registering "/images/" causes ServeMux
// to redirect a request for "/images" to "/images/", unless "/images" has
// been registered separately.
//
// Patterns may optionally begin with a host name, restricting matches to
// URLs on that host only. Host-specific patterns take precedence over
// general patterns, so that a handler might register for the two patterns
// "/codesearch" and "codesearch.google.com/" without also taking over
// requests for "http://www.google.com/".
//
// ServeMux also takes care of sanitizing the URL request path and the Host
// header, stripping the port number and redirecting any request containing . or
// .. elements or repeated slashes to an equivalent, cleaner URL.
type ServeMux struct {
    mu    sync.RWMutex
    m     map[string]muxEntry
    es    []muxEntry // slice of entries sorted from longest to shortest.
    hosts bool       // whether any patterns contain hostnames
}

:

// NewServeMux allocates and returns a new ServeMux.
func NewServeMux() *ServeMux { return new(ServeMux) }
 </pre>





<h4>http.Handle</h4>


<p>URLのパターンをServeMuxに登録するための関数。
ServeMux に Handle メソッドが定義されているので、<code>mux.Handle("/api/", apiHandler{}) </code>でこれが呼び出される。
Handle(pattern string, handler Handler)  の第二引数で Handler が定義されている。</p>

<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://go.googlesource.com/go/+/go1.14.12/src/net/http/server.go#2421">src/net/http/server.go - go - Git at Google</a></li>
</ul>
<pre class="terminal"> // Handle registers the handler for the given pattern.
// If a handler already exists for pattern, Handle panics.
func (mux *ServeMux) Handle(pattern string, handler Handler) {
    mux.mu.Lock()
    defer mux.mu.Unlock()
    if pattern == "" {
        panic("http: invalid pattern")
    }
    if handler == nil {
        panic("http: nil handler")
    }
    if _, exist := mux.m[pattern]; exist {
        panic("http: multiple registrations for " + pattern)
    }
    if mux.m == nil {
        mux.m = make(map[string]muxEntry)
    }
    e := muxEntry{h: handler, pattern: pattern}
    mux.m[pattern] = e
    if pattern[len(pattern)-1] == '/' {
        mux.es = appendSorted(mux.es, e)
    }
    if pattern[0] != '/' {
        mux.hosts = true
    }
}

:

// Handle registers the handler for the given pattern
// in the DefaultServeMux.
// The documentation for ServeMux explains how patterns are matched.
func Handle(pattern string, handler Handler) { DefaultServeMux.Handle(pattern, handler) }
 </pre>





<h4>http.Handler</h4>


<p>。
http.Handler は、ServeHTTP メソッドを持った Interface。
これは HTTPResponseWriter という Interface と、Request 構造体のポインタを引数に取っている。
Handler は ServeHTTP という名前が付けられたもので、ServeHTTP(ResponseWriter, *Request)というメソッドを持つもの全てがハンドラとなる模様。</p>

<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://go.googlesource.com/go/+/go1.14.12/src/net/http/server.go#86">src/net/http/server.go - go - Git at Google</a></li>
</ul>
<pre class="terminal"> // A Handler responds to an HTTP request.
//
// ServeHTTP should write reply headers and data to the ResponseWriter
// and then return. Returning signals that the request is finished; it
// is not valid to use the ResponseWriter or read from the
// Request.Body after or concurrently with the completion of the
// ServeHTTP call.
//
// Depending on the HTTP client software, HTTP protocol version, and
// any intermediaries between the client and the Go server, it may not
// be possible to read from the Request.Body after writing to the
// ResponseWriter. Cautious handlers should read the Request.Body
// first, and then reply.
//
// Except for reading the body, handlers should not modify the
// provided Request.
//
// If ServeHTTP panics, the server (the caller of ServeHTTP) assumes
// that the effect of the panic was isolated to the active request.
// It recovers the panic, logs a stack trace to the server error log,
// and either closes the network connection or sends an HTTP/2
// RST_STREAM, depending on the HTTP protocol. To abort a handler so
// the client sees an interrupted response but the server doesn't log
// an error, panic with the value ErrAbortHandler.
type Handler interface {
    ServeHTTP(ResponseWriter, *Request)
}
 </pre>





<h4>http.HandleFunc</h4>


<p>特定のパターンを処理するためのハンドラ関数を DefaultServeMux に登録する関数。</p>

<p>mux.HandleFunc() では以下のメソッドが呼び出される。</p>

<p>ServeMux がある場合は、第２引数の handler を HandlerFunc にキャストして、自身の Handle() (DefaultServeMux.Handle(pattern, handler)) を呼び出して DefaultServeMux に追加して、、ServeMux がない場合は、DefaultServeMux.HandleFunc() を呼び出して URL が登録される模様。</p>

<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://go.googlesource.com/go/+/go1.14.12/src/net/http/server.go#2464">src/net/http/server.go - go - Git at Google</a></li>
</ul>
<pre class="terminal"> // HandleFunc registers the handler function for the given pattern.
func (mux *ServeMux) HandleFunc(pattern string, handler func(ResponseWriter, *Request)) {
    if handler == nil {
        panic("http: nil handler")
    }
    mux.Handle(pattern, HandlerFunc(handler))
}

:

// HandleFunc registers the handler function for the given pattern
// in the DefaultServeMux.
// The documentation for ServeMux explains how patterns are matched.
func HandleFunc(pattern string, handler func(ResponseWriter, *Request)) {
    DefaultServeMux.HandleFunc(pattern, handler)
}
 </pre>





<h4>http.HandlerFunc</h4>


<p>HandlerFunc は func(ResponseWriter, *Request) 関数を型として定義されたもの。関数ではなく型のこと。
ServeHTTP メソッドが実装されている。
適切な値を持った関数 f() を メソッド f を持つハンドラに変換する。</p>

<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://go.googlesource.com/go/+/go1.14.12/src/net/http/server.go#2033">src/net/http/server.go - go - Git at Google</a></li>

</ul>
<pre class="terminal"> // The HandlerFunc type is an adapter to allow the use of
// ordinary functions as HTTP handlers. If f is a function
// with the appropriate signature, HandlerFunc(f) is a
// Handler that calls f.
type HandlerFunc func(ResponseWriter, *Request)

// ServeHTTP calls f(w, r).
func (f HandlerFunc) ServeHTTP(w ResponseWriter, r *Request) {
    f(w, r)
}
 </pre>





<h4>DefaultServeMux/ServeMux</h4>


<p>DefaultServeMux 変数は *ServeMux 型の変数で、 defaultServeMux のポインタが DefaultServeMux へ格納されている。
コメントを見ると、リク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トのURLを登録済みパターンのリストと照合し、URLに最も近いパターンの handler を呼び出す、とされている。
前述のコードでは、ServeMux.Handleメソッドを呼び出して以下のようにURLのPathとHandlerを登録しており、HTTPリク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トのURLが /<a class="keyword" href="http://d.hatena.ne.jp/keyword/api">api</a>/ の場合、apiHandlerのServeHTTPメソッドが呼び出される。</p>

<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://go.googlesource.com/go/+/go1.14.12/src/net/http/server.go#2237">src/net/http/server.go - go - Git at Google</a></li>
</ul>
<pre class="terminal"> // ServeMux is an HTTP request multiplexer.
// It matches the URL of each incoming request against a list of registered
// patterns and calls the handler for the pattern that
// most closely matches the URL.
//
// Patterns name fixed, rooted paths, like "/favicon.ico",
// or rooted subtrees, like "/images/" (note the trailing slash).
// Longer patterns take precedence over shorter ones, so that
// if there are handlers registered for both "/images/"
// and "/images/thumbnails/", the latter handler will be
// called for paths beginning "/images/thumbnails/" and the
// former will receive requests for any other paths in the
// "/images/" subtree.
//
// Note that since a pattern ending in a slash names a rooted subtree,
// the pattern "/" matches all paths not matched by other registered
// patterns, not just the URL with Path == "/".
//
// If a subtree has been registered and a request is received naming the
// subtree root without its trailing slash, ServeMux redirects that
// request to the subtree root (adding the trailing slash). This behavior can
// be overridden with a separate registration for the path without
// the trailing slash. For example, registering "/images/" causes ServeMux
// to redirect a request for "/images" to "/images/", unless "/images" has
// been registered separately.
//
// Patterns may optionally begin with a host name, restricting matches to
// URLs on that host only. Host-specific patterns take precedence over
// general patterns, so that a handler might register for the two patterns
// "/codesearch" and "codesearch.google.com/" without also taking over
// requests for "http://www.google.com/".
//
// ServeMux also takes care of sanitizing the URL request path and the Host
// header, stripping the port number and redirecting any request containing . or
// .. elements or repeated slashes to an equivalent, cleaner URL.
type ServeMux struct {
    mu    sync.RWMutex
    m     map[string]muxEntry
    es    []muxEntry // slice of entries sorted from longest to shortest.
    hosts bool       // whether any patterns contain hostnames
}

type muxEntry struct {
    h       Handler
    pattern string
}

// DefaultServeMux is the default ServeMux used by Serve.
var DefaultServeMux = &amp;defaultServeMux

var defaultServeMux ServeMux
 </pre>





<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://go.googlesource.com/go/+/go1.14.12/src/net/http/server.go#2519">src/net/http/server.go - go - Git at Google</a></li>

</ul>
<pre class="terminal"> // A Server defines parameters for running an HTTP server.
// The zero value for Server is a valid configuration.
type Server struct {
    // Addr optionally specifies the TCP address for the server to listen on,
    // in the form "host:port". If empty, ":http" (port 80) is used.
    // The service names are defined in RFC 6335 and assigned by IANA.
    // See net.Dial for details of the address format.
    Addr string
    Handler Handler // handler to invoke, http.DefaultServeMux if nil
 </pre>





<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://go.googlesource.com/go/+/go1.14.12/src/net/http/server.go#2824">src/net/http/server.go - go - Git at Google</a></li>

</ul>
<pre class="terminal"> // serverHandler delegates to either the server's Handler or
// DefaultServeMux and also handles "OPTIONS *" requests.
type serverHandler struct {
    srv *Server
}

func (sh serverHandler) ServeHTTP(rw ResponseWriter, req *Request) {
    handler := sh.srv.Handler
    if handler == nil {
        handler = DefaultServeMux
    }
    if req.RequestURI == "*" &amp;&amp; req.Method == "OPTIONS" {
        handler = globalOptionsHandler{}
    }
    handler.ServeHTTP(rw, req)
}
 </pre>





<h4>まとめ</h4>


<p>とりあえず全体的に http.Handler の ServeHTTP(ResponseWriter, *Request) で処理されることになるっぽい。</p>

<p>Handle などの関数は http パッケージの関数であり、ServeMux のメソッドでもある模様。実際には DefaultServeMux が持っている関数を呼び出すためのもので、例えば、http.Handle を呼び出すコードは、DefaultServeMux のメソッド Handle を呼び出すことになるようだった。</p>

<h4>参考</h4>




<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://go.googlesource.com/go">go - Git at Google</a></li>
  <li><a target="_brank" rel="noopener noreferrer" href="https://golang.org/pkg/net/http/">http - The Go Programming Language</a></li>
  <li><a target="_blank" href="https://www.amazon.co.jp/gp/product/B06XKPNVWV/ref=as_li_tl?ie=UTF8&amp;camp=247&amp;creative=1211&amp;creativeASIN=B06XKPNVWV&amp;linkCode=as2&amp;tag=dshimizu05-22&amp;linkId=44f10d71230133dc6c3750ffbfd00567" rel="noopener">Goプログラミング実践入門　標準ライブラリでゼロからWebアプリを作る impress top gearシリーズ</a></li>
  <li><a target="_brank" rel="noopener noreferrer" href="https://tutuz-tech.hatenablog.com/entry/2020/03/23/162831">GoのHTTPサーバーの実装 - 技術メモ</a></li>
  <li><a target="_brank" rel="noopener noreferrer" href="https://stackoverflow.com/questions/49668070/how-does-servehttp-work">go - How does ServeHTTP work? - Stack Overflow</a></li>
  <li><a target="_brank" rel="noopener noreferrer" href="https://journal.lampetty.net/entry/understanding-http-handler-in-go">Goのhttp.Handlerやhttp.HandlerFuncをちゃんと理解する - oinume journal</a></li>
  <li><a target="_brank" rel="noopener noreferrer" href="https://qiita.com/tenntenn/items/eac962a49c56b2b15ee8">インタフェースの実装パターン #golang - Qiita</a></li>
  <li><a target="_brank" rel="noopener noreferrer" href="https://qiita.com/immrshc/items/1d1c64d05f7e72e31a98">【Go】net/httpパッケージを読んでhttp.HandleFuncが実行される仕組み - Qiita</a></li>
  <li><a target="_brank" rel="noopener noreferrer" href="https://teratail.com/questions/224653">なぜServeHTTPを実装しているだけで呼ばれるのかがわからない｜teratail</a></li>
</ul>

</body>
