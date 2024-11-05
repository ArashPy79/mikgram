قبل وارد کردن دستورات در ترمینال میکروتیک ، یوزر منیجر و کانتینر روی میکروتیک باید نصب شده باشه 
هر بخش رو به صورت جدا کپی کنید و وارد کنید 

ااگر جایی به مشکل برخوردین ،به ایدی ArashPy79 در تگرام پیام بدین 


**بخش اول**
```
/ip/firewall/nat add chain=srcnat action=masquerade src-address=172.17.0.0/24 
/interface/bridge/add name=docker
/ip/address/add address=172.17.0.1/24 interface=docker
/interface/veth add address=172.17.0.2/24 gateway=172.17.0.1 name=veth-mikgram
/interface/bridge/port add bridge=docker interface=veth-mikgram
/tool fetch url="https://raw.githubusercontent.com/ArashPy79/mikgram/main/vpnDB.db" dst-path=mikgram/vpnDB.db; /tool fetch url="https://raw.githubusercontent.com/ArashPy79/mikgram/main/config.yaml" dst-path=mikgram/config.yaml; /tool fetch url="https://raw.githubusercontent.com/ArashPy79/mikgram/main/user_cont.txt" dst-path=mikgram/user_cont.txt

```

**بخش دوم**

```
/container config set registry-url=https://registry-1.docker.io tmpdir=disk1/pull
/container mounts add dst=/app name=mikgram src=/mikgram
/container add cmd="sh -c './mikgram/pygram'" interface=veth-mikgram mounts=mikgram  remote-image=arashpy79iran/mikgram:latest start-on-boot=yes
:global d "loading #"
:while ([:len [container/find where tag=arashpy79iran/mikgram:latest status=extracting]]=1) do={:delay 1s;:global d ($d."#");:put $d}
:delay 5s
/container start number=[find tag=arashpy79iran/mikgram:latest] 
/system scheduler add interval=30s name=active on-event="/user-manager session remove [find where active=no]" policy=\
    ftp,reboot,read,write,policy,test,password,sniff,sensitive,romon start-date=2024-05-29 start-time=10:12:13
/system scheduler add interval=2m name=profile on-event=":foreach usad in=[/user-manager/user-profile/find where (state=used or state=running)] do={\r\
    \n    \r\
    \n    :local nameuser [/user-manager/user-profile/find where user=[/user-manager/user-profile/get \$usad user]]\r\
    \n    :foreach nameuser2 in=\$nameuser do={\r\
    \n        :local i [/user-manager/user-profile/get \$nameuser2 state]\r\
    \n        :local y [/user-manager/user-profile/get \$nameuser2 user]\r\
    \n        :\r\
    \n        :if ( \$i != \"used\" and \$i != \"running\") do={\r\
    \n            :foreach z in=[/user-manager/user-profile/find where user=\$y (state=used or state=running) ] do={\r\
    \n            /user-manager/user-profile/remove numbers=\$z\r\
    \n            /user-manager/user/remove numbers=[find name=\$y]\r\
    \n            undo\r\
    \n            }\r\
    \n        } \r\
    \n    \r\
    \n}}" policy=ftp,reboot,read,write,policy,test,password,sniff,sensitive,romon start-date=2024-09-25 start-time=06:26:19
/certificate
add name=ca days-valid=3650 common-name=your.server.url key-usage=key-cert-sign,crl-sign
add name=server days-valid=3650 common-name=your.server.url

/certificate
sign ca name=root-ca
:delay 3s
sign ca=root-ca server name=server
:delay 3s

/certificate
set root-ca trusted=yes
set server trusted=yes

/ip service
set www-ssl certificate=server disabled=no port=4433
```



آ
