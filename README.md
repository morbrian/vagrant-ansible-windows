
Ansible controller on CENTOS-7 with ability to control Windows via windows remoting and kerberos authentication.

Once the packages are installed (performed by provisioning in Vagrantfile), configure system for your Windows environment.

Edit the /etc/krb5.conf file on the vm with updates to the following sections:

        [realms]
        FORWARDSLOPE.LOCAL = {
          kdc = DOMAIN-CONTROLLER
          kdc = DOMAIN-CONTROLLER.DOMAIN.COM
        }
        
        [domain_realm]
        DOMAIN.COM = DOMAIN.COM

If unsure of appropriate values, then usng the command `klist.exe` on your windows system will reveal the correct domain controller values.

Once krb5.conf is updated, the following commands can test authentication with the domain controller.

On the linux vm run:

1. `kinit username@DOMAIN.COM` will request a kerberos ticket, and success is indicated by no output after password is entered.

    1. If the username is not already known, the `klist.exe` command can help identify a correct username.

2. `klist` will display a list of active tickets, the only item will be the ticket requested above on the first time configuring the system.


References:

* EPEL notes: http://fedoraproject.org/wiki/EPEL

        curl -O https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
        rpm -Uvh epel-release-latest-7.noarch.rpm

* CENTOS 6 python upgrade: https://danieleriksson.net/2017/02/08/how-to-install-latest-python-on-centos/

* Ansible install (basic): http://docs.ansible.com/ansible/intro_installation.html

* Ansible Windows: http://docs.ansible.com/ansible/intro_windows.html

