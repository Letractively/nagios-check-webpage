# Introduction #

The aim of this nagios plugin is to download a web page with all his content ( internals links only actually ).

A thread is created by ressource and all are get in parallel.

Main features:

  * Ruby small script, easy to understand and hack ...
  * http/https
  * Use the powerful and simple hpricot lib to parse html
  * Multi-thread
  * Keyword check
  * Follow redirections
  * Download all the links on the page or only the html

# Requirements #
  * Tested with Ruby
    * 1.8.7
    * 1.9.2

  * [hpricot](http://wiki.github.com/why/hpricot)
  * [optiflag](http://optiflag.rubyforge.org/)

# Usage #
```
$ ./check_webpage.rb -h

Help for commands:
  -h          (Optional, takes 0 arguments)
                  Help
  -v          (Optional, takes 0 arguments)
                  verbose
  -vv         (Optional, takes 0 arguments)
                  more verbose !
  -e          (Optional, takes 0 arguments)
                  extended mode, see the documentation
  -H          (Optional, takes 0 arguments)
                  --span-hosts, download from other hosts
  -c          (Optional, takes 1 argument)
                  --critical, default 60,  Critical time (s)
  -w          (Optional, takes 1 argument)
                  --warn, default 5,  warn time (s)
  -w2         (Optional, takes 1 argument)
                  --warn2, default 10,  warn2 time (s), use with '-e'
  -k          (Optional, takes 1 argument)
                  --key, check for keyword
  -z          (Optional, takes 0 arguments)
                  --gzip, add gzip,deflate to http headers
  -n          (Optional, takes 0 arguments)
                  --no-inner-links, do not dl inner links, get only the html (to use instead of check_http)
  -u          (Required, takes 1 argument)
                  --url, absolute: [http://www.google.com]
```

  * **-c** Critical status time. If all or only one element is more than this time to download, a Critical status is returned.
  * **-e** Extended mode - use unknow status like a second level warning. Use with caution ...


Do not use verbose mode with nagios ...

To use in nagios :

```
define command{
  command_name  check-webpage
  command_line  $USER1$/check_webpage.rb -u $ARG1$
}
```

# Examples #

  * Normal use
```
$ ./check_webpage.rb -u http://www.google.com
OK - 15ko, 2 files, 0.74s
```

  * Verbose
```
$ ./check_webpage.rb -v -u http://www.google.com

 * Get main page: http://www.google.com/
   -> 302, main page is now: http://www.google.fr/
[200] OK s(6691) t(2.611563)

 * downloading inner links (1) ...
[200] OK /intl/fr_fr/images/logo.gif -> s(8866o) t(0.72s)

 * results
Inner links count: 1
Inner links dl cumulated time: 0.72s
Total time: 3.33s
Total size: 15ko

OK - 15ko, 2 files, 3.33s
```


  * More verbose
```
$ ./check_webpage.rb -vv -u http://www.google.com

 * ARGS: c=60 w=5 e=0 w2=10 u=http://www.google.com

 * Get main page: http://www.google.com/
   -> 302, main page is now: http://www.google.fr/
[200] OK s(6697) t(0.251305)

 * parsing results (1) ...
http://www.google.fr/intl/fr_fr/images/logo.gif -> add

 * remove duplicated links: 1 -> 1

 * downloading inner links (1) ...
[200] OK /intl/fr_fr/images/logo.gif -> s(8866o) t(0.09s)

 * results
Inner links count: 1
Inner links dl cumulated time: 0.09s
Total time: 0.35s
Total size: 15ko

OK - 15ko, 2 files, 0.35s
```