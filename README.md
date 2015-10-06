What is ... ?

You want a simple web server started in a directory for dev or whatever, so you
could use good ol' `python -m SimpleHTTPServer` but you are busy and you don't
care about the port but UGH you have to pick a port and maybe it collides with
the server you're already running on 8000? but OMG picking ports is so hard I
don't CARE can't someone just do it for me why yes someone can! It's your
operating system, if you ask to listen a socket on port 0 then it will assign
one for you from its vast array of 16-bit uints. Oh and then of course you don't
want to mouse over to the URL and copy/paste it like a peasant so hey,
quickserver will open it FOR YOU in your browser thanks you're welcome.

### Requirements

 * Python 2.5 or greater. Works with Py3k.

### Usage

    $ ./quickserver
    Serving HTTP on 0.0.0.0 port 61209 ...
    http://0.0.0.0:61209/
    127.0.0.1 - - [19/Jun/2014 17:21:56] "GET / HTTP/1.1" 200 -

    ~ YOUR BROWSER HAS MAGICALLY OPENED A NEW TAB TO THIS URL REJOICE ~

### Installation

Put the `quickserver` file somewhere in your $PATH, or copy/paste this into your
.bashrc:

``` shell
quickserver () {
    prog=quickserver
    f1=/tmp/.$prog.$$.1
    f2=/tmp/.$prog.$$.2
    mkfifo $f1 $f2
    trap "rm -f $f1 $f2" EXIT
    grep --line-buffered -E '^Serving HTTP on .* port .* \.\.\.$' < $f1 | \
        sed -l -E -e 's|Serving HTTP on (.+) port (.+) \.\.\.|http://\1:\2/|' > $f2 &
    {
        read addr <$f2
        echo $addr
        python -m webbrowser -t $addr
    } &
    py2=`python -c 'import sys; print("%i" % (sys.hexversion<0x03000000))'`
    if [ $py2 -eq 0 ]; then
        python -u -m http.server 0 | tee $f1
    else
        python -u -m SimpleHTTPServer 0 | tee $f1
    fi
 }
 ```
