# Learning STEM: Getting Tor Version

## Dependencies
* [Tor](https://www.torproject.org/download/)
* [stem](https://stem.torproject.org/download.html) (Use pip)

First thing we'd learn using stem is to get Tor's version. Please download/install all the dependcies for this tutorial.


Let's jump to an example:
```python
from stem.version import get_system_tor_version

tor_path = "<path to your tor binary>"
# For me it was <where I extracted Tor>/tor-browser_en-US/Browser/TorBrowser/Tor/tor

try:
	my_version = get_system_tor_version()
	print(my_version)

except IOError as e:
	print(e) #If this happens, you need to check your Tor path

```

You also get to break down this version if you want:
```python
print("Major:", my_version.major)
print("Minor:", my_version.minor)
print("extra info:", my_version.all_extra)
```
Full API can be found [here](https://stem.torproject.org/api/version.html)
