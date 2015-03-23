Initialize
==========

![Version](https://img.shields.io/badge/version-1.0-green.svg)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](http://opensource.org/licenses/MIT)

Configuration script for linux systems to setup a web and mail server hosting multiple domains.  The script is currently built specifically for CentOS 6.3, though it could easily be, and eventually will be, adapted for other linux distributions and versions.  The goal is a lightweight, fast and secure server configured in minutes.

Overview
--------

The __initialize__ script does the following:

 - Configure RPMforge.
 - Updates the system and installs some packages.  See full list below.
 - Configures the system hostname and modifies `/etc/hosts`.
 - Creates an administrative user account.
 - Adds some usefull dotfiles.
 - Secures the ssh server.
 - Configures iptables firewall, restricting to ssh, web and mail traffic.
 - Enables SELinux.
 - Configures and secures mysql.
 - Configures and enables NTP.
 - Configures php with some good defaults for security.
 - Configures Apache web server with virtual hosts.
 - Configures Postfix as the mail tranfer agent.
 - Configures Dovecot as the IMAP server and Local Delivery Agent.
 - Configures Amavis, Spamassassin and clamav to work with Postfix/Dovecot.
 - Creates an administrator email account, and forwards root, webmaster and
   postmaster.
 - Generates a self-signed certificate.

The __initialize__ script does not do the following:

 - Install web based management tools.
 - ...

The __initialize__ script will eventually do the following:

 - Support other versions of CentOS and other ditributions of Linux.
 - Setup Pyzor and DCC with Spamassassin.
 - Allow selection of which components to configure/install.

Prerequisites & Assumptions
---------------------------

 - CentOS 6.3
 - Configured IP
 - Configured DNS
 - Console or SSH access
 - Command Line & VI Skills

It is assumed that CentOS is already installed with a valid IP address and that DNS is pointing to the server.  Make sure DNS directs __mail.yourdomain.com__, __yourhostname.yourdomain.com__, __www.yourdomain.com__ and __yourdomain.com__ to your server.  Only __mail.yourdomain.com__ and __yourdomain.com__ are needed to have a working mail and web server, but the others don't hurt and should probably be configured as a best practice.

The script will be run from the command line and currently kicks off a vi session to edit the sudoers file, so some understanding of the command line and vi will be handy.

Usage
-----

### Download

You have a couple of options:

1. Download a zip of the latest [HERE](https://github.com/codybuell/initialize/zipball/master).

2. Clone the latest with:

        git clone https://github.com/codybuell/initialize.git

### Initialize Script

Once you have the initalize script and templates, place them both on your server in the same path then execute.  The script must be run as root and from the directory in which it is located.

    ./initialize [-h|--help] [-i|--initialize] [-V|--new-vhost] [-E|--new-email]

          -h, --help        Display usage information.
          -i, --initialize  Run the full initialize script.
          -V, --new-vhost   Provision a new Apache virtualhost.
          -E, --new-email   Provision a new email account.

What It Does
------------

### Replaced System Files

Any file that is modified by the script is first backed up by creating a copy with __.orig__ appended to the name.

### Installed Packages

    man wget vim telnet crypto-utils mod_ssl tree bind-utils ntp httpd
    mysql-server php php-mysql postfix dovecot dovecot-mysql mailx sudo git ruby
    rubygems setroubleshoot amavisd-new clamav clamav-devel clamd spamassassin

This list does not include installed dependencies.

### Template Files

 - __10-auth.conf__                    - Dovecot auth config
 - __10-mail.conf__                    - Dovecot mail config
 - __10-master.conf__                  - Dovecot master config
 - __10-ssl.conf__                     - Dovecot ssl config
 - __20-imap.conf__                    - Dovecot imap config
 - __20-pop3.conf__                    - Dovecot pop3 config
 - __90-quota.conf__                   - Dovecot quota config
 - __auth-sql.conf.ext__               - Dovecot auth for sql users config
 - __bashrc__                          - Dotfile for bash environment
 - __dovecot-dict-sql.conf.ext__       - Dovecot quota tracking sql access config
 - __dovecot-sql.conf.ext__            - Dovecot sql auth config
 - __dovecot.conf__                    - Dovecot primary config file
 - __httpd.conf__                      - Apache config file
 - __index.php__                       - Placeholder for virtualhosts
 - __iptables__                        - Firewall config
 - __mail.isam.sql__                   - SQL dump file of mail database
 - __main.cf__                         - Postfix main.cf config file
 - __master.cf__                       - Postfix master.cf config file
 - __mysql_virtual_domains.cf__        - Postfix interface to mysql domains table
 - __mysql_virtual_forwardings.cf__    - Postfix interface to mysql forwardings table
 - __mysql_virtual_mailbox_limits.cf__ - Postfix interface to mysql users quota limits
 - __mysql_virtual_mailboxes.cf__      - Postfix interface to mysql users accounts
 - __mysql_virtual_transports.cf__     - Postfix interface to mysql transports table
 - __php.ini__                         - PHP config
 - __ssl.conf__                        - Apache ssl module config
 - __vhost.conf__                      - Apache virtualhost template file

### Mail Server Configuration

The mail server is configured with Postfix, Dovecot, Amavisd-new, Spamassassin and ClamAV.  Postfix accepts smtp connections on ports 25 and 587.  Dovecot accepts imap connections on ports 143 and 993.  SSL/TLS is enabled.  An email account administrator@yourdomain.com is created.

__administrator@yourdomain.com__

    Username: administrator@yourdomain.com
    Password: set through initialize script
    IMAP Server: mail.yourdomain.com
    SMTP Server: mail.yourdomain.com

### Web Server Configuration
 
 - Creates __/etc/httpd/vhost.d__ for virtualhost configurations.
 - Creates __/var/www/vhosts__ for virtualhosts document roots.
 - Creates an initial virtualhost based on prompts. See
   __/etc/httpd/vhost.d/yourdomain.com.conf__ and
   __/var/www/vhosts/yourdomain.com__.
 - Default/fallback virtualhost points to __/var/www/html__.
 - Adds a number of directives to __/etc/httpd/conf/httpd.conf__ for security,
   take some time and familiarize yourself with this file.
 - AllowOverride is set to None by default.  If possible try to put all needed
   directives in the __/etc/httpd/vhost.d/yourdomain.com.conf__ configuration
   files as you create virtualhosts.  If AllowOverride is necessary then set it
   there for virtualhosts individually.

### SELinux Notes

SELinux can cause a lot of headaches and so generally people will tell you to just turn it off.  Don't do that.  SELinux is your friend and a greate tool for securing your server.  Rather than turning it off, take a few moments to familiarize yourself with how it functions and incorporate it into your troubleshooting workflow, you will be a beter system administrator for it.  That said here are a few links and tools to get you started.

 - <http://wiki.centos.org/HowTos/SELinux>
 - <http://fedoraproject.org/wiki/SELinux>
 - SELinux logs are kept in `/var/log/audit/audit.log`
 - Search the log for `failed` resolutions:

        ausearch -sv no

 - Search the log file for AVC messages:

        sealert -a /var/log/audit/audit.log

### MySQL 'mail' Databases

__Domains Table__

List of virtual domains postfix receives mail for.

    .--------------.
    |  domains     |
    |--------------|
    |  domain.com  |
    `--------------`

__Users Table__

List of virtual email accounts.

    .------------------------------------------------------.
    |  email            |  password         |  quota       |
    |-------------------|-------------------|--------------|
    |  user@domain.com  |  [CRAM-MD5 HASH]  |  1048576000  |
    `------------------------------------------------------`

 - Quotas are tracked in bytes
 - 0 for unlimited quota
 - Passwords are stored in CRAM-MD5, use `doveadm -pw` to generate

__Forwardings Table__

List of forwards.  Emails sent to the source address will be forwarded to the
destination address.

    .---------------------------------------.
    |  source           |  destination      |
    |-------------------|-------------------|
    |  info@domain.com  |  user@domain.com  |
    `---------------------------------------`

__Transport Table__

List of transports for Postfix.  See <http://www.postfix.org/transport.5.html>.

    .----------------------------------.
    |  domain      |  transport        |
    |--------------|-------------------|
    |  domain.com  |  smtp:[1.2.3.4]   |
    `----------------------------------`

Testing
-------

 1. Check that the following services are running:

   - sshd
   - ntpd
   - iptables
   - mysqld
   - httpd
   - postfix (master)
   - dovecot
   - amavisd
   - clamd

        service [service name] status
        service --status-all

   _* spamd should be stopped as it is not run as a service with this
   configuration_

 2. Check that the server is listening on the following ports:

   - ??    (whatever you defined for ssh, default 22)
   - 80    (http)
   - 443   (https)
   - 25    (smtp)
   - 587   (smtp alternate)
   - 143   (imap)
   - 993   (imaps)
   - 10024 (amavis listening for postfix)
   - 10025 (postfix listening for amavis)

        netstat -ant

   _* pop3/ssl-pop (ports 110 & 995) are not enabled by default with this
   script, enable in `/etc/dovecot/dovecot.conf` by adding pop3 to the
   `protocols` declaration and restarting dovecot.  IPTables will have to be
   adjusted as well._

 3. Check the following logs for errors:

   - `/var/log/audit/audit.log`
   - `/var/log/messages`
   - `/var/log/maillog`
   - `/var/log/httpd/error_log`
   - `/var/log/httpd/ssl_error_log`

        tail -100 [log file]

 4. Navigate to the server in a browser.  Try both http and https.  Check the
    certificate information when connecting with ssl/tls.

 5. Test SMTP with telnet:

        telnet localhost 25
        ehlo localhost
        quit

   You should get output like:

        telnet localhost 25
        Trying ::1...
        Connected to localhost.
        Escape character is '^]'.
        220 mail.yourdomain.com ESMTP Postfix
        ehlo localhost
        250-mail.yourdomain.com
        250-PIPELINING
        250-SIZE 10240000
        250-VRFY
        250-ETRN
        250-AUTH PLAIN LOGIN CRAM-MD5
        250-AUTH=PLAIN LOGIN CRAM-MD5
        250-ENHANCEDSTATUSCODES
        250-8BITMIME
        250 DSN
        quit

 6. Test IPAM with telnet:

        telnet localhost 143
        ctrl + ]
        quit

   You should get output like:

        telnet localhost 143
        Trying ::1...
        Connected to localhost.
        Escape character is '^]'.
        * OK [CAPABILITY IMAP4rev1 LITERAL+ SASL-IR LOGIN-REFERRALS ID ENABLE
          IDLE STARTTLS AUTH=PLAIN AUTH=LOGIN AUTH=CRAM-MD5] Dovecot ready.
        ctrl + ]
        quit

 7. Test that Amavis is lintening for Postfix with telnet:

        telnet localhost 10024
        ehlo localhost
        quit

   You should get output like:

        telnet localhost 10024
        Trying ::1...
        telnet: connect to address ::1: Connection refused
        Trying 127.0.0.1...
        Connected to localhost.
        Escape character is '^]'.
        220 [127.0.0.1] ESMTP amavisd-new service ready
        ehlo localhost
        250-[127.0.0.1]
        250-VRFY
        250-PIPELINING
        250-SIZE
        250-ENHANCEDSTATUSCODES
        250-8BITMIME
        250-DSN
        250 XFORWARD NAME ADDR PORT PROTO HELO SOURCE
        quit

 8. Test that Postfix is listening for Amavis with telnet:

        telnet localhost 10025
        ehlo localhost
        quit

   You should get output like:

        telnet localhost 10025
        Trying ::1...
        telnet: connect to address ::1: Connection refused
        Trying 127.0.0.1...
        Connected to localhost.
        Escape character is '^]'.
        220 beta.yourdomain.com ESMTP Postfix
        ehlo localhost
        250-beta.yourdomain.com
        250-PIPELINING
        250-SIZE 10240000
        250-VRFY
        250-ETRN
        250-STARTTLS
        250-AUTH PLAIN LOGIN CRAM-MD5
        250-AUTH=PLAIN LOGIN CRAM-MD5
        250-ENHANCEDSTATUSCODES
        250-8BITMIME
        250 DSN
        quit

 9. Send some spammy emails to test Amavis:

        cd /usr/share/doc/amavisd-new-2.6.6/test-messages/
        perl -pe 's/./chr(ord($&)^255)/sge' <sample.tar.gz.compl | zcat | tar xvf -
        sendmail -i administrator@yourdomain.com < sample-virus-simple.txt
        sendmail -i administrator@yourdomain.com < sample-spam-GTUBE-junk.txt
        sendmail -i administrator@yourdomain.com < [DO A LISTING TO SEE FIND MORE]

 10. Send an email:

      mailx administrator@yourdomain.com

    You will be prompted for a subject, then enter the body text.  To finish and
    send create a new line, put in a period and hit enter.

License
-------

Initialize is open-sourced software licensed under the [MIT license](http://opensource.org/licenses/MIT).

Resources
---------

 - <http://www.postfix.org/>
 - <http://www.dovecot.org/>
 - <http://www.apache.org/>
 - <http://www.centos.org/>
 - <http://wiki.centos.org/HowTos/postfix_sasl>
 - <http://wiki.centos.org/AdditionalResources/Repositories/RPMForge>
 - <http://wiki.centos.org/HowTos/Amavisd>
 - <http://wiki.mediatemple.net/w/(ve):Install_Postfix_with_Dovecot_on_CentOS>
 - <http://www.howtoforge.com/virtual-users-and-domains-with-postfix-courier-mysql-and-squirrelmail-centos-6.2-x86_64>
