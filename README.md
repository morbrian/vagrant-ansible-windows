# Provision Windows 10 systems with Linux Ansible Controller

Ansible controller on CENTOS-7 with ability to control Windows via windows remoting and kerberos authentication.

# Windows Client Requirements

The Windows client must be prepared to support powershell scripts to run remotely.

1. Clone the ansible repository to the Windows client:

        git clone https://github.com/ansible/ansible

2. Open PowerShell

3. Start a new shell from the powershell console as admin

        Start-Process powershell -Verb runAs

4. In new admin powershell console update the execution policy to allow the configuration script to run.

5. Run the configuration script from the admin console

        cd ansible/examples/scripts
        ./ConfigureRemotingForAnsible.ps1


# Linux Requirements

Testing was performed with:
CENTOS 7, Ansible 2.3, Python 2.7

It is presumably possible to provision CENTOS 6 with an updated of Python and Ansible, however CENTOS 7 is recommended as it is far easier to prepare.

The Vagrantfile will automatically create a CENTOS 7 VM with the required Python, pip, Ansible versions installed and configured.

The following extra step must be performed to configure the Linux system for your Windoww domain.

Edit the /etc/krb5.conf file on the vm with updates to the following sections:

        # If unsure of windows domain values, use the command `klist.exe` on your windows to reveal the correct domain controller values.
        [realms]
        DOMAIN-CONTROLLER = {
          kdc = DOMAIN-CONTROLLER
          kdc = DOMAIN-CONTROLLER.DOMAIN.COM
        }
        
        [domain_realm]
        DOMAIN.COM = DOMAIN.COM


Once krb5.conf is updated, run the following commands to test authentication with the domain controller.

On the linux vm run:

1. `kinit` username@DOMAIN.COM` will request a kerberos ticket, and success is indicated by no output after password is entered.

2. `klist` will display a list of active tickets


References:

* EPEL notes: http://fedoraproject.org/wiki/EPEL

        curl -O https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
        rpm -Uvh epel-release-latest-7.noarch.rpm

* CENTOS 6 python upgrade: https://danieleriksson.net/2017/02/08/how-to-install-latest-python-on-centos/

* Ansible install (basic): http://docs.ansible.com/ansible/intro_installation.html

* Ansible Windows: http://docs.ansible.com/ansible/intro_windows.html

# Additional Windows and Linux config tips

1. Server not found in Kerberos database

If you use the IP address in your ansible inventory file this message is the result.
Instead...
use the hostname (not ip address) in the ansible inventory file.
If windows hostname not in DNS, Edit hosts file, map the hostname of the target windows VM to it's IP Address

2. Configuration file does not specify default realm

The /etc/krb5.conf file should specify default_realm under libdefaults section, for example:
	[libdefaults]
	 dns_lookup_realm = false
	 ticket_lifetime = 24h
	 renew_lifetime = 7d
	 forwardable = true
	 rdns = false
	 default_realm = MYDOMAIN.LOCAL
	 default_ccache_name = KEYRING:persistent:%{uid}


3. username in windows.yml group_vars must be full username@domain
example: bmoriarty@MYDOMAIN.LOCAL

4. Simple test command:

        ansible -i hosts windows -m win_ping -vvvvv

