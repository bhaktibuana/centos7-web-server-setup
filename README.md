# CentOS 7 Web Server Setup

## Description

This repository is documentation on how to setup a web server on CentOS 7 operating system. Using Nginx web server, Microsoft SQL Server database and NodeJs environtment project such as Express, React, Vue, Angular and etc.

## Install Everything Needed

Before start the installation setup, you need to go `root directory` and turn into root account by running the following command:

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

There are three different ways to deploy forntend on Nginx CentOS 7. Deploying on default HTTP port (port 80), deploying on default HTTPS port (port 443) and deploying on custom available port (eg. port 3001). First, this documentation will show you how to deploy custom port as follows.

<details>
<summary><b>Deployment on custom port</b></summary>

<p>

In this case for example we will use `port 3001`.

1 | Go to the `root directory` and run the following command to get root access

```linux
$ sudo su
```

2 | Check used ports with this following command:

```linux
$ netstat -tunlp
```

The output will similar like this:

```linux
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 127.0.0.1:1434          0.0.0.0:*               LISTEN      3270/sqlservr
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      550/rpcbind
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      1862/nginx: master
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1150/sshd
tcp        0      0 127.0.0.1:1431          0.0.0.0:*               LISTEN      3270/sqlservr
tcp        0      0 0.0.0.0:1433            0.0.0.0:*               LISTEN      3270/sqlservr
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN      1097/master
tcp6       0      0 ::1:1434                :::*                    LISTEN      3270/sqlservr
tcp6       0      0 :::111                  :::*                    LISTEN      550/rpcbind
tcp6       0      0 :::80                   :::*                    LISTEN      1862/nginx: master
```

The ports on the list above are ports that already used, you can choose the available port for example `port 3001`

3 | Enable firewall on port that will used (eg. port 3001)

```linux
$ firewall-cmd --permanent --zone=public --add-port=3001/tcp
$ firewall-cmd --reload
```

After that check is the selected port already enabled with the following command:

```linux
$ firewall-cmd --permanent --zone=public --list-ports
```

4 | Open selected port

After we enable the firewall on port that we want to use, we need to open the tcp port become available:

```linux
$ semanage port -a -t http_port_t -p tcp 3001
```

Sometimes it will return error like this:

```linux
ValueError: Port tcp/3001 already defined
```

Dont worry, we can replace `-a` option with `-m` for modify as follows:

```linux
$ semanage port -m -t http_port_t -p tcp 3001
```

After that, check to verify port that we want is available:

```linux
$ semanage port -l | grep http_port_t
```

It will return similar like this:

```linux
http_port_t                    tcp      3001, 80, 81, 443, 488, 8008, 8009, 8443, 9000
```

5 | Create a forntend project folder which will be read by nginx

```linux
$ mkdir -p /var/www/project-name
```

note: `-p` flag is used to create nested directory and `project-name` is the name of the project folder

6 | Change the project folder permissions to make it accessible to everyone with the followng command:

```linux
$ chown -R $USER:$USER /var/www/project-name
$ chmod -R 755 /var/www
$ restorecon -R /var/www/project-name
```

7 | Create index.html as the main html file which will be read by nginx. Or you can copy or clone your ready-project for example using git. But in this chase we will try to create new html file

```linux
$ nano /var/www/project-name/index.html
```

Once you are directed to the text editor, paste the following html code as an example project

```html
<html>
  <head>
    <title>Whelcome to my app :)</title>
  </head>
  <body>
    <h1>Hello! This is an app with port 3001</h1>
  </body>
</html>
```

8 | Create `sites-available` and `sites-enable` folder a config folder which will be read by nginx (ignore this step if the folders already created)

```linux
$ mkdir /etc/nginx/sites-available
$ mkdir /etc/nginx/sites-enabled
```

9 | Open Nginx configuration file

```linux
$ nano /etc/nginx/nginx.conf
```

You will see the nginx configuration code. Inside the `html { ... }` block find the code that similar with:

