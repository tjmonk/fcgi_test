# fcgi_test
FCGI Test Interface

## Overview

The fcgi_test service is a FastCGI server which can be used to test
the FCGI behavior of a web server.

## Build

The build script installs build tools and the lighttpd web server via apt-get.
It then compiles and installs the FCGI library before finally compiling
and installing the fcgi_vars service.

The fcgi_test service is automatically invoked by the lighttpd web server when
the lighttpd web server starts.

```
./build.sh
```

## Prerequisites

The fcgi_test service requires the following components:

- FCGI : Fast CGI ( https://github.com/FastCGI-Archives/fcgi2 )

The example is run using the lighttpd web server ( https://www.lighttpd.net/).

The build script installs the lighttpd web server, and builds the FCGI library.

## Start the lighttpd server

```
lighttpd -f test/lighttpd.conf &
```

## Sample Lighttpd Configuration

```
server.modules = (
    "mod_auth",
    "mod_cgi",
    "mod_fastcgi",
    "mod_setenv"
)

server.document-root        = "/www/pages"
server.upload-dirs          = ( "/var/cache/lighttpd/uploads" )
server.errorlog             = "/var/log/lighttpd/error.log"
server.pid-file             = "/var/run/lighttpd.pid"
server.username             = "www-data"
server.groupname            = "www-data"
server.port                 = 80

setenv.add-response-header = ("Access-Control-Allow-Origin" => "*" )

index-file.names            = ( "index.php", "index.html", "index.lighttpd.html" )
url.access-deny             = ( "~", ".inc" )
static-file.exclude-extensions = ( ".php", ".pl", ".fcgi" )

compress.cache-dir          = "/var/cache/lighttpd/compress/"
compress.filetype           = ( "application/javascript", "text/css", "text/html", "text/plain" )

fastcgi.debug = 1
fastcgi.server = (
  "/test" => ((
    "bin-environment" => (
        "LD_LIBRARY_PATH" => "/usr/local/lib"
    ),
    "bin-path" => "/usr/local/bin/fcgi_test",
    "socket" => "/tmp/fcgi_test.sock",
    "check-local" => "disable",
    "max-procs" => 1,
  ))
)
```

## Perform Test query

```
curl localhost/test
```
```
<html><head><title>FastCGI Tester Service!</title></head><body><h1>FastCGI Hello!</h1>Request number 6 running on host <i>localhost</i>
</body></html>
```

