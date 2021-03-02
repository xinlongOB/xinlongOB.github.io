---
title: CentOS下部署ftp共享服务
tags:
  - vsftpd
  - linux
categories: 运维
date: 2021-03-02 16:40:07
---
## FTP概述：
FTP（文件传输协议）是典型的C/S架构的应用层协议，需要由服务端软件、客户端软件两部分共同实现文件传输功能。
## FTP连接及传输模式
FTP的两个端口：
1、控制连接：TCP的21号端口，用于控制连接，发送FTP命令信息。
2、数据连接：TCP的20号端口，用于上传、下载数据。

一般观测FTP时，只观测21号端口即可，存在则说明服务正在运行。
数据连接的建立类型：主动模式、被动模式(以服务器的角度)

主动模式：服务器主动发起数据连接
  首先由服务器的21号端口建立FTP控制连接，当需要数据连接时，客户端以PORT命令告知服务器，“我打开了某个端口，你过来连接我”，于是服务器从20号端口向客户端的该端口发送请求并建立数据连接。
这其中的“某端口”是客户端任意挑选的一个未被使用的随即端口。

被动模式：服务器被动等待数据连接
  如果客户机所在网络的防火墙禁止主动模式连接，通常会使用被动模式，首先由客户端向服务器的21号端口建立FTP控制连接，当需要数据连接时，服务器以PASV命令告知客户端（非20号端口）发送请求并建立数据连接。
这其中的“某端口”是服务器任意挑选的一个未被使用的随即端口。
客户端与服务器建立好数据连接之后就可以根据控制连接中发送FTP命令上传或下载文件了。

在传输文件时，根据是否进行字符转换，分为文本模式和二进制模式。
1、文本模式：又称为ASCII（美国信息交换标准码）模式，这种模式在传输文件时使用ASCII标准字符序列，一般只用于“纯文本”的传输。
2、二进制模式：又称为Binary模式，这种模式不会转换文件中的字符序列，更适合传输程序，图片等非纯文本字符的文件。
使用二进制模式比文本模式更有效率，大多数FTP客户端工具根据文件类型自动选项文件传输模式，而不需要用户手动指定。

## FTP的用户类型
FTP用户类型可分为三类
1、使用FTP客户端软件访问服务器时，通常要用到一类特殊的用户账号，其用户名为ftp或者anonymous，提供任意密码（包括空密码）都可以通过FTP服务器的验证，这样的用户称为“匿名用户”。
2、除了不需要密码验证的匿名用户以外，FTP服务区还可以直接使用本机的系统用户帐号进行验证，这些用户通常被称为“本地用户”。
3、有些FTP服务器软件还可以维护一份独立的用户数据库文件，而不是直接使用系统用户帐号，这些位于独立数据文件中的FTP用户帐号，通常被称为“虚拟用户”，通过使用虚拟用户将FTP账户与linux系统账户的关联性降至最低，增加了系统的安全性。
## FTP服务器软件的种类
在windows系统中，常见的FTP服务器软件包括file zilla server、serv-U等。
在linux系统中，vsftpd是目前在linux/Unix领域应用十分广泛的一款FTP服务软件。
vsftpd服务的全名来源于“very secure FTP daemon”。
该软件针对安全性方面做了大量的设计，除了安全性外，vsftpd在速度和稳定性方面的表现也相当突出。
最简单的FTP客户端工具莫过于“ftp”命令程序了，除了之外还有大量的图形化FTP客户端工具，windows中比较常用的包括“Cuter FTP”、“Flash FXP”、“Leap FTP”、“Filezilla”等。
还有一些下载工具软件，如“Flash Get”、“wget”等。包括大多数网页浏览器程序，都支持通过FTP协议下载文件，但因不具备FTP上传等管理功能，通常不称为FTP客户端工具。
Vsftpd的配置文件
通过安装vsftpd软件包，将会自动添加名为vsftpd的系统服务。因此，启动、停止vsftpd服务变得非常方便。构建vsftpd服务器的关键在于熟悉相关的配置文件。Vsftpd服务的配置文件位于 /etc/vsftpd/目录下，包括用户列表文件ftpusers、user_list和主机配置文件vsftpd.conf等。

