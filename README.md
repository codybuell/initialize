Initialize
==========

Configuration script for linux systems to setup a web and mail server hosting
multiple domains.  The script is currently built specifically for CentOS 6.3,
though it could easily be adapted for other linux distributions and versions.
The goal is a lightweight, fast and secure server configured in minutes.

Overview
--------

  what it does
  what it does not
  what it hopes to do

Prerequisites & Assumptions
---------------------------

what is required
  how to do it
distro installation
  ...
setup ip
setup dns
you know vi and are comfortable with the command line

Usage
-----

### Download

You have a couple of options:

1. Download a zip of the latest [HERE](https://github.com/codybuell/initialize/zipball/master)

2. Clone the latest with:

        git clone https://github.com/codybuell/initialize.git

### Initialize Script

Once you have the initalize script and templates, place them both on your server
in the same path then execute.

    ./initialize

    step by step list of prompts

** note that the script must be run as root and from the same directory in which
it is located. **

### Helper Scripts

Currently they don't exist!  But the plan is to create helper scripts to help
manage email accounts and setup new virtual domains.  Though if you are using
this script, chances are you already know how to do those things.

What It Does
------------

### Replaced System Files

Any file that is modified by the script is first backed up by creating a copy
with `.orig` appended to the name.

### Installed Packages

    man wget vim telnet crypto-utils mod_ssl tree bind-utils ntp httpd
    mysql-server php php-mysql postfix dovecot dovecot-mysql mailx sudo git ruby
    rubygems setroubleshoot amavisd-new clamav clamav-devel clamd spamassassin

** note this list does not include installed dependencies **

### Template Files

__10-auth.conf__                    - Dovecot auth config
__10-mail.conf__                    - Dovecot mail config
__10-master.conf__                  - Dovecot master config
__10-ssl.conf__                     - Dovecot ssl config
__20-imap.conf__                    - Dovecot imap config
__20-pop3.conf__                    - Dovecot pop3 config
__90-quota.conf__                   - Dovecot quota config
__auth-sql.conf.ext__               - 
__bashrc__                          - Dotfile for bash environment
__dovecot-dict-sql.conf.ext__       - Dovecot quota tracking sql access config
__dovecot-sql.conf.ext__            - Dovecot sql auth config
__dovecot.conf__                    - Dovecot primary config file
__httpd.conf__                      - Apache config file
__iptables__                        - Firewall config
__mail.isam.sql__                   - SQL dump file of mail database
__main.cf__                         - Postfix main.cf config file
__master.cf__                       - Postfix master.cf config file
__mysql_virtual_domains.cf__
__mysql_virtual_forwardings.cf__
__mysql_virtual_mailbox_limits.cf__
__mysql_virtual_mailboxes.cf__
__mysql_virtual_transports.cf__
__php.ini__
__vhost.conf__                      - Virtualhost template file

### Mail Server Configuration
 features
 how it is configured

### Web Server Configuration
 features
 how it is configured

### SELinux Notes
 selinux is your friend, here's a primer

### MySQL 'mail' Databases

__Domains Table__

    .--------------.
    |  domains     |
    |--------------|
    |  domain.com  |
    `--------------`

a list of virtual domains postfix receives mail for

__Users Table__

    .------------------------------------------------------.
    |  email            |  password         |  quota       |
    |-------------------|-------------------|--------------|
    |  user@domain.com  |  [CRAM-MD5 HASH]  |  1048576000  |
    `------------------------------------------------------`

  0 means unlimited in quota
  quotas are tracked in bytes
  cram-md5 and how to generate

__Forwardings Table__

    .---------------------------------------.
    |  source           |  destination      |
    |-------------------|-------------------|
    |  info@domain.com  |  user@domain.com  |
    `---------------------------------------`

Emails sent to the source address will be forwarded to the destination address.

__Transport Table__

How to handle transport for domains? where to send x domains email...??  tells
postfix what transport agent to use for each domain.

    .----------------------------------.
    |  domain      |  transport        |
    |--------------|-------------------|
    |  domain.com  |  smtp:[1.2.3.4]   |
    `----------------------------------`

    cards.example.com    :         delivers to local mailserver?
    example.com          :[192.168.19.198]  delivers to new ip without mx lookup?
    bar.com              local:
    virt-domain.com      virtual:  requires virtual_tranport = virtual in main.cf??
  destination is changed user remains the same:
    mydomain.com         :[mail.otherdomain.com]
    mydomain.org         :[mail.otherdomain.com]
    hisdomain.net        :[mail.otherdomain.com]


Testing
-------


Resources
---------

<http://www.postfix.org/>
<http://www.dovecot.org/>
<http://www.apache.org/>
<http://www.centos.org/>
<http://wiki.centos.org/HowTos/postfix_sasl>
<http://wiki.centos.org/AdditionalResources/Repositories/RPMForge>
<http://wiki.centos.org/HowTos/Amavisd>
<http://wiki.mediatemple.net/w/(ve):Install_Postfix_with_Dovecot_on_CentOS>
<http://www.howtoforge.com/virtual-users-and-domains-with-postfix-courier-mysql-and-squirrelmail-centos-6.2-x86_64>
