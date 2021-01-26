# Implement and configure an HTTP Server

- `Listen :80` 

```
DocumentRoot "/var/www/html"

# Allow to look into this directory

<Directory "/var/www">
AllowOverrided None
Required all granted
</Directory>

<Directory "/var/ww/html">

Options Indexes FollowSymlinks

</Directory>
```

- There are types in  `/etc/mime.types` and `/etc/httpd/conf/magic`

- Own user data directory `mod_userdir`

```
<IfModule mod_userdir.c>
  UserDir disabled

  # Enable requests to serve ~/user/public_html directory
  #UserDir public_html 
</IfModule>
```


# Implementing NTP server


## Server
- Install `chrony` (gets your clock more intime with upstream)
- Allow clients to syncrhonize even if upstream is down `local stratum 10`
- Check sources `chronyc sources` or `chronyc  tracking`, `chronyc sourcestats -v`

## Client

- Install chrony and add `server NTP_SERVER`


# Configure a Network Logging Server


## Server
```
more /etc/rsyslog.d/blackbox.conf

if $fromhost-ip == '192.168.1.63' then {
  action(type="omfile" file="/var/log/blackbox.log")
  stop
}
```


## Client

- Look into `$ActionQueueType LinkedList`

```
# /etc/rsyslog.conf (optional the port :514)
*.* @@192.168.1.162
```

# Configure a DHCP server

## Server

- Install `isc-dhcp-server` (uncomment authoritative if it is the only one)

```
option domain-name "la.org";

authoritative;

subnet 10.0.0.0 netmask 255.255.255.0 {
   range 10.0.0.100 10.0.0.150;
}
```
- enable and start the server `systemcl enable|start dhcpd`

- cat `/var/lib/dhcp/dhcpd.leases`


## Client

- Look at netplan DHCP


# Implement and configure SMTP Service

- Install `postfix telnet mailx`
- Configuration `postconf -e "mydestination = FQDN, localhost.localdomain, localhost"`
- Networks `postconf -e "mynetworks = 127.0.0.0/8, 172.31.0.0/16"`
- Interfaces `postconf -e "inet_interfaces = all"`


# Configure HTTP Proxy Server

- Install `nginx` and set an upstream.

```
server {
	location / {
	  proxy_pass http://localhost:3000;
	}
}
```

# Configure Host-based and User-based Security 

- TCPWrappers  (ldd /usr/bin/sshd | grep libwrap)
- `/etc/hosts.allow and /etc/hosts.deny`
- Restart SSHD after changes to this files


**/etc/hosts.allow**

```
sshd: 172.31.47.0/24 

```

**/etc/hosts.deny**
```
sshd: 172.31.47.221 \ : spawn /bin/echo $(/bin/date) access denied >> /var/log/sshd_host_denied \:deny

```


# Configure an OpenLDAP server

- Install `slapd, ldap-utils`
- Configure your domain `dpkg-reconfigure slapd (lab.cz and dc=lab,dc=cz)`
- Set password `olcRootPW`

```
slappasswd -h {SSHA} -s lapasswd
{SSHA}QmLwRb7DBZ0YAhXhvoHJs25oTXeQI3h7
```
- Ldap monitor (lookup this)


- ldapsearch `ldapsearch -LLL -h localhost -D "cn=admin,dc=lab,dc=cz" -b dc=lab,dc=cz -s sub  -W`
- `ldappasswd -s alice -W -D "cn=admin,dc=lab,dc=cz" -x "uid=alice,ou=People,dc=lab,dc=cz"`
- `ldapdelete -W -D "cn=admin,dc=lab,dc=cz" uid=alice,ou=People,dc=lab,dc=cz`
 
## Client

 - Install `sudo apt -y install libnss-ldap libpam-ldap ldap-utils`

 - `dpkg-reconfigure libnss-ldap`
 - `edit nsswitch.conf`


# Configure a PXE Boot server

 // TODO: find a modern and easy setup


# Configure an Authoritative DNS Server

- `apt install bind9 -yq`

**/etc/bind/named.conf.local**

```
zone "lab" {
	type master;
	file "/etc/bind/lab";
};
```


**/etc/bind/lab**

```
;
; BIND data file for local loopback interface
;
$TTL	64800
@	IN	SOA	ns.lab. mx.lab. (
			      1		; Serial
			 604800		; Refresh
			  86400		; Retry
			2419200		; Expire
			 604800 )	; Negative Cache TTL
;
@	IN	NS	ns.lab.
ns	IN	A	192.168.1.162
oc	IN	A       192.168.1.162
black	IN	A 	192.168.1.63
ryzen	IN 	A	192.168.1.62
nas	IN	A       192.168.1.26
lab	IN 	A 	192.168.1.19
```

- Check config :  `named-checkconf  /etc/bind/named.conf`
- Check zone: `named-checkzone <ZONENAME> <FILENAME>`


// TODO: PTR reverse lookup

// TODO: Bind views
