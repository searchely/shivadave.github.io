php-reverse-proxy
=================

This is a tiny 'reverse proxy' PHP script with a file-based cache.


Motivation
----------

While reverse proxy can be implemented as a server (nginx, squid, etc) or a server module (apache mod_proxy), installing or enabling them requires having root access to the host that is not available for end users of web-hosting services in general.

For such end users, this implements a reverse proxy as a PHP script.


License
-------

This script is licensed under the Apache License 2.0. Please note that `Cache_Lite` in `lib/` is not by the author. It is a part of PEAR.


Installation
------------

Simply upload `proxy.php` and `lib/` directory to your web site. If you do not need caching mechanism, use `proxy-simple.php` instead. `proxy-simple.php` does not require files in `lib/`.


Usage
-----

Suppose your `proxy.php` is accessible as `http://www.aaa.com/bbb/proxy.php`. To retrive `http://www.ccc.com/ddd.html` by the script, visit `http://www.aaa.com/bbb/proxy.php?dst=http://www.ccc.com/ddd.html`. Here `www.aaa.com` and `www.ccc.com` serve as a frontend and backend servers respectively; meaning that `www.ccc.com` is not necessarily reachable from you.

By using Apache `mod_rewrite`, you can map a directory of the backend server to the frontend server as follows.
```/.htaccess
RewriteRule ^eee/?(.*)$ /bbb/proxy.php?dst=http://www.ccc.com/eee/$1 [L]
```
Here all accesses to `http://www.aaa.com/eee/*` are redirected to `http://www.ccc.com/eee/*`. Besides, the flag `[QSA]` will not work as expected. See the limitation below.


Customization
-------------

If your frontend server requires an HTTP proxy to access the backend server, you can uncomment the following part in `proxy.php` to modify the default parameter for `stream_get_contents()`.

```php:proxy.php
/* # 0. Configure the proxy */
/*
stream_context_set_default(
        array("http" => array(
                        "proxy" => "tcp://your.proxy.com:8080",
                        "request_fulluri" => TRUE,
                        ),
        )
);
*/
```

Limitations
-----------

To keep the script as simple as possible, all the query strings are all proxied, as they are to the destination.

So the call `http://www.aaa.com/bbb/proxy.php?bbb=444&dst=http://www.ccc.com/ddd.html&aaa=333` becames `http://www.ccc.com/ddd.html?bbb=444&aaa=333`
