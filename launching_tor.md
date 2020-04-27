# Learning STEM: Launching Tor

This tutorial helps you launch Tor programmatically as a process. That means you don't have to manually click Tor icon to launch the browser. But since our aim is to do everything programmatically, don't expect any GUI. 

## Dependencies
* [Tor](https://www.torproject.org/download/)
* [stem](https://stem.torproject.org/download.html) (Use pip)


Let's dive straight into an example:
```python

from stem.process import launch_tor

tor_path = "<path to your Tor binary>"
#Or the path to Tor.exe for windows users

tor = launch_tor(tor_cmd= tor_path)
tor.terminate()

``` 

Here we launched Tor, and then terminated it. But it feels like magic, we never got to know when it started or ended. Let's change that with a parameter called `init_msg_handler`

```python

from stem.process import launch_tor

tor_path = "<path to your Tor binary>"
#Or the path to Tor.exe for windows users

tor = launch_tor(tor_cmd=tor_path, init_msg_handler=print)
tor.terminate()

``` 

`init_msg_handler` lets us mention the function that will handle all the messages regarding Tor's initialization. Here `print` is just python's `print()` function. We can put any user defined function in its place.

### torrc

`torrc` is the configuration file that tor uses while launching. If you don't write one explicitly, stem looks for a torrc in a default location (for me it was '/var/tmp/dist/tor/etc/tor/torrc'). If that doesn't work, it creates a default torrc for you. Here's what a [sample torrc](https://gitweb.torproject.org/tor.git/tree/src/config/torrc.sample.in) looks like. You can mention a custom torrc by using `torrc_path` parameter in `launch_tor()`.

### take_ownership

Let's say you run the following code:

```python

from stem.process import launch_tor

tor_path = "<path to your Tor binary>"
#Or the path to Tor.exe for windows users

tor = launch_tor(tor_cmd=tor_path, init_msg_handler=print)
print(omega)
tor.terminate()

``` 
and end up getting an error before you can terminate the tor process. Since tor is a seperate process, it'll keep on running in the background until you explicitely terminate it (you can check there will be a tor process running). `take_ownership` binds tor process with the calling python script and aborts it if the calling python process terminates.

Try using `take_ownership` parameter, but before that, end the tor process manually.

<hr>

There's another method used for launching Tor - `launch_tor_with_config`. It basically is used when you want to write some specific tor configurations. We'll look at them more closely in the next tutorial.

Full API can be found [here](https://stem.torproject.org/api/process.html)





