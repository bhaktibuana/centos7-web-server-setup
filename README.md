# CentOS 7 Web Server Setup

## Description

This repository is documentation on how to setup a web server on CentOS 7 operating system. Using Nginx web server, Microsoft SQL Server database and NodeJs environtment project such as express, react, vue, angular and etc.

## Install Everything Needed

Before start the installation setup, you need to turn into root account by riunnging

```linux
$ sudo su
```

And update your system by running

```linux
$ yum update
```

<details>
<summary><b>Install firewall</b></summary>

<p>
First, you need to install firewall with this comamnd

```linux
$ yum install firewalld
```

After install firewall, you can enable the service and reboot your server to keep in mind that firewalld will cause the service to start up at boot

```linux
$ systemctl enable firewalld
$ reboot
```

After reboot the system we can verify that the service is running and reachable by typing:

```linux
$ firewall-cmd --state
```

and the output will be like this

```linux
running
```

Enable http and https services by running

```linux
$ firewall-cmd --permanent --zone=public --add-service=http
$ firewall-cmd --permanent --zone=public --add-service=https
```

And enable port 80 for http and 443 for https and you can also enable other ports as you want

```linux
$ firewall-cmd --permanent --zone=public --add-port=80/tcp
$ firewall-cmd --permanent --zone=public --add-port=443/tcp
$ firewall-cmd --permanent --zone=public --add-port=3000/tcp
```

Once everything is set, you can check the list that you have activated

```linux
$ firewall-cmd --permanent --zone=public --list-all
```

</p>
</details>

<details>
<summary><b>Install NodeJs</b></summary>

<p>
Before install NodeJs, install nvm with following command

```linux
$ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash
```

Close and re-opent the terminal and check nvm version to verify that nvm is installed successfully

```linux
$ nvm --version
```

After that you can install NodeJs with 3 options. First, you can install the lates version of NodeJs by typing:

```linux
$ nvm install node
```

or you can install the lts version:

```linux
$ nvm install --lts
```

or you can install the specific version (eg. v16.16.0):

```linux
$ nvm install 16.16.0
```

After installation success, check node and npm version to make sure that NodeJs is installed successfully

```linux
$ node --version
$ npm --version
```

</p>
</details>

<details>
<summary><b>Install yarn</b></summary>

<p>
Before install yarn, you need to import yarn repository with the following commands:

```linux
$ curl --silent --location https://dl.yarnpkg.com/rpm/yarn.repo | sudo tee /etc/yum.repos.d/yarn.repo
$ rpm --import https://dl.yarnpkg.com/rpm/pubkey.gpg
```

Once the repository is added, you can install yarn, by running:

```linux
$ yum install yarn
```

Verify the installation by checking the yarn version number:

```linux
$ yarn --version
```

</p>
</details>

<details>
<summary><b>Install git</b></summary>

<p>
Install git with the following commands:

```linux
$ yum install git
```

Verify the installation by chekcing the git version number:

```linux
$ git --version
```

Setting up your git by using the git config command to provide the name and email address that you would like to have embedded into your commits:

```linux
$ git config --global user.name "Your Name"
$ git config --global user.email "you@example.com"
```

To confirm that these configurations were added successfully, we can see all of the configuration items that have been set by typing:

```linux
$ git config --list
```

</p>
</details>

<details>
<summary><b>Install pm2</b></summary>

<p>
Install pm2 with the following commands:

```linux
$ npm i -g pm2
```

Verify the installation by chekcing the pm2 list:

```linux
$ pm2 list
```

</p>
</details>

<details>
<summary><b>Install SQL Server 2019</b></summary>

<p>
Before start the installation, make sure that your memory at least more than 2GB (not 2GB but 3GB or more). You can add the repository to your CentOS 7 by running the following command:

```linux
$ curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2019.repo
```

Update your system cache:

```linux
$ yum makecache
```

Then install SQL server 2019:

```linux
$ yum install -y mssql-server
```

After the installation, get info about the installed package

```linux
$ rpm -qi mssql-server
```

And the output will be like this:

