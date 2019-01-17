# OpenShift environment with Windows and Linux nodes

This current is tested on VMware, with two machines, one running OpenShift 3.11 on RHEL 7.5, and one running OpenShift with Windows.
An OpenShift entitlement is required.

The Windows node is Windows Server Core 2019/Windows Datacenter Edition 2019.
The Windows node requires it to be enabled for Ansible.
The `bin/winansible.ps1`script sets up the Windows node for Ansible.

## Azure
The openshift-windows repository code for 3.11 now supports Microsoft Azure Cloud. A full ARM template is now included and has been tested. 

## How to Use

### Repos for Openshift Windows

Supported: https://github.com/openshift/openshift-windows  
Upstream: https://github.com/glennswest/openshift-windows

### Requirements
1. Linux node with host name set, and static IP, and a proper search domain
2. Windows node with a hostname set, and DHCP that returns same IP all the time, matching hostname. (Windows 2019 Datacenter Edition)
3. The Windows node must have the correct host name, make sure you rename it.

### Overview
1. Install two nodes, one with RHEL 7.5 and one with Windows 1803.
2. Setup DNS for both nodes, and search domain so the hosts can be found by both there short name, and there fully qualified name.
3. Make sure the Windows node can use DHCP to find its IP address.
4. Make sure the Mac address is unique for the Windows node in the first 5 bytes.
5. Login to root, and install git
6. git clone repo
7. cd repo (Either hybrid or openshift-windows)
8. Run `allinone.sh` script

> _Important Note_: The Windows Node must run on a physical box, or an environment that supports nested virtualization, with passthru configured on the VM. 

```shell
./allinone.sh LinuxHostName WindowsHostName InternalDomain OpenShiftPublicURL AppPublicURL UserName Password rhnusername rhnpassword
```

Arguments Examples:

Linux Host Name - node01 or openshift or linuxnode
Windows Host Name: winnode01 or windows 
Internal Domain: ncc9.com 
Openshift Public URL: openshift.ncc9.com 
App Public URL: example: app.openshift.ncc9.com
Username:  example: openshift 
Password:  SuperSecret 
rhnusername: A Red Hat Network Username - For OpenShift and RHEL Subscription 
rhnpassword: A Red Hat Network Password 


9. `cd ..`
10. Prepare Windows Machine
    1. RDP to Windows console (or use vmware console) 
    2. From Command Prompt: (To enable Ansible) be sure to specify the version of OpenShift to be installed. The command below assumes 3.11 specify a different version if required
        1. type: `powershell`
        2. type: `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12`
        3. `Invoke-WebRequest -Uri https://raw.githubusercontent.com/openshift/openshift-windows/master/3.11/bin/winansible.ps1 -OutFile "winansible.ps1" -UseDefaultCredentials`
        4. `.\winansible.ps1`
        5. `Rename-Computer -NewName "winnode01" -Restart -Force`
        6. Disconnect from Windows.
11. Copy the `group_vars/windows.example` to `group_vars/windows`
12. Add a username and password to `group_vars/windows`
13. Run `ansible-playbook windows.yml`

## Known Issues / Changes
1. Azure testing is current in process. Azure will require a further update to function.  
2. The 3.11 branch requires 2 additional arguments, `rhnusername` and `rhnpassword`.
3. Nested Virt is no longer required.
