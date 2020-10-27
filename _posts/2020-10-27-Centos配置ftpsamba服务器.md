---
layout:     post
title:      Centos配置ftp/samba服务器
subtitle:   centos configure ftp /samba
date:       2020-10-27
author:     AGod
header-img: img/secondc.jpg
catalog: true
tags:
    - centos,ftp
---

# Centos中完成FTP服务器的部署

 

\`# yum install vsftpd* -y    没有挂载,请自行挂载.`

`` 

`\# vim /etc/vsftpd/vsftpd.conf`

`anonymous_enable=YES`

`将上面的注释,或者改为NO`

`添加:`

`local_root=/var/ftpsite`

`pasv_enable=YES`

`guest_enable=YES           #开启虚拟用户`

`guest_username=vuser        #虚拟用户名`

`user_config_dir=/etc/vsftpd_user_conf`

`保存退出.`

`` 

`\# cd /etc/vsftpd` 

`\# useradd vuser`

`\# vim vuser`

`ftpuser1`

`ftpuser1`

`ftpuser2`

`ftpuser2`

`保存退出`

`然后使用vuser文件创建一个密码DB文件。`

`\# db_load -T -t hash -f vuser vuser.db`

`` 

`\# vim /etc/pam.d/vsftpd`

`注释其他选项,添加.`

`auth sufficient pam_userdb.so     db=/etc/vsftpd/vuser`

`account sufficient  pam_userdb.so db=/etc/vsftpd/vuser`

`保存退出`

`` 

`\# cd /etc/vsftpd_user_conf 编辑对应用户的配置文件`

`vim ftpuser1添加:`

`local_root=/home/vsftpd`

`Anon_upload_enable=yes`

`Download_enable=yes`

`Anon_mkdir_write_enable=yes`

`Anon_other_write_enable=yes`

`Cmds_denied=DELE`

`` 

`vim ftpuser2添加:`

`local_root=/home/vsftpd`

`download_enable=yes`

`anon_other_write_enable=no`

`anon_upload_enable=no`

`` 

`\# systemctl restart vsftpd.service`

`\# firewall-cmd --add-service=ftp --permanent`

`\# firewall-cmd --reload`

`或者`

`\# systemctl stop firewalld`

`\# systemctl disable firewalld`

 

 

***如果出现错误，关闭selinux,自行关闭。***

 

 

**从2.3.5之后，vsftpd增强了安全检查，如果用户被限定在了其主目录下，则该用户的主目录不能再具有写权限了！如果检查发现还有写权限，就会报该错误。**

 **要修复这个错误，可以用命令chmod a-w /home/user去除用户主目录的写权限，注意把目录替换成你自己的。**

 

如果还有错误，在vsftpd的主配置文件中，添加：

`allow_writeable_chroot=YES`

`` 

`\# systemctl restart vsftpd.service`

然后重启服务即可。如果还不行，请自行百度。
 

 

# Centos-B2中完成Samba服务部署

 

\`# yum install samba* -y`

`` 

`\# useradd user1;useradd user2;useradd user3 ;useradd manager`

`\# groupadd administration;groupadd sales`

`\# usermod -G administration user1;usermod -G administration user2`

`\# usermod -G sales user3`

`\# smbpasswd -a user1;smbpasswd -a user2;smbpasswd -a user3 ;smbpasswd -a manager`

`` 

`\# mkdir /opt/administration_share ; mkdir /opt/sales_share ; mkdir /opt/public_share`

`` 

`\# chown manager:administration /opt/administration_share`

`\# chown manager:sales /opt/sales_share`

`\# chmod 777 /public_share`

`` 

`\# vim /etc/profile`

![](G:\Users\GANJI\Documents\GitHub\agod-tiger.github.io\img\samba.png)

`修改上面的002为022`

`保存退出`

`\# source /etc/profile`

`` 

`\# vim /etc/samba/smb.conf`

`添加`

`[administration_share]`

`path = /opt/administration_share`

`writeable = yes`

`` 

`[sales_share]`

`path = /opt/sales_share`

`writeable = yes`

`` 

`[share]`

`path = /opt/public_share`

`writeable = yes`

`保存退出。`

`\# systemctl restart smb`