```conf
server {
  listen        80;
  listen        [::]:80;
  server_name   _;
  root          /usr/share/nginx/html;

  # Load configuration files for the default server block.
  include /etc/nginx/default.d/*.conf;

  error_page 404 /404.html;
  location = /404.html {
  }

  error_page 500 502 503 504 /50x.html;
  location = /50x.html {
  }
}
```

That code is server config block code for default http port (port 80). Under that code (outside server { ... } block), add this following code

```conf
include /etc/nginx/sites-enabled/*.conf
```

And it will be looks like this:

```conf
server {
  listen        80;
  listen        [::]:80;
  server_name   _;
  root          /usr/share/nginx/html;

  # Load configuration files for the default server block.
  include /etc/nginx/default.d/*.conf;

  error_page 404 /404.html;
  location = /404.html {
  }

  error_page 500 502 503 504 /50x.html;
  location = /50x.html {
  }
}

include /etc/nginx/sites-enabled/*.conf
```

10 | Create new nginx config file at `sites-available` with file name as your project name

```linux
$ nano /etc/nginx/sites-available/project-name.conf
```

And paste this following code:

```conf
server {
  listen       3001;
  listen       [::]:3001;
  server_name  _;
  root         /var/www/project-name;

  location / {
    try_files $uri /index.html;
  }

  # Load configuration files for the default server block.
  include /etc/nginx/default.d/*.conf;

  error_page 404 /404.html;
  location = /404.html {
  }

  error_page 500 502 503 504 /50x.html;
  location = /50x.html {
  }
}
```

Maybe you will think that the code is similar to the nginx config code in step number 8. The difference are `port number` assign with selected port and `root directory` assign with your project directory that you have been created before and add new `location / { ... }` code block

11 | Finally the final step. Link the config file that have been just created on `sites-available` to `sites-enabled` with this following command:

```linux
$ ln -s /etc/nginx/sites-available/project-name.conf /etc/nginx/sites-enabled/project-name.conf
```

After that you should restart nginx service

```linux
$ systemctl restart nginx
```

note: sometimes at this step you will facing an error that nginx service cannot be restart. Try to check your setting from the step 1 untill step 10 again. If you are sure that it is in accordance with the instructions, then it is likely that what happen is that the port you have chosen cannot be used by the nginx service. Then try using another port and repeat the steps above starting from the first step.

If you can successfully restart nginx service, access `public ip with port` on your browser

```linux
http://server_public_ip:3001/
```

</p>
</details>

<details>
<summary><b>Deployment on default HTTP port</b></summary>

<p>

In this section we will use `default HTTP port (port 80)`.

1 | Go to the root directory and run the following command to get root access

```linux
$ sudo su
```

2 | Enable firewall on default HTTP port

```linux
$ firewall-cmd --permanent --zone=public --add-service=http
$ firewall-cmd --permanent --zone=public --add-port=80/tcp
$ firewall-cmd --reload
```

After that check is the default HTTP port already enabled with the following command:

```linux
$ firewall-cmd --permanent --zone=public --list-all
```

3 | Create a forntend project folder which will be read by nginx

```linux
$ mkdir -p /var/www/project-name
```

note: -p flag is used to create nested directory and project-name is the name of the project folder

4 | Change the project folder permissions to make it accessible to everyone with the followng command:

```linux
$ chown -R $USER:$USER /var/www/project-name
$ chmod -R 755 /var/www
$ restorecon -R /var/www/project-name
```

5 | Create index.html as the main html file which will be read by nginx. Or you can copy or clone your ready-project for example using git. But in this chase we will try to create new html file

```linux
$ nano /var/www/project-name/index.html
```

Once you are directed to the text editor, paste the following html code as an example project

```html
<html>
  <head>
    <title>Whelcome to my app :)</title>
  </head>
  <body>
    <h1>Hello! This is an app default HTTP port 80</h1>
  </body>
</html>
```

6 | Open Nginx configuration file

```linux
$ nano /etc/nginx/nginx.conf
```

You will see the nginx configuration code. Inside the html { ... } block find the code that similar with:

