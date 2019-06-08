# Catalog Application Served on Apache2

## Addresses
* IP: 99.79.100.96 -- only the domain will work with authorization.
* SSH: 2200
* Domain: https://kylehansen.dev

## Summary

#### using Ubuntu 18.04 LTS

### Configure Firewall
We need to configure the firewall to secure the server right away to make sure we get the settings correct.

* sudo ufw status
* sudo ufw default deny incoming
* sudo ufw default allow outgoing
* sudo ufw allow www - port 80
* sudo ufw allow 2200/tcp - this is for ssh
  --  for this you will also need to navigate and adjust /etc/ssh/sshd_config file and point the port to 2200 / then restart sudo service ssh restart
* sudo ufw allow https - port 443 required for .dev domain.
* sudo ufw allow 123/tcp for NFT opened this because it was in the Project Specification

* sudo ufw enable

---

### Add Users

next setup of users student, and grader

* sudo adduser student
* sudo adduser grader

Navigating to /etc/sudoers.d/ we can add configs, named after each user and follow the default files content with the user name adjusted to allow each user to have sudo access.

Once that is completed add under each users directory (/home/student/) a .ssh directory and a authorized_keys file within that directory.

permissions need to be set on the directory and the file.

* I used chmod 755 on the .ssh, and 644 on the authorized_keys

* create a ssh keys and copy the public key to the authorized_keys file.

* validate that the connection works, for each user.

* last step is to delete the default user file from /etc/sudoers.d/ to disable sudo access for ubuntu.

---

### Python Environment

With users working working you can setup the python virtual environemnt and install the dependencies listed for the site below, I set mine up under the student user /environment_directory/environment/

---

### Install Apache & ibapache2-mod-wsgi-py3

After you can see the default apache web page you can clone the web project below to the default location or create a new domain, I used: /var/www/html/catalog/ 

Within the root directory I created a new file ws.wsgi and added some simple python code to have it point at the applications app. I also needs the location of the python environment.

```
activate_this = '/home/student/environment_directory/environment/bin/activate_this.py'
with open(activate_this) as file_:
    exec(file_.read(), dict(__file__=activate_this))
```

now navigate to /etc/apache2/000-default.conf and update it to point the alias and in my case create another VirualHost and point it to port 443, with all the information from the 80 one.

This will be needed when the ssl certs are added to the server.

---

### Add SSL

I needed to add this because I picked a domain name that appears to require it see [.dev](https://medium.engineering/use-a-dev-domain-not-anymore-95219778e6fd).

Luckly this is very easy to add thanks to [Certbot](https://certbot.eff.org/lets-encrypt/ubuntubionic-apache)


I followed the instuctions [here](https://certbot.eff.org/lets-encrypt/ubuntubionic-apache) and your up and running in no time.


### Postgres

Lastly the site will not work without a database behind it. so I installed sudo apt install postgresql postgresql-contrib

When you sign in with the postgres user you can create a new user with the same name as the ubuntu user in my case student.

update my configuration file for the site, and restart apache2.

---
## Website
* Source: https://github.com/polysplitter/catalog

### Dependencies

* Flask==1.0.3
* Flask-SQLAlchemy==2.4.0
* Flask-WTF==0.14.2
* psycopg2==2.8.2
* SQLAlchemy==1.3.4
* oauth2client==4.1.3
---

## Third Party Resources
* [Certbot](https://certbot.eff.org/lets-encrypt/ubuntubionic-apache)
* [finger](http://manpages.ubuntu.com/manpages/bionic/man1/finger.1.html)
* [apache2](https://httpd.apache.org/)
* [ibapache2-mod-wsgi-py3](https://packages.ubuntu.com/bionic/libapache2-mod-wsgi-py3)
* [postgres](https://www.postgresql.org/)
