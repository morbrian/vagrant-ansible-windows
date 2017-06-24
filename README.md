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

