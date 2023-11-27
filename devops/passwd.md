```bash
passwd
adduser sergey 

sudo usermod -aG www-data $USER
sudo chown -R www-data:www-data /var/www
sudo chmod -R 644 /var/www
sudo find /var/www -type d -exec chmod 755 {} \;


sudo usermod -aG www-data $USER
sudo chown -R www-data:www-data /var/www
sudo chmod -R 770 /var/www


/etc/sudoers
%sudo ALL=(ALL) NOPASSWD: ALL
```