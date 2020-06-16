# Deploying <u>osTicket</u> for Issue Tracking

Ticketing systems are used to allow users to submit issues and requests. These systems help staff work effectively and efficiently. I'll setup osTicket on my Ubuntu server **(Ubuntu 18.04.4 LTS)**.

## Installing MySQL/MariaDB

In order to use osTicket, we'll need to install Apache, MySQL/MariaDB, and PHP. First we have to update the system packages with `sudo apt update && sudo apt upgrade` We can install MariaDB with `sudo apt install mariadb-server`

Using the command `sudo mysql_secure_installation` will help you secure your database. You'll be prompted with the following questions. Answer them with the following answers:

- Enter current password for root (enter for none): **press Enter**
- Set root password? [Y/n]: **Y**
- New password: **Enter password**
- Re-enter new password: **Repeat password**
- Remove anonymous users? [Y/n]: **Y**
- Disallow root login remotely? [Y/n]: **Y**
- Remove test database and access to it? [Y/n]:  **Y**
- Reload privilege tables now? [Y/n]:  **Y**

![](https://drive.google.com/uc?id=1ucInGYMR3fTiFALS9pumzaKKwRqat2Wg)

Login to MariaDB with the command `sudo mysql -u root -p`

![](https://drive.google.com/uc?id=1fn-F184G5Su43dxG3Q5tPpOZ936ASJzX)

We can create a database with:

```sql
CREATE DATABASE osticket_db;
GRANT ALL PRIVILEGES ON osticket_db.* TO your_username@localhost IDENTIFIED BY "yourPassword";
```

![](https://drive.google.com/uc?id=1oLTwotghmtx4pTzMQjV60cLjMhfLtny_)

## Installing Apache

Apache can be installed with the command: `sudo apt install apache2`

Enable apache with `sudo systemctl enable apache2` and check the status by using the command `sudo systemctl status apache2`

![](https://drive.google.com/uc?id=1L_4zFvQMpaP-ScKex1Fd5WO0ELbUpY3h)

## Installing PHP

Now we can install PHP. Use the command:

```bash
sudo apt-get install php php-{fpm,pear,imap,apcu,intl,cgi,common,mbstring,net-socket,gd,xml-util,mysql,bcmath}
```

Make sure PHP is installed by using the command `php -v`

## Finally, installing osTicket

First, make sure curl, wget, and unzip are installed

`sudo apt install curl wget unzip`

Download the latest version of osTicket from github with the command

```bash
curl -s https://api.github.com/repos/osTicket/osTicket/releases/latest \
  | grep browser_download_url \
  | grep "browser_download_url" \
  | cut -d '"' -f 4 \
  | wget -i -
```

Now we can extract our zip file `unzip osTicket-v1.14.2.zip -d osTicket`

We'll have a new folder called osTicket, which we'll move to the directory /var/www/

`sudo mv osTicket /var/www/`

We'll need to create a osTicket configuration file:

```bash
cd /var/www/osTicket/upload/include
sudo cp ost-sampleconfig.php ost-config.php
```

Change the ownership of the /var/www directory to **www-data**

`sudo chown -R www-data:www-data /var/www`

We'll create a **VirtualHost** configuration file for osTicket

**(you can use nano or vim as your editor)**

```bash
cd /etc/apache2/sites-available/
sudo vim osticket.conf
```

![](https://drive.google.com/uc?id=1hLqKEXOeOhegycwcmceYJXiSTqnys7Kg)

Enable the configuration file we just created and disable 000-default.conf

```bash
sudo a2ensite osticket.conf
sudo a2dissite 000-default.conf
sudo systemctl reload apache2
```

Go to your browser and type your computers ip address **(EX: 192.168.50.116)**

The webpage will look like this

![](https://drive.google.com/uc?id=1l9F0elZcvfFr-TREXBlxpQ-5w1a68xKo)

Fill out the following sections

![](https://drive.google.com/uc?id=1Tf42iEcTI4yZEy4K5QSD076JHA7PuWyG)

![](https://drive.google.com/uc?id=1F_Cwhl2loRCBMOFm9cc8RGiJJdTMCxAq)

After clicking install, it'll take a few minutes to set everything up

![](https://drive.google.com/uc?id=1SBXIoeKEVy0vknPxQl4lkmFD1wwiWl92)

You'll be greated by this page after the installation is complete. We need to change the permissions of the **ost-config.php** file and remove the **setup** directory.

```bash
sudo chmod 0644 /var/www/osTicket/upload/include/ost-config.php
sudo rm -rf /var/www/osTicket/upload/setup/
```

At the bottom of the page you'll see a few links. There is a link to the **staff control panel** (scp). This is what the login page looks like; attempt to login.

![](https://drive.google.com/uc?id=1b2_NTz8OvUHOYiY29gorQxC76l_yMdLz)

![](https://drive.google.com/uc?id=1F-ZI4ZFrJnEBnWtgiO466dMrW7OKwMvJ)

osTicket has been successfully installed! There are additional settings that can be configured, but this installation is a good place to get started with osTicket.
