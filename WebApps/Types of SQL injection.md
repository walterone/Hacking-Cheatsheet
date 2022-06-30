# SQL injections 
1) **In-band SQLi (Classic SQLi)**
In-band SQL Injection occurs when an attacker is able to use the same communication channel to both launch the attack and gather results.

- *Error-based SQLi*
Error-based SQLi is an in-band SQL Injection technique that relies on error messages thrown by the database server to obtain information about the structure of the database.

- *Union-based SQLi*
Union-based SQLi is an in-band SQL injection technique that leverages the UNION SQL operator to combine the results of two or more SELECT statements into a single result which is then returned as part of the HTTP response.


<br>

2) **Inferential SQLi (Blind SQLi)**
In an inferential SQLi attack, no data is actually transferred via the web application and the attacker would not be able to see the result of an attack in-band (which is why such attacks are commonly referred to as “[blind SQL Injection attacks](https://www.acunetix.com/websitesecurity/blind-sql-injection/)”). 



- *Boolean-based (content-based) Blind SQLi*
Boolean-based SQL Injection is an inferential SQL Injection technique that relies on sending an SQL query to the database which forces the application to return a different result depending on whether the query returns a TRUE or FALSE result.


- *Time-based Blind SQLi*
Time-based SQL Injection is an inferential SQL Injection technique that relies on sending an SQL query to the database which forces the database to wait for a specified amount of time (in seconds) before responding. The response time will indicate to the attacker whether the result of the query is TRUE or FALSE.


<br>

3) **Out-of-band SQLi** 
[Out-of-band SQL Injection](https://www.acunetix.com/blog/articles/blind-out-of-band-sql-injection-vulnerability-testing-added-acumonitor/) is not very common, mostly because it depends on features being enabled on the database server being used by the web application. Out-of-band SQL Injection occurs when an attacker is unable to use the same channel to launch the attack and gather results.<br><br>Out-of-band techniques, offer an attacker an alternative to inferential time-based techniques, especially if the server responses are not very stable (making an inferential time-based attack unreliable).<br><br>Out-of-band SQLi techniques would rely on the database server’s ability to make DNS or HTTP requests to deliver data to an attacker. Such is the case with Microsoft SQL Server’s `xp_dirtree` command, which can be used to make DNS requests to a server an attacker controls; as well as Oracle Database’s UTL_HTTP package, which can be used to send HTTP requests from SQL and PL/SQL to a server an attacker controls.