## FTP服务器配置
1、用户列表文件ftpusers和user_list
在ftpusers、user_list文件中，各自记录了若干个FTP用户的帐号名称，两个文件都用于FTP登录控制，但是控制方式存在一些差别。
  ftpusers文件：此文件中列出的用户将禁止登录vsftpd服务器，不管该用户是否在user_list文件中出现，默认已经包括root、bin、daemon等用于系统允许的特殊用户（就如同黑名单）。
user-list文件：此文件中包含的用户可能被禁止登录，也可能被允许登录，具体取决主配置文件vsftpd.conf中的设置，当存在“userlist_enable = YES”的配置项时，user_list列表文件方可生效。若指定“userlist_deny = YES”则仅禁止此文件列表中的用户登录。若制定“userlist_deny = NO”，则仅允许此文件列表中的用户登录。
ftpusers文件相当于黑名单，为vsftpd服务提供了一份禁止登录的用户列表，而user_list文件提供了一份可以灵活控制的用户列表，二者相互结合，为FTP帐号的登录控制提供了辩解的途径。


2、主配置文件vsftpd.conf
在vsftpd的主配置文件中，配置航采用“配置项 = 参数”形式。
以下为常见的配置项及含义说明：
```bash
anoinymous_enable = YES ：是否允许匿名访问。Yes表示允许。
anon_umask = 022 ：设置匿名用户上传文件的默认权限掩码值。
anon_root = /var/ftp ：设置匿名用户的FTP根目录（默认/var/ftp）
anon_upload_enable = YES ：是否允许匿名用户上传文件，YES为允许。
anon_mkdir_write_enable = YES : 是否允许匿名用户有创建目录的写入权限。
anon_other_write_enable ：是否允许匿名用户有其他写入权限，如：对文件改名、覆盖及删除文件等操作。
anon_max_rate = 0 ：限制匿名用户的最大传输速率（0表示无限制），单位为：  字节/秒
用于本地用户验证配置项：
local_enable = YES ：是否允许本地系统用户访问。
local_umask = 022 ：设本地用户所上传文件的默认权限掩码值。
local_root = /var/ftp ：设置本地用户的FTP根目录（默认为用户的家目录）。
chroot_local_user = YES ：是否将FTP本地用户禁锢在家目录中。
local_max_rate = 0 ：限制本地用户的最大传输速率（0表示无限制），单位为： 字节/秒
全局配置项：
Listen = YES ；是否以独立运行的方式监听服务
Listen_address = 0.0.0.0 ：设置监听FTP服务的ip地址
Listen_port = 21 ：设置监听FTP服务的端口号。
Write_enable =YES ：启用任何形式的写入权限，如上传、删除文件等都需要开启此项。
download_enable = YES ：是否允许下载文件（建立仅限于浏览、上传的FTP服务器时可将其设置为 NO ）。
Xferlog_enable = YES ：启用xferlog日志，默认记录到/var/log/xferlog
Xferlog_std_format = YES ：启用标准的xferlog日志格式，若仅用此项，将使用vsftpd自己的日志格式。
Connect_from_port_20 = YES ：允许服务器主动模式（从20号端口尽力数据连接）。
Pasv_enable = YES ：允许被动模式连接
Pasv_max_port = 24600 :设置用于被动模式的服务器最大端口号
Pasv_min_port = 24500 :设置用于被动模式的服务器最小端口号
Pam_server_name = vsftpd : 设置用户认证的PAM文件位置（/etc/pam.d/目录中对应的文件名）
userlist_enable = YES ：是否启用user_list用户列表文件
userlist_deny = YES ：是否禁止user_list列表文件中的用户帐号。
max_clients = 0 ：最多允许多少个客户端同时连接（0为无限制）
max_per_ip = 0 ：对来自同一个ip地址的客户端，最多允许多少个并发连接（0为无限制）
```

