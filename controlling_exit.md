# Learning STEM: Controlling The Exit

As you know, Tor routes requests from atleast 3 nodes(by default) before querying the destination server. In this tutorial we're going to learn how to control exit location (last node) in Tor.

## Dependencies
* [Tor](https://www.torproject.org/download/)
* [stem](https://stem.torproject.org/download.html) (Use pip)

<hr>
<br>

Before we get started with this tutorial, there's a word of caution from team Tor:
> You get the best security that Tor can provide when you leave the route selection to Tor; overriding the entry / exit nodes can mess up your anonymity in ways we don't understand. 

[Read more about it here](https://2019.www.torproject.org/docs/faq.html.en#ChooseEntryExit)

<br>

### Finding Exit Nodes
[tormap](https://tormap.void.gr/) has a great visualization of all the exit nodes. But if you want something more detailed, you can find it [here](https://metrics.torproject.org/rs.html#search/exit)

### Exiting From A Specific IP
We'll use a specific Tor exit node IP to route our data from. We can set preferred exit nodes by adding it to our config file torrc, or using `launch_tor_with_config`. 


```python
from stem.process import launch_tor_with_config

def print_status(line):
	if("100" in line):
		print(line)

tor_path = "<path to your Tor binary>"

data = requests.get("https://api.myip.com/")
print("before Tor:", data.json())

tor = launch_tor_with_config(config = {'SocksPort': '7000', 
	'ExitNodes': '<ip of the exit node>'}, init_msg_handler = print_status, 
	tor_cmd=tor_path, take_ownership=True)

try:
	data = requests.get('https://api.myip.com/', 
		proxies={'https': 'SOCKS5://127.0.0.1:7000'})
	print("After Tor:", data.json())

except Exception as e:
	print(e)

tor.terminate()
```

### Exiting From Specific Country(s)
```python
from stem.process import launch_tor_with_config

tor_path = "<path to your Tor binary>"

data = requests.get("https://api.myip.com/")
print("before Tor:", data.json())

tor = launch_tor_with_config(config = {'SocksPort': '7000', 
	'ExitNodes': '<ip of the exit node>'}, init_msg_handler = print, 
	tor_cmd=tor_path, take_ownership=True)

try:
	data = requests.get('https://api.myip.com/', 
		proxies={'https': 'SOCKS5://127.0.0.1:7000'})
	print("After Tor:", data.json())

except Exception as e:
	print(e)

tor.terminate()
```
Tor uses static files to figure out what country an IP belongs to. When you run the above code, Tor tries to find those files at default locations (for me it was /var/tmp/dist/tor/share/tor/). Since Tor couldn't verfiy the location, it would create an exit route from any random country. Solution to this would be to explicitely give it the location of the geoip files.

```python
from stem.process import launch_tor_with_config

tor_path = "<path to your Tor binary>"
geoip_path = "<where you extracted Tor>/tor-browser_en-US/Browser/TorBrowser/Data/Tor/geoip"
geoipv6_path = "<where you extracted Tor>/tor-browser_en-US/Browser/TorBrowser/Data/Tor/geoip6"


data = requests.get("https://api.myip.com/")
print("before Tor:", data.json())

tor = launch_tor_with_config(config = {'SocksPort': '7000', 'ExitNodes': '{in}', 
	'GeoIPFile': geoip_path, 'GeoIPv6File': geoipv6_path}, init_msg_handler = print, 
	tor_cmd=tor_path)

try:
	data = requests.get('https://api.myip.com/', 
		proxies={'https': 'SOCKS5://127.0.0.1:7000'})
	print("After Tor:", data.json())

except Exception as e:
	print(e)

tor.terminate()
```