```conf
server {
  listen        80;
  listen        [::]:80;
  server_name   _;
  root          /usr/share/nginx/html;

  # Load configuration files for the default server block.
  include /etc/nginx/default.d/*.conf;

  error_page 404 /404.html;
  location = /404.html {
  }

  error_page 500 502 503 504 /50x.html;
  location = /50x.html {
  }
}
```

That code is server config block code for default http port (port 80). Change the root directory project with your project directory that you just have been created and add `location / { ... }` code block under the root directory. It should similar like this:

```conf
server {
  listen        80;
  listen        [::]:80;
  server_name   _;
  root          /var/www/project-name;

  location / {
    try_files $uri /index.html;
  }

  # Load configuration files for the default server block.
  include /etc/nginx/default.d/*.conf;

  error_page 404 /404.html;
  location = /404.html {
  }

  error_page 500 502 503 504 /50x.html;
  location = /50x.html {
  }
}
```

7 | Restart nginx service

```linux
$ systemctl restart nginx
```

If you can successfully restart nginx service, access server public ip with your browser

```linux
http://server_public_ip/
```

</p>
</details>

<details>
<summary><b>Deployment on default HTTPS port</b></summary>

<p>

COMING SOON 😫😋😁

</p>
</details>

## Backend Deployment

In this section we are going to deploy a backend with NodeJs environtment such as ExpressJs project using pm2. Before we start the deployment we need to setup the time zone on the server.

<details>
<summary><b>Server time zone setting</b></summary>

<p>

If you are already setting up your server time zone you can just skip this part.

1 | Go to the `root directory` and run the following command to get root access

```linux
$ sudo su
```

2 | Check server current time zone

```linux
$ timedatectl
```

The output will similar like this:

```linux
      Local time: Sat 2023-03-18 02:00:01 UTC
  Universal time: Sat 2023-03-18 02:00:01 UTC
        RTC time: Sat 2023-03-18 02:00:01
       Time zone: UTC (UTC, +0000)
     NTP enabled: yes
NTP synchronized: yes
 RTC in local TZ: no
      DST active: n/a
```

3 | Basicly the default server time zone is UTC. You need to change it with time zone that you want. For example in this case we are going to change it with Asia/Jakarta time zone (WIB). First, we need to check available time zone list as follows:

```linux
$ timedatectl list-timezones
```

4 | Change the time zone

```linux
$ timedatectl set-timezone Asia/Jakarta
```

5 | Check server time zone once again

```linux
$ timedatectl
```

The output will similar like this:

```linux
      Local time: Sat 2023-03-18 09:19:25 WIB
  Universal time: Sat 2023-03-18 02:19:25 UTC
        RTC time: Sat 2023-03-18 02:19:25
       Time zone: Asia/Jakarta (WIB, +0700)
     NTP enabled: yes
NTP synchronized: yes
 RTC in local TZ: no
      DST active: n/a
```

</p>
</details>

<details>
<summary><b>Deploy the backend project</b></summary>

<p>

In this part we will deploy an ExpressJs project using pm2 with `port 4000`

1 | Go to the `root directory` and run the following command to get root access

```linux
$ sudo su
```

2 | Check used ports with this following command:

```linux
$ netstat -tunlp
```

The output will similar like this:

```linux
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 127.0.0.1:1434          0.0.0.0:*               LISTEN      3270/sqlservr
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      550/rpcbind
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      1862/nginx: master
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1150/sshd
tcp        0      0 127.0.0.1:1431          0.0.0.0:*               LISTEN      3270/sqlservr
tcp        0      0 0.0.0.0:1433            0.0.0.0:*               LISTEN      3270/sqlservr
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN      1097/master
tcp6       0      0 ::1:1434                :::*                    LISTEN      3270/sqlservr
tcp6       0      0 :::111                  :::*                    LISTEN      550/rpcbind
tcp6       0      0 :::80                   :::*                    LISTEN      1862/nginx: master
```

The ports on the list above are ports that already used, you can choose the available port for example `port 4000`

3 | Enable firewall on port that will used (eg. port 4000)

```linux
$ firewall-cmd --permanent --zone=public --add-port=4000/tcp
$ firewall-cmd --reload
```

