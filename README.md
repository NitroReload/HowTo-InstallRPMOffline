# HowTo-InstallRPMOffline
Installing of a rpm without going into dependency hell

## Prerequisite
- An Online environment (If not, look at cloud options)
## Objective
Install `httpd` in a offline environment 

## Guide

Go to a similar environment with internet capability (ie. ec2) to download required rpm and dependencies.

### In your Online Environment
Install `yum-plugin-downloadonly` , `yum utils` and `createrepo`
```python
yum install yum-plugin-downloadonly yum-utils createrepo
```
Create directories to hold your rpms, the installroot folder served as a blank slate to force a full download of all dependencies. 
```python
mkdir /var/tmp/httpd
mkdir /var/tmp/httpd-installroot
```
This will download all required dependencies to `var/tmp/httpd` 

```bash
yum install --downloadonly --installroot=/var/tmp/httpd-installroot --downloaddir=/var/tmp/httpd
```
Generate the metadata to rebuild RPMs folder into a yum repository
```bash
createrepo --database /var/tmp/httpd
```
SFTP or somehow transfer your rpm folder(`/var/tmp/httpd`) to your offline environment. Preferably the same location (`/var/tmp/httpd`)
### In your Offline Environment
Create a local repository
```bash
vi /etc/yum.repos.d/offline-httpd.repo
```
With the following content (Ensure your baseurl points to location of your rpm folder)
```bash
[offline-httpd]
name=myoffline-repo-or-something
baseurl=file:///var/tmp/httpd
enabled=0
gpgcheck=1
```

Installation
```bash
yum --disablerepo=\* --enablerepo=offline-nc install httpd --nogpgcheck
```

### Issues
 yum report conflicts with another rpm
```
rpm -qa | grep python27
# This cmd will allow you to find the exact package that is being conflicted
			
rm -e python27.xxxxxx.amzn.xxxx
# This cmd can be used to try uninstalling the rpm
			
rpm -ivh  python27.xxxxxx.rpm --force
# If all things failed, force install at your own risk
```

## Contributing


## License
[MIT](https://choosealicense.com/licenses/mit/)
