# Learning STEM: Requests With Tor

In this tutorial we're going to learn how to use the famous [requests library](https://requests.readthedocs.io/en/master/) to get resources from internet via Tor network. That means, we'll be using tor as a proxy between us and the internet.

## Dependencies
* [Tor](https://www.torproject.org/download/)
* [stem](https://stem.torproject.org/download.html) (Use pip)
* [requests](https://requests.readthedocs.io/en/master/user/install/#install) (use `pip install requests[socks]` instead of `pip install requests`)
<hr>
<br>
### Without Tor
Let's use myip.com's API to find out our public IP and the country we're requesting from.

```python
import requests

data = requests.get("https://api.myip.com/")
print(data.json())
```
<br>
### With Tor

```python
import requests
from stem.process import launch_tor

def print_status(line):
	if("100" in line):
		print(line)

tor_path = "<path to your Tor binary>"

data = requests.get("https://api.myip.com/")
print("before Tor:", data.json())

tor = launch_tor(tor_cmd= tor_path, init_msg_handler= print_status, 
	take_ownership=True)

try:
	data = requests.get('https://api.myip.com/', 
		proxies={'https': 'SOCKS5://127.0.0.1:9050'})
	print("After Tor:", data.json())

except Exception as e:
	print(e)

tor.terminate()
```

<br>
### what happened?
![requests_with_tor1](https://sigmapie8.github.io/learning-stem/images/requests_with_tor1.png)

Let's break down our code.

* `tor = launch_tor(tor_cmd= tor_path, init_msg_handler= print_status, 
					take_ownership=True)`
Remember how `init_msg_handler` could be set to any function to handle the intialization messages? We used `print_status` function to filter out those messages and print the status only when the setup was 100% completed.

* `data = requests.get('https://api.myip.com/', 
						proxies={'https': 'SOCKS5://127.0.0.1:9050'})`
By default, Tor accepts socks proxy requests at port 9050. That means, if we send requests to Tor on port 9050, it'll route those requests from within Tor's network. We installed `requests[socks]` so that requests would be able to communicate to Tor on socket level.

## Using a Different Port
Well, may be the port 9050 is taken, or you want to use it for something else. How are you going to make Tor listen on a different port?
You can make changes in the torrc file. Or you can use a shiny new method of launching tor that gives you dynamic configurations.

```python
import requests
from stem.process import launch_tor

def print_status(line):
	if("100" in line):
		print(line)

tor_path = "<path to your Tor binary>"

data = requests.get("https://api.myip.com/")
print("before Tor:", data.json())

#CHANGED
tor = launch_tor_with_config(config = {'SocksPort': '7000'}, 
	tor_cmd= tor_path, init_msg_handler= print_status)
try:
	#CHANGED
	data = requests.get('https://api.myip.com/', 
		proxies={'https': 'SOCKS5://127.0.0.1:7000'})
	print("After Tor:", data.json())

except Exception as e:
	print(e)

tor.terminate()
```

See what we did there? we changed the method from `launch_tor` to `launch_tor_with_config` and made Tor listen on port 7000.

Full API can be found [here](https://stem.torproject.org/api/process.html)