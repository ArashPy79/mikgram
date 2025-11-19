قبل وارد کردن دستورات در ترمینال میکروتیک ، یوزر منیجر و کانتینر روی میکروتیک باید نصب شده باشه 

هر بخش رو به صورت جدا کپی و وارد کنید 

اگر جایی به مشکل برخوردین ،به ایدی ArashPy79 در تگرام پیام بدین 

### توجه! فقط اکانت هایی که با این ربات ساخته شدن از طریق ربات مدیریت میشن ، در صورت استفاده از ربات هم داخل بخش یوزر ها و پروفایل ها به صورت دستی تغییراتی ندید ( منظور از تغییر اینکه پروفایل و یوزر هایی که ربات ساخته رو تغییر یا حذف نکنید ، حتی اگر پروفایل تموم شده باشه یا پسورد کاربر رو از داخل میکروتیک عوض نکنید ، تمام این موارد داخل دیتابیس ربات هم ذخیر شده و اگر به صورت دستی تغییر بدین ربات به مشکل برمیخوره ، تغییر در یوزر ها یا پروفایل های که خودتون قبلا به صورت دستی ساختید مشکلی نداره ) 
# بخش اول 
```yaml
/ip/firewall/nat add chain=srcnat action=masquerade src-address=172.17.0.0/24 
/interface/bridge/add name=docker
/ip/address/add address=172.17.0.1/24 interface=docker
/interface/veth add address=172.17.0.2/24 gateway=172.17.0.1 name=veth-mikgram
/interface/bridge/port add bridge=docker interface=veth-mikgram
/tool fetch url="https://raw.githubusercontent.com/ArashPy79/mikgram/main/vpnDB.db" dst-path=mikgram/vpnDB.db; /tool fetch url="https://raw.githubusercontent.com/ArashPy79/mikgram/main/config.yaml" dst-path=mikgram/config.yaml; /tool fetch url="https://raw.githubusercontent.com/ArashPy79/mikgram/main/user_cont.txt" dst-path=mikgram/user_cont.txt

```

# بخش دوم

```yaml
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

**بعد از وارد کردن دستورات فایل config.yaml رو دانلود و باز کنید برای انجام تظمیات**

بعد از انجام کانفیگ فایل قدیمی داخل میکروتیک رو پاک کنید و فایل config.yaml جدید رو آپلود کنید ، بعد از آپلود کانتینر رو مجددا start کنید ، توجه داشته باشید که اگر سینتکس config.yaml درست نباشه کانتینر stop میشه 



**admin_id**
======
در این قسمت باید چت ایدی اکانت ادمین رو وارد کنید ، اگر چندتا رو میخواید ادمین کنید به این صورت وارد کنید 

```yaml
admin_id:
  - 731704898633
  - 731704787879
```



**TOKEN**
======
در این قسمت باید توکن ربات خودتونو که از @BotFather گرفتید وارد کنید ، دقت داشته باشید که بین " باشه 

```yaml
TOKEN: "6768820945:AAH7OVaajU3-faZZQLDwu9fiK2xmWnXRjQk"
```


**license**
======
در این قسمت باید لایسنس خودتونو وارد کنید ، در صورت نداشتن لایسنس تغییری ندین ، بدون لایسنس بخش همکاران فعال نمیشه 
```yaml
license: "AAH7OVaajU3-faZZQLDwu9fiK2xmWnXRjQk"
```



**mikrotik_ip**
======
در این قسمت باید  ip و port سرور میکروتیک رو وارد کنید ، به صورت پیش فقط روی پورت 4433 هستش 
```yaml
mikrotik_ip: "80.90.70.20:4433"
```


**username & password**
======
در این دو قسمت یوزر و پسورد میکروتیک رو وارد کنید ، میتونید برای این قسمت یک یوزر جدید با دسترسی ها محدود ایجاد کنید و در این قسمت وارد کنید
```yaml
username: "user"
password: "pass"
```


**listp**
======

در این قسمت باید پروفایل هایی که در یوزرمنیجر از قبل ساختید رو وارد کنید 
در بخش name نام دقیق پروفایل ( به حروف کوچیک و بزرگ و فاصله ها دقت کنید ) 
در بخش price قیمت همکارای پلن رو انتخاب کنید ( داخل " قرار ندین ، جلوش هم صفر نذارید ، اعشاری هم نباشه ، مثلا اگر 35 هزار تومانه شما عدد 35 رو قرار بدین )
در بخش text متنی برای نمایش پروفایل در تلگرام انتخاب کنید ( مثلا بذارید پلن 50 گیگ یک ماهه ) 

اگر چندتا پروفایل دارید به این صورت وارد کنید 
```yaml

listp:
  - name: "50G-30d"
    price: 35
    text: "یک ماهه - 50 گیگ - تک کاربر"
  - name: "100G-30d"
    price: 75
    text: "یک ماهه - نامحدود - تک کاربر"
  - name: "150G - 30d"
    price: 95
    text: "یک ماهه - نامحدود - دو کاربر"
  - name: "100GB_limit_30d"
    price: 95
    text: "یک ماهه - 100 گیگ - دو کاربر"

```

**testP**
======
در این بخش نام پروفایلی که برای ساخت یوزر تست ایجاد کردین رو وارد کنید 
```yaml
testP: "TEST-200-MB"
```






**ovpn_test_file & open_file**
======
در این بخش باید نام فایل ovpn که برای کاربر ارسال میشه رو وارد کنید ، صورت نبود فایل فقط یوزر و پسورد ارسال میشه ، اگر فایلی داشته باشید یوزر و پسورد همراه با فایل ارسال میشه 
دقت داشته باشید فایل ovpn باید در پوشه mikgram قرار داشته باشه ، میتونید برای پروفایل تست فایل دیگه ای داشته باشید در غیر این صورت هر اسمی که در بخش open_file ثبت کردین در ovpn_test_file هم ثبت کنید 

```yaml
ovpn_test_file: "OpenVPN.ovpn"
open_file: "OpenVPN.ovpn"
```



**text_user_test & text_user**
======

در این دو بخش میتونید متنی تنظیم کنید که هنگام ارسال یوزر یا یوزر تست همراه مشخصات برای کاربر ارسال بشه ، به صورت پیش فرض خالی هستش

```yaml
text_user_test: ""
text_user: ""
```




**base_name**
======

در این بخش باید یک اسم ثابت برای یوزر ها انتخاب کنید ، به صورت پیش فرض user هستش ، مثلا اگر اولین یوزر رو درست کنید یوزرنیم میشه user1 و یوزر دوم که درست کنید میشه user2
```yaml
base_name: "user"
```




**pass_panel**
======
 در این قسمت باید یک پسورد برای دسترسی به پنل مدیریت ربات انتخاب کنید ، دقت داشته باشید ، فقط یوزر هایی که به اعنوان ادمین معرفی کردین میتونن از این پسورد استفاده کنن 

```yaml
base_name: "user"
```



**text_main_menu**
======
در این قسمت باید متن منو رو انتخاب کنید ، این متن برای تمام کاربران نمایش داده میشه 
```yaml
text_main_menu: |
  سلااام به ربات  VPN خوش اومدی 🫡🌸

  ما اینجاییم تا شما را بدون هیچ محدویتی به شبکه جهانی متصل کنیم

  📡 برقرای امنیت در ارتباط شما
  ☎️ پشتیبانی تا روز آخر

```



**username_admin**
======
در این قسمت باید ایدی اکانت تلگرامی که برای فروش هست رو قرار بدین 
```yaml
username_admin: "ID"
```