## 基于匿名访问的FTP服务
Vsftpd服务可以使用linux主机中的系统用户账号作为登录FTP的帐号，包括匿名访问和用户验证两种方式。
匿名访问的FTP服务:访问匿名FTP服务器时，不需要密码，任何人都可以使用，非常方便，当需要提供公开访问的文件下载资源或者让用户上传一些不需要保密的数据资料时，可以选择搭建匿名FTP服务器。
```bash
yum -y -install vsftpd
```
设置匿名访问的FTP服务器需要三步
```bash
1、准备匿名FTP访问的目录
2、开放匿名用户配置并启动vsftpd服务
3、测试匿名FTP访问
```
具体步骤
1、准备匿名访问的目录
```bash
FTP匿名用户对应的系统用户为ftp，其家目录为/var/ftp/。也就是匿名访问vsftpd服务时所在的FTP根目录，基于安全性考虑，FT根目录的权限不允许匿名用户或者其他用户有写入权限（否则会报500错误）
为了测试，可以在/var/ftp/目录下创建一个用于下载的测试文件，例如执行一下操作复制一个文件到/var/ftp目录下。

cp  /etc/issue  /var/ftp/

/var/ftp/目录下默认设置了一个名为pub的子目录，可以给匿名访问FTP时上传文件使用。
将/var/ftp/pub目录赋予匿名用户ftp写入权限：
chown  ftp  /var/ftp/pub （ftp这个目录权限不能给777.最多755。777的话。连接时会报错）
```
2、开放匿名用户配置并启动vsftpd服务
```bash
配置vsftpd服务时，是否开启匿名FTP访问取决于配置项“anonymous_enable”，只要将其设置为“YES”即可。
启用此项后默认只有读取权限，只能查看和下载文件，若要允许匿名用户有上传文件的权限，就需要开放更多的配置，主要有以下几项：
write_enable = YES             启用任何形式的写入权限
anon_upload_enable = YES       允许匿名用户上传文件
anon_mkdir_write_enable = YES   允许匿名用户可创建目录
anon_other_write_enable = YES   允许匿名用户有写入权限
以上配置项应根据实际需要选择设置。

重启vsftpd服务
systemctl   restart  vsftpd
```
3、测试匿名FTP访问
```bash
在linux的字符界面中，可使用ftp命令进行测试。
安装ftp命令 
yum  -y  install  ftp

例如：执行一下操作可以匿名登录到FTP服务器192.168.0.136
ftp 192.168.0.136
…………
Name(192.168.0.136:root):ftp 	  输入用户名ftp
passwd:         		直接回车即可
…………ftp> 		成功

成功登录后执行“？”或者“help”会显示出登录环境中的可用命令。

或者在windows主机中可直接在“我的电脑”或“计算机”地址栏中输入URL地址访问，如ftp://192.168.0.125
```
## 搭建用户验证的FTP服务
vsftpd可以直接使用linux主机的系统用户作为FTP帐号，提供基于用户名/密码的登录验证。用户使用系统用户帐号登录FTP服务器后将默认位于自己的家目录中，且在家目录中拥有读写权限。

1、基于本地用户验证
```bash
使用基于本地用户验证只需要打开local_enable 、write_enable两个配置项即可，为了提高上传文件的权限，可以将权限掩码设定为077，若还希望将所有的用户禁锢在其家目录中，可以添加chroot_local_user配置项，否则用户将能够任意切换到服务器的任意目录中，这样就会带来一定的安全隐患，建议将用户禁锢。
vim  /etc/vsftpd/vsftpd.conf
local_enable = YES            允许本地系统用户访问
write_enable = YES            允许任何形式的写入权限
local_umask = 077  本地用户所上传文件的默认权限掩码值
chroot_local_user = YES		将FTP本地用户禁锢在家目录中
```
在访问要求用户验证的FTP服务器时，如果选用URL地址的形式，必须知道FTP帐号名称。
如：访问"ftp://name@192.168.0.125"。可以根据提示输入密码进行验证。也可以在URL地址中直接指定密码"ftp://name:passwd@192.168.0.125"

