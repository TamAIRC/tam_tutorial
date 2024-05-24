# INSTALLATION INSTRUCTIONS DC - Samba - LDAP ON CENTOS 9

Set up the LDAP server, configuring Samba to use LDAP, and ensuring the client machines are correctly configured to authenticate against the LDAP server.

## Prerequisites

- Ensure that both the Samba server and the LDAP server are installed on your CentOS 9 machine.
- Ensure that the necessary packages for Samba and LDAP are installed. Use dnf to install them if needed.

### Install EPEL repository

```bash
sudo dnf config-manager --set-enabled crb
```

```bash
sudo dnf -y install epel-release epel-next-release
```

### Install Samba Server, LDAP Server and some Packages

```bash
dnf -y install samba samba-client samba-common openldap-clients openldap-servers
```

## Step 1: Configure LDAP Server

Make sure your LDAP server is configured and running. You might need to refer to the previous setup if not already done. Here is a quick guide to setting up OpenLDAP:

```bash
systemctl enable --now slapd
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/cosine.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/nis.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/inetorgperson.ldif
```

## Step 2: Configure Samba to Use LDAP

Modify the Samba configuration to integrate with LDAP. Edit the `/etc/samba/smb.conf` file:

```bash
sudo nano /etc/samba/smb.conf
```

Add or modify the following lines in the smb.conf file:

```conf
# See smb.conf.example for a more detailed config file or
# read the smb.conf manpage.
# Run 'testparm' to verify the config is correct after
# you modified it.
#
# Note:
# SMB1 is disabled by default. This means clients without support for SMB2 or
# SMB3 are no longer able to connect to smbd (by default).

[global]
        unix charset = UTF-8
        ; YOUR_WORKGROUP
        workgroup = SAMBA
        server string = Samba Server
        netbios name = SAMBASERVER
        security = user
        ; ldapsam:ldap://localhost or ldapsam:ldap://<ip address>
        passdb backend = ldapsam:ldap://localhost
        ; your domain. ex: hello.local
        ldap admin dn = cn=Manager,dc=hello,dc=local
        ldap suffix = dc=hello,dc=local
        ldap group suffix = ou=Group
        ldap user suffix = ou=People
        ldap machine suffix = ou=Computers
        ldap ssl = no

        # Logs
        log file = /var/log/samba/%m.log
        max log size = 50

        # add IP addresses you allow to access
        hosts allow = 127. 192.168.88.

        # add (no authentication)
        map to guest = Bad User

        printing = cups
        printcap name = cups
        load printers = yes
        cups options = raw

[homes]
        comment = Home Directories
        valid users = %S, %D%w%S
        browseable = No
        read only = No
        inherit acls = Yes

[printers]
        comment = All Printers
        path = /var/tmp/samba
        printable = Yes
        create mask = 0600
        browseable = No

[print$]
        comment = Printer Drivers
        path = /var/lib/samba/drivers
        write list = @printadmin root
        force group = @printadmin
        create mask = 0664
        directory mask = 0775

# any Share name you like
[Share]
        # specify shared directory
        path = /home/share
        # allow writing
        writable = yes
        # allow guest user (nobody)
        guest ok = yes
        # looks all as guest user
        guest only = yes
        # set permission [777] when file created
        force create mode = 777
        # set permission [777] when folder created
        force directory mode = 777
```

## Step 3: Configure LDAP for Samba

Create the necessary Samba LDAP entries. You can use LDIF files to create the required schema and initial user data. For example:

### 1. Samba LDAP Schema: Samba requires

To configure Samba with LDAP authentication on CentOS 9, you'll need to perform several steps, including setting up the LDAP server, configuring Samba to use LDAP, and ensuring client machines can authenticate against the LDAP server.

**Prerequisites**

- Ensure both Samba and LDAP packages are installed

```bash
sudo dnf install samba samba-client samba-common openldap-clients openldap-servers
```

#### Step 1: Configure LDAP Server

**1. Install and start OpenLDAP:**

```bash
sudo systemctl enable --now slapd
```

**2. Add required schemas:**

```bash
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/cosine.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/nis.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/inetorgperson.ldif
```

**3. Add Samba schema:**
Dowload `samba.ldif` from the Zentyal GitHub repository.

```sh
wget https://raw.githubusercontent.com/zentyal/samba/master/examples/LDAP/samba.ldif -O /etc/openldap/schema/samba.ldif
```

Add the schema:

```bash
sudo ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/samba.ldif
```
