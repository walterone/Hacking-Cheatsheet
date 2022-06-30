Molti script non vanno anche se siamo admin (da fuori) perche' c'e' un remote UAC di default.

Simile al dialog in GUI questo non permette di essere system.


Per disattivarlo: https://www.poweradmin.com/help/pa-server-monitor-8-4/howto_disable_remote_uac.aspx

If you are trying to access the server, you may get a message that says Access Denied- Failed to connect to the ADMIN$ share even if you have already given the server admin status. The target system likely has Remote UAC running, which blocks local servers from operating in an elevated mode while connected to the network. To disable Remote UAC, you can edit the registry by following these steps:

1. Navigate to HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System.

2. Create a DWORD value named LocalAccountTokenFilterPolicy and set it to 1.

3. Reboot, or restart the server service.



Script:

```cmd
REG ADD HKLM\Software\Microsoft\windows\CurrentVersion\Policies\system /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1 /f

```