2、使用user_list用户列表文件
当vsftpd服务器开放了"local_enable"配置项以后，默认情况下除了root外所有的系统用户都可以登录到此FTP服务器的，若只希望为一小部分系统用户开放FTP服务，则需要开放用户列表控制相关配置项，其中主要包括userlist_enable、userlist_deny。

例如：执行一下操作后，vsftpd服务器将只允许bad、tom和jerry这三个用户登录。
```bash
Vim  /etc/vsftpd/user_list
    bad
    tom
    jerry

Vim  /etc/vsftpd/vsftpd.conf
    userlist_enable = YES   # 开启user_list用户列表文件
    userlist_deny = NO    # 不禁用user_list列表文件中的用户
    allow_writeable_chroot=YES  # 从2.3.5之后vsftpd增强了安全检查  基于用户认证必须添加此配置 
```
3、测试用户FTP访问
```bash
systemctl   restart  vsftpd
```

## vsftpd服务的其他常用配置
1、修改vsftpd服务的监听地址、端口
```bash
listen = YES 			 允许独立监听服务
listen_address = 192.168.0.136
listen_port = 21212
重启生效即可
systemctl   restart  vsftpd
```
2、允许使用FTP服务的被动模式
```bash
......
pasv_enable = YES
pasv_min_port = 24500
pasv_max_port = 24600
重启生效即可：systemctl   restart  vsftpd
```
3、限制FTP的并发连接数、传输速率
```bash
vim  /etc/vsftpd/vsftpd.conf
max_clients = 10     限制并发客户连接数最多10个
max_per_ip = 5      限制每个ip地址的连接数最多5个
anon_max_rate = 100000  限制匿名用户传输速率为100kb/s
local_max_rate = 200000  限制本地用户传输速率为200kb/s
重启生效即可：systemctl   restart  vsftpd
```

## 基于虚拟用户的FTP服务
vsftpd服务使用Berkeley DB格式的数据库文件来存放虚拟用户账户，建立这种数据库需要用到db_load工具，由db4-utils软件包提供。

虚拟用户建立的大体步骤
    1、创建文本格式的用户名、密码列表
    2、创建Berkeley DB格式的数据库文件
    3、添加虚拟用户的映射账号、创建FTP根目录
    4、为虚拟用户建立PAM认证文件
    5、修改vsftpd配置、添加虚拟用户支持
    6、为不同的虚拟用户建立独立的配置文件
    7、测试

1、创建文本格式的用户名和密码列表
```bash
首先需要建立文本格式的用户名、密码列表文件，奇数行为用户名，偶数行为上一行中用户所对应的密码。
例如：添加两个用户hali、bote密码分别为123、789可以执行以下操作：
vim /etc/vsftpd/vusers.list      （为新建文件，名字任意）
    hali
    123
    bote
    789
```
2、创建Berkeley DB格式的数据库文件
```bash
有了文本格式的用户名、密码列表文件以后，以此文件为数据源，通过db_load工具创建处Berkeley DB格式的数据库文件。
cd  /etc/vsftpd
db_load  -T  -t  hash  -f  vusers.list  vusers.db

file  vusers.db    查看转换后的文件类型
为了提高此文件的安全性，chmod 600 vusers.db    防止数据外泄
```
3、添加虚拟用户的映射账户，创建FTP根目录
```bash
vsftpd服务器对虚拟用户的控制采用了映射的控制方式，将所有虚拟用户对应到同一个系统用户，该系统用户家目录作为所有虚拟用户登录后共同的FTP根目录，因此还需要添加一个对应的系统用户账户（此账户无需设置登录密码和登录shell）
创建好的虚拟用户的帐号数据文件之后，还需要对vsftpd服务的配置做相应的调整，以便识别并读取新的用户信息，在vsftpd服务器中，用户认证提供PAM机制实现，该机制包含灵活的选择认证方式。

useradd   long
```
4、为虚拟用户建立PAM认证文件
```bash
vsftpd服务默认的PAM认证文件为/etc/pam.d/vsftpd。该文件适用于linux主机的系统用户账户进行认证，若要读取虚拟用户的账户数据文件，则需要创建新的PAM认证配置。
例如：可以参考一下内容在/etc/pam.d/目录下建立一个名为vaftpd.vu的新文件。
vim  /etc/pam.d/vsftpd.vu
    #%PAM-1.0
    auth  required  pam_userdb.so  db=/etc/vsftpd/vusers
    account  required  pam_userdb.so  db=/etc/vsftpd/vusers
保存退出即可
```
上述PAM配置内容中，通过“db=/etc/vsftpd/vusers”参数指定了虚拟用户数据库文件位置（省略 .db扩展名）即对应为“/etc/vsftpd/vusers.db”文件。

