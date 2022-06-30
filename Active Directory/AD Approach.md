## Tipi di attacco
Su AD abbiamo 3 path diversi che possiamo seguire:

- #### User Accounts Attacks
	- **Spraying**
		*(Tipologia di attacco molto rumorosa, rischio lockout accounts)*
	- **MiTM**
		*(Spoofing, Hijacking e tante altre cose che per l'OSCP non ho approfondito)*
		- NTLM Spoofing/Relaying
	- **NTLM Retrival**
 		*(Semplice reupero dell'hash NTLMv2 con Responder (o smbsrv.py))*
	- **[[Mimikatz]]**
 		*In questo caso abbiamo bisogno di privilegi **Local Admin** per sfruttare Mimikatz e l'interazione con LSASS*
		
- #### Abusing Kerberos
	- **[[AS-REP Roasting]]**
		*(Non servono credenziali valide, se ci sono SVC accounts con SPN attivi possiamo chiedere un TGS)*
	- **Kerberoasting**
		*(Spoofing, Hijacking e tante altre cose che per l'OSCP non ho approfondito)*
		- NTLM Spoofing/Relaying
	- **NTLM Retrival**
 		*(Semplice reupero dell'hash NTLMv2 con Responder (o smbsrv.py))*
		
		
		
		


## Tools