```linux
Name        : mssql-server
Version     : 15.0.4223.1
Release     : 2
Architecture: x86_64
Install Date: Tue May 17 08:22:16 2022
Group       : Unspecified
Size        : 1297034956
License     : Commercial
Signature   : RSA/SHA256, Mon Apr 18 20:46:17 2022, Key ID eb3e94adbe1229cf
Source RPM  : mssql-server-15.0.4223.1-2.src.rpm
Build Date  : Mon Apr 18 20:05:17 2022
Build Host  : 17a94b24c000000.qzwxqe3wa2kubparrevzc0ivhc.xx.internal.cloudapp.net
...
```

After the package installation finishes, run mssql-conf setup and follow the prompts to set the sa (super admin) password and choose your edition

```linux
$ sudo /opt/mssql/bin/mssql-conf setup
```

And the output will be like this:

```linux
Choose an edition of SQL Server:
  1) Evaluation (free, no production use rights, 180-day limit)
  2) Developer (free, no production use rights)
  3) Express (free)
  4) Web (PAID)
  5) Standard (PAID)
  6) Enterprise (PAID)
  7) Enterprise Core (PAID)
  8) I bought a license through a retail sales channel and have a product key to enter.
```

For example we chose Developer Edition (number 2). And then type Yes and enter your sa password.

Then Install mssql-tools with the unixODBC developer package. Add the repository containing required packages using the next command:

```linux
$ curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/7/prod.repo
```

With the repository added, we can proceed to install the tools

```linux
$ yum -y install mssql-tools unixODBC-devel
```

Accept the license terms during installation

After the installation success, you are ready to start and enable the sql server

```linux
$ systemctl start mssql-server
$ systemctl enable mssql-server
```

Add `/opt/mssql/bin/` to your $PATH variable:

```linux
$ echo 'export PATH=$PATH:/opt/mssql/bin:/opt/mssql-tools/bin' | sudo tee /etc/profile.d/mssql.sh
```

Source the file to start using MS SQL executable binaries in your current shell session:

```linux
$ source /etc/profile.d/mssql.sh
```

Allow SQL Server ports for remote hosts to connect:

```linux
$ firewall-cmd --permanent --add-port=1433/tcp
$ firewall-cmd --reload
```

Finally, connect to the SQL Server and verify it is working

```linux
$ sqlcmd -S localhost -U SA
```

At the first line type `select name from sysusers;` and 2nd line type `go`. Congrats if you see the list of database. You can try to test remote connection of the databse using SSMS, DBEaver or etc

</p>
</details>

<details>
<summary><b>Install Nginx</b></summary>

<p>
Before installing nginx, you need to add the EPEL software repository:

```linux
$ yum install epel-release
```

Install Nginx

```linux
$ yum install nginx
```

Start and enable nginx service:

```linux
$ systemctl start nginx
$ systemctl enable nginx
```

Check nginx service status after start by running this following command:

```linux
$ systemctl status nginx
```

And the output will be like this:

```linux
● nginx.service - The nginx HTTP and reverse proxy server
   Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; vendor preset: disabled)
   Active: active (running) since Mon 2022-01-24 20:14:24 UTC; 5s ago
  Process: 1898 ExecStart=/usr/sbin/nginx (code=exited, status=0/SUCCESS)
  Process: 1896 ExecStartPre=/usr/sbin/nginx -t (code=exited, status=0/SUCCESS)
  Process: 1895 ExecStartPre=/usr/bin/rm -f /run/nginx.pid (code=exited, status=0/SUCCESS)
 Main PID: 1900 (nginx)
   CGroup: /system.slice/nginx.service
           ├─1900 nginx: master process /usr/sbin/nginx
           └─1901 nginx: worker process
```

If you haven't allow the firewall, allow the firewall first:

```linux
$ firewall-cmd --permanent --zone=public --add-service=http
$ firewall-cmd --permanent --zone=public --add-service=https
$ firewall-cmd --reload
```

Reboot the system

```linux
$ sudo reboot
```

After reboot the system, access your public ip or domain name on your browser

```linux
http://server_domain_name_or_public_ip/
```

The output on your browser will be like this:
![NGINX CENTOS 7](https://assets.digitalocean.com/articles/centos/nginx/centos-7-nginx.png)

</p>
</details>

## Frontend Deployment

## Backend Deployment

## Database Remote