After that check is the selected port already enabled with the following command:

```linux
$ firewall-cmd --permanent --zone=public --list-ports
```

4 | Open selected port

After we enable the firewall on port that we want to use, we need to open the tcp port become available:

```linux
$ semanage port -a -t http_port_t -p tcp 4000
```

Sometimes it will return error like this:

```linux
ValueError: Port tcp/4000 already defined
```

Dont worry, we can replace `-a` option with `-m` for modify as follows:

```linux
$ semanage port -m -t http_port_t -p tcp 4000
```

After that, check to verify port that we want is available:

```linux
$ semanage port -l | grep http_port_t
```

It will return similar like this:

```linux
http_port_t                    tcp      4000, 80, 81, 443, 488, 8008, 8009, 8443, 9000
```

5 | Create a backend project folder or clone from your git remote repository which will be execute by pm2

```linux
$ mkdir -p /var/server/project-name
$ cd /var/server/project-name
$ git init
$ git checkout -b main
$ git remote add origin https://github_repo_url
$ git pull origin main
```

or

```linux
$ mkdir -p /var/server
$ cd /var/server
$ git clone https://github_repo_url
```

note: `-p` flag is used to create nested directory and `project-name` is the name of the project folder

6 | Change the project folder permissions to make it accessible to everyone with the followng command:

```linux
$ chown -R $USER:$USER /var/server/project-name
$ chmod -R 755 /var/server
$ restorecon -R /var/server/project-name
```

7 | Install the project

Go to the project directory

```linux
$ cd /var/server/project-name
```

Using npm:

```linux
$ npm install
```

Using yarn:

```linux
$ yarn
```

8 | Run the project locally to verify that it's working

In this step, we are going to run the project as development mode. And here is the script preview inside the `package.json` file

```json
"scripts": {
  "dev": "nodemon ./src/index.js",
  "start": "node ./src/index.js",
  "test": "echo \"Error: no test specified\" && exit 1"
},
```

Go to the project directory

```linux
$ cd /var/server/project-name
```

Let's try to run in development mode:

```linux
$ yarn dev
```

Once it's running, test your project end-point on your Postman

```
http://server_public_ip:4000/your_end_point
```

If it seems okay, stop the project by pressing CTRL + C on terminal and lets go to the next step

note: before you start the project locally, make sure you have alraedy setting up your environtment variables such as port and etc. And make sure the port that you use on your project is the same with port that you will use for the backend deployment (eg. port 4000 for this case).

9 | Run the project with pm2

Go to the server directory that you have been created

```linux
$ cd /var/server
```

After that run your project with pm2:

```linux
$ pm2 start ./project-name/src/index.js --name="type_name_here"
```

explanation:
• `start` is command to execute your project. It's not always use start, it based on your `package.json` file scripts (see 8th step) for example based package.json file on 8th step above you can use `pm2 start` or `pm2 dev` or etc.
• `./project-name/src/index.js` is path to target your main project file (in this case is index.js).
• `--name="type_name_here"` is the name that will shown on the pm2 running list. For example `--name="my-awesome-backend"`.

After you execute the pm2, check the pm2 list to verify that it is running by the following command:

```linux
$ pm2 list
```

And the output will be like this:

![alt text](https://github.com/bhaktibuana/centos7-web-server-setup/blob/main/assets/pm2-list.png?raw=true)

Make sure that the status is `Online`

10 | Check ports that used on your server to verify that your project is currently running

```linux
$ netstat -tunlp
```

The output will be similar like this:

```linux
tcp6       0      0 :::4000                 :::*                    LISTEN      18016/node /var/ser
```

If you see something like that, congrat! your backend deployment success!

Now you can test it on your Postman

```
http://server_public_ip:4000/your_end_point
```

If you want to stop your project you can run this following command:

```linux
$ pm2 stop selected_pm2_id
```

or

```linux
$ pm2 stop selected_pm2_name
```

If you want to delete the task, you can stop it first and do this:

```linux
$ pm2 delete selected_pm2_id
```

or

```linux
$ pm2 delete selected_pm2_name
```

</p>
</details>
