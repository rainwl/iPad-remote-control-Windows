# Use frp to establish intranet penetration for remote computer power on and remote control
---
Taking remote control of a Windows desktop using an iPad Pro as an example.
## I.Introduce & Prepare
This document will guide you through the process of using a smart plug to remotely power on your computer, deploying frp, and establishing a remote connection to your computer via RD Client. 

`The content you need to prepare`
- xiaomi smart socket
- Cloud Server (Tencent & Alibaba etc)

`what is frp ?`
- frp is a fast reverse proxy that allows you to expose a local server located behind a NAT or firewall to the Internet. It currently supports TCP and UDP, as well as HTTP and HTTPS protocols, enabling requests to be forwarded to internal services via domain name.

## II.Install
### 1.Configure frp on the cloud server
CentOS as example

First check the system architecture
```xml
arch
```
output:
```
X86_64
```
if "X86_64" , chose `amd64`

### 2.Remote connect to CentOS and perform installation.
You can execute the following command to perform the installation.
Please note that when connecting to the server, you should use the "root" user account.
```
wget https://github.com/fatedier/frp/releases/download/v0.48.0/frp_0.48.0_linux_amd64.tar.gz 
```
As of April 23rd, 2023, the latest version is 0.48. If the current date is later than that, please check the "releases" page for the latest version and replace the version number accordingly.
```
https://github.com/fatedier/frp/releases
```

### 3.Unzip command
```
tar -zxvf frp_0.48.0_linux_amd64.tar.gz 
```

### 4.Rename file
```
mv frp_0.48_linux_amd64 frp48
```
### 5.Enter file
```
cd frp48
```
### 6.Check directory
```
ls -l 
```
- frps is the server program and frps.ini is the server configuration file.
- frpc is the client program and frpc.ini is the client configuration file.
- For the server, we need to modify the frps.ini file.
### 7.Please modify the file to the corresponding format.
```
vim frps.ini
```
Alternatively, you can make the changes directly in the file directory of the Baota panel. After making the changes, the result should look like this:
```
[common]
bind_port = 7000
token = [abc123]
dashboard_port = 7500
dashboard_user = [user]
dashboard_pwd = [password]
```
Please replace the contents inside the brackets [] with your own.
### 8.Execute
```
 ./frps -c frps.ini 
```
output:
```
2023/04/23 15:58:55 [I] [root.go:200] frps uses config file: frps.ini
2023/04/23 15:58:55 [I] [service.go:192] frps tcp listen on 0.0.0.0:7001
2023/04/23 15:58:55 [I] [root.go:209] frps started successfully
```
If you see the above output, then there is no problem.
### 9.Run in the background
Use "systemctl" to control the startup.
```
vim /lib/systemd/system/frps.service
```
Write the following content into the frps.service file (which can be named as xx.service):
```
[Unit]

Description=frps service After=network.target syslog.target Wants=network.target

[Service]

Type=simple 
 
ExecStart=[actual installation directory of your frps here]/frps -c [actual installation directory of your frps here]/frps.ini
  
[Install] 

WantedBy=multi-user.target
```
Use the following command to start frps:
```
systemctl start frps
```
Use the following command to enable automatic startup:
```
systemctl enable frps
```
Use the following command to disable automatic startup:
```
systemctl  disable frps
```
Use the following command to restart the application:
```
systemctl restart frps
```
Use the following command to stop the application:
```
systemctl stop frps
```
Use the following command to view the application's logs:
```
systemctl status frps`
```
### 10.Windows
```
https://github.com/fatedier/frp/releases
```
chose `windows_amd64`
### 11.Edit frpc.ini
```
[common]
server_addr = xx.xx.xx.xx
server_port = 7000
[rdp]
type = tcp
local_ip = 127.0.0.1
local_port = 3389
remote_port = 3390
```
### 12.Automatic startup
Create a new file named "run.bat" with the following content
```
@echo off
if "%1" == "h" goto begin
mshta vbscript:createobject("wscript.shell").run("""%~nx0"" h",0)(window.close)&&exit
:begin
REM
cd [frp Path]
frpc -c frpc.ini
exit
```
`win+R` 
```
shell:startup
```
You should place a `Windows Shortcut` of the "run.bat" file in there.
###WARN
```
not run.bat , place the "SHORTCUT"
```

## III.Turn on a computer using a smart plug controlled by an iPad
Download Xiaomi's smart home control software 'Xiaomi Home', and connect to the smart plug.

When starting up a Windows computer, press and hold Delete or F12 or F2 (depending on the motherboard model, the key may differ) to enter the BIOS interface, and select the power-on option
## IV.iPad RD Client Connection
- The system requires Windows 10 Professional edition. If you don't have it, you can search online for a solution to enable remote control in the Home edition.
- Login account: Microsoft account email.
- Login password: Microsoft account password (not PIN).

### WARN
```xml
If there is an issue, log out of the computer, forget the PIN, and re-login using your Microsoft account password
```
