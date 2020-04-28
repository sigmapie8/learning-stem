# Learning STEM: Requests With Tor

In this tutorial we're going to learn how to use the famous [requests library](https://requests.readthedocs.io/en/master/) to get resources from internet via Tor network. That means, we'll be using tor as a proxy between us and the internet.

## Dependencies
* [Tor](https://www.torproject.org/download/)
* [stem](https://stem.torproject.org/download.html) (Use pip)
* [requests](https://requests.readthedocs.io/en/master/user/install/#install) (use `pip install requests[socks]` instead of `pip install requests`)

### Without Tor
Let's use myip.com's API to find out our public IP and the country we're requesting from.

```python
import requests

data = requests.get("https://api.myip.com/")
print(data.json())
```
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

tor = launch_tor(tor_cmd= tor_path, init_msg_handler= print_status, take_ownership=True)

try:
	data = requests.get('https://api.myip.com/', proxies={'https': 'SOCKS5://127.0.0.1:9050'})
	print("After Tor:", data.json())

except Exception as e:
	print(e)

tor.terminate()
```
**what happened?**
![requests_with_tor1](https://sigmapie8.github.io/learning-stem/images/requests_with_tor1.png)

Let's break down our code.

* 