5、修改vsftpd配置、添加虚拟用户支持
```bash   
在vsftpd.conf配置文件中添加guest_enable 、guest_username配置项，将访问FTP服务的所有虚拟用户对应到同一系列用户账户long，并修改pam_server_name配置项，指向上一步建立的 /etc/pam.d/vsftpd.vu认证文件。
vim  /etc/vsftpd/vsftpd.conf
    .........
    local_enable = YES      	允许本地系统用户访问
    write_enable = YES				允许可写权限
    anon_umask = 022				匿名用户所上传文件的默认权限掩码值
    guest_enable = YES				启用用户映射功能
    guest_username = long    指定映射的系统用户名称
    pam_service_name = vsftpd.vu			指定新的PAM认证文件

```
在vsftpd服务中，虚拟用户被默认作为匿名用户进行处理，以降低权限，因此对应的配置项通常以 anon_ 开头，例如：在设置虚拟用户所上传文件的默认权限掩码时，应采用配置项为anon_umask而不是local_umask。

6、为不同的虚拟用户建立独立的配置文件
```bash
通过前面的几个步骤，实际上已经可以重新加载vsftpd并提供服务了，使用任意一个虚拟账号都可以登录FTP服务器并下载文件，但应为所有的虚拟用户都映射到了同一个系统用户账户，因此FTP访问权限也是相同的要么都可以，要么都不可以。
若要为不同的虚拟用户账号设置不同的访问权限，可以通过为每个虚拟用户建立单独的配置文件实现，为FTP用户启用独立配置文件，需要修改vsftpd.conf配置文件，添加一条配置项 user_config_dir
例如：设置表示将从/etc/vsftpd/vusers_dir/目录中查找每个用户的独立配置文件：   、

vim  /etc/vsftpd/vsftpd.conf
.........
user_config_dir = /etc/vsftpd/vusers_dir

```
有了上述配置以后，就可以在/etc/vsftpd/vusers_dir/目录中为每个用户分别建立配置文件。
```bash
例如：用户hali仍然只有默认的下载权限，而boli有上传文件和目录的权利，可以执行一下操作：
mkdir  /etc/vsftpd/vusers_dir			创建目录
cd  /etc/vsftpd/vusers_dir
touch  hali               为hali创建空的配置文件

vim  bote             为bote创建配置文件
anon_upload_enable = YES
anon_mkdir_write_enable = YES
```
重启生效即可
```bash
systemctl   restart  vsftpd
```

在vsftpd.conf文件中启用了"user_config_dir"配置项以后，应该为每一个虚拟用户都建立一个单独的配置文件（可以是空文件），否则该用户可能无法登录，在每个用户的独立配置文件中，可以添加新的配置项来限制访问权限、下载速率等。
有了虚拟用户数据文件，并未vsftpd正确添加了虚拟用户支持以后就可以重新加载vsftpd服务，用户可以使用FTP客户端程序访问该FTP服务器，以虚拟用户账号进行测试，根据之前配置过程，至少确认一下结果。
1、用户hali可以登录，并能够正常浏览，下载文件，但不能上传。
2、用户bote可以登录，正常浏览、下载、上传文件。
3、Linux主机中的系统用户无法登录。