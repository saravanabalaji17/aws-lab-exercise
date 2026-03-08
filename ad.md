

#sevice iptables off
#iptableds -F


#setenforce 0
#vi  vi /etc/sysconfig/selinux
SELINUX=disabled


#hostname vm-1.vspheretech.com
#hostname -f


#vi /etc/hosts
192.168.44.9 vm-1.vspheretech.com

#vi /etc/sysconfig/network
HOSTNAME=vm-1.vspheretech.com

#cat /etc/resolv.conf
nameserver <AD>


#hostname -f

#nslookup win-ad.vspheretech.com

#ntpdate win-ad.vspheretech.com
#date

# cat centos.repo
[base]
name=CentOS-6 - Base
baseurl=http://ftp.iij.ad.jp/pub/linux/centos-vault/centos/6/os/x86_64/
enabled=1
gpgcheck=0

[updates]
name=CentOS-6 - Updates
baseurl=http://ftp.iij.ad.jp/pub/linux/centos-vault/centos/6/updates/x86_64/
enabled=1
gpgcheck=0

[extras]
name=CentOS-6 - Extras
baseurl=http://ftp.iij.ad.jp/pub/linux/centos-vault/centos/6/extras/x86_64/
enabled=1
gpgcheck=0



#yum install nmap ntp samba telnet -y

#yum -y install adcli sssd authconfig pam_krb5 samba-common


#authconfig \
--enablekrb5 \
--krb5kdc=vspheretech.com \
--krb5adminserver=vspheretech.com \
--krb5realm=VSPHERETECH.COM \
--enablesssd \
--enablesssdauth \
--update

# cat /etc/krb5.conf
[logging]
 default = FILE:/var/log/krb5libs.log
 kdc = FILE:/var/log/krb5kdc.log
 admin_server = FILE:/var/log/kadmind.log

[libdefaults]
 default_realm = VSPHERETECH.COM
 dns_lookup_realm = false
 dns_lookup_kdc = false
 ticket_lifetime = 24h
 renew_lifetime = 7d
 forwardable = true

[realms]
 VSPHERETECH.COM = {
  kdc = vspheretech.com
  admin_server = vspheretech.com
 }

[domain_realm]
 .vspheretech.com = VSPHERETECH.COM
 vspheretech.com = VSPHERETECH.COM


#kinit user@DOMAIN.COM  #kinit administrator
#klist


#adcli info VSPHERETECH.COM
#adcli join VSPHERETECH.COM -U administrator


# vi /etc/sssd/sssd.conf
[sssd]
domains = vspheretech.com
config_file_version = 2
services = nss, pam

[domain/vspheretech.com]
ad_domain = vspheretech.com
krb5_realm = VSPHERETECH.COM
realmd_tags = manages-system joined-with-samba
cache_credentials = True
id_provider = ad
krb5_store_password_if_offline = True
default_shell = /bin/bash
ldap_id_mapping = True
use_fully_qualified_names = False
fallback_homedir = /home/%d/%u
access_provider = ad


#chmod 600 /etc/sssd/sssd.conf

vi /etc/pam.d/system-auth
# add follows to the end (generate home directory if not)
session     optional      pam_mkhomedir.so skel=/etc/skel umask=077 


#/etc/rc.d/init.d/sssd start
#chkconfig sssd on


#id sm
#getent passwd sm
#su - sm
