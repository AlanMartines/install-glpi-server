Instalação do GLPI Server no Debian/Ubuntu
==========================================

Introdução
----------

>Este guia fornece um passo a passo para instalar o **GLPI (Gestionnaire Libre de Parc Informatique)**, um sistema open-source de gerenciamento de ativos e help desk, em servidores **Debian** e **Ubuntu**. O tutorial inclui a instalação de dependências, banco de dados MySQL, configuração do PHP e ajustes de segurança.

## Instalando Dependencias Necessárias

```sh
sudo apt -y install vim git make cmake gcc build-essential graphicsmagick libgraphicsmagick1-dev \
software-properties-common ca-certificates lsb-release apt-transport-https curl libapache2-mod-perl2 \
libapache-dbi-perl libapache-db-perl bzip2 curl mycli wget ntp libarchive-tools;
```

### Ubuntu
```sh
sudo apt update;
sudo apt -y install software-properties-common;
sudo apt update && sudo apt -y full-upgrade;
sudo add-apt-repository ppa:ondrej/php;
curl -sSL https://packages.sury.org/php/README.txt | sudo bash -x;
sudo apt update;
```

### Debian
```sh
sudo apt update;
sudo apt install lsb-release apt-transport-https ca-certificates;
sudo wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg;
echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/php.list;
sudo apt update;
```

### Instalar PHP 7.4
```sh
sudo apt -y install apache2 libapache2-mod-fcgid php7.4 libapache2-mod-php php-zip php-pclzip php-gd php-mysql php-soap php-curl php-json php-pear \
php-xml php-mbstring perl libxml-simple-perl libcompress-zlib-perl libdbi-perl libdbd-mysql-perl libnet-ip-perl \
libsoap-lite-perl libio-compress-perl libapache2-mod-perl2-dev libarchive-zip-perl libmojolicious-perl \
libplack-perl libswitch-perl php7.4-curl php7.4-gd php7.4-mbstring php7.4-xml php7.4-bcmath php7.4-bz2 \
php7.4-intl php-fpm php7.4-fpm php-cli php-xmlrpc php7.4-common php7.4-mysql php-imagick php7.4-dev php7.4-imap \
php7.4-opcache php7.4-soap php7.4-zip php7.4-intl php-ssh2 php7.4-oauth php7.4-mcrypt libapache2-mod-php7.4 \
php7.4-apcu php7.4-ldap php7.4-snmp php7.4-pgsql;
```

### Instalar PHP 8.3
```sh
sudo apt -y install apache2 libapache2-mod-fcgid php8.3 libapache2-mod-php php-zip php-pclzip php-gd php-mysql php-soap php-curl php-json php-pear \
php-xml php-mbstring perl libxml-simple-perl libcompress-zlib-perl libdbi-perl libdbd-mysql-perl libnet-ip-perl \
libsoap-lite-perl libio-compress-perl libapache2-mod-perl2-dev libarchive-zip-perl libmojolicious-perl \
libplack-perl libswitch-perl php8.3-curl php8.3-gd php8.3-mbstring php8.3-xml php8.3-bcmath php8.3-bz2 \
php8.3-intl php-fpm php8.3-fpm php-cli php-xmlrpc php8.3-common php8.3-mysql php-imagick php8.3-dev php8.3-imap \
php8.3-opcache php8.3-soap php8.3-zip php8.3-intl php-ssh2 php8.3-oauth php8.3-mcrypt libapache2-mod-php8.3 \
php8.3-apcu php8.3-ldap php8.3-snmp php8.3-pgsql;
```

```sh
sudo a2enmod proxy_fcgi setenvif rewrite;
sudo systemctl restart apache2;
```

### Instalar MySQL
```sh
sudo apt update;
sudo apt upgrade -y;
wget https://dev.mysql.com/get/mysql-apt-config_$(wget -qO- https://dev.mysql.com/downloads/repo/apt/ | grep -oP 'mysql-apt-config_\K[0-9]+.[0-9]+.[0-9]+-[0-9]+(?=_all.deb)' | head -1)_all.deb;
sudo apt install ./mysql-apt-config_*.deb -y;
sudo dpkg-reconfigure mysql-apt-config;
sudo apt update;
sudo apt install -y mysql-server;
```

### Instalação segura do Mysql

```sh
mysql_secure_installation
```

![image](https://user-images.githubusercontent.com/10979090/208107935-70eadcf0-aa37-47ad-87a7-d43bee8a39d1.png)

### Criando o banco de dados glpi10 no MySQL
```sh
mysql -u root -p

CREATE DATABASE glpi10 CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;

CREATE USER 'glpi10'@'localhost' IDENTIFIED BY 'glpi10@135';

GRANT ALL PRIVILEGES ON `glpi10`.* TO 'glpi10'@'localhost';

FLUSH PRIVILEGES;

QUIT;
```
![image](https://user-images.githubusercontent.com/10979090/208531417-a62e7a78-8426-4b8d-bda1-4fddd92034d7.png)


## Ajustes no php.ini

### PHP 7.4
```sh
# /etc/php/7.4/apache2/php.ini
sed -i 's/^\s*short_open_tag\s*=.*/short_open_tag = On/' /etc/php/7.4/apache2/php.ini;
sed -i 's/^\s*post_max_size\s*=.*/post_max_size = 1024M/' /etc/php/7.4/apache2/php.ini;
sed -i 's/^\s*upload_max_filesize\s*=.*/upload_max_filesize = 256M/' /etc/php/7.4/apache2/php.ini;
sed -i 's/^\s*max_execution_time\s*=.*/max_execution_time = 600/' /etc/php/7.4/apache2/php.ini;
sed -i 's/^\s*max_input_time\s*=.*/max_input_time = 600/' /etc/php/7.4/apache2/php.ini;
sed -i 's/^\s*session.cookie_httponly\s*=.*/session.cookie_httponly = On/' /etc/php/7.4/apache2/php.ini

# /etc/php/7.4/cli/php.ini
sed -i 's/^\s*short_open_tag\s*=.*/short_open_tag = On/' /etc/php/7.4/cli/php.ini;
sed -i 's/^\s*post_max_size\s*=.*/post_max_size = 1024M/' /etc/php/7.4/cli/php.ini;
sed -i 's/^\s*upload_max_filesize\s*=.*/upload_max_filesize = 256M/' /etc/php/7.4/cli/php.ini;
sed -i 's/^\s*max_execution_time\s*=.*/max_execution_time = 300/' /etc/php/7.4/cli/php.ini;
sed -i 's/^\s*max_input_time\s*=.*/max_input_time = 300/' /etc/php/7.4/cli/php.ini;
sed -i 's/^\s*session.cookie_httponly\s*=.*/session.cookie_httponly = On/' /etc/php/7.4/cli/php.ini

# /etc/php/7.4/fpm/php.ini
sed -i 's/^\s*short_open_tag\s*=.*/short_open_tag = On/' /etc/php/7.4/fpm/php.ini;
sed -i 's/^\s*post_max_size\s*=.*/post_max_size = 1024M/' /etc/php/7.4/fpm/php.ini;
sed -i 's/^\s*upload_max_filesize\s*=.*/upload_max_filesize = 256M/' /etc/php/7.4/fpm/php.ini;
sed -i 's/^\s*max_execution_time\s*=.*/max_execution_time = 300/' /etc/php/7.4/fpm/php.ini;
sed -i 's/^\s*max_input_time\s*=.*/max_input_time = 300/' /etc/php/7.4/fpm/php.ini;
sed -i 's/^\s*session.cookie_httponly\s*=.*/session.cookie_httponly = On/' /etc/php/7.4/fpm/php.ini
```

### PHP 8.3
```sh
# /etc/php/8.3/apache2/php.ini
sed -i 's/^\s*short_open_tag\s*=.*/short_open_tag = On/' /etc/php/8.3/apache2/php.ini;
sed -i 's/^\s*post_max_size\s*=.*/post_max_size = 1024M/' /etc/php/8.3/apache2/php.ini;
sed -i 's/^\s*upload_max_filesize\s*=.*/upload_max_filesize = 256M/' /etc/php/8.3/apache2/php.ini;
sed -i 's/^\s*max_execution_time\s*=.*/max_execution_time = 300/' /etc/php/8.3/apache2/php.ini;
sed -i 's/^\s*max_input_time\s*=.*/max_input_time = 300/' /etc/php/8.3/apache2/php.ini;
sed -i 's/^\s*session.cookie_httponly\s*=.*/session.cookie_httponly = On/' /etc/php/8.3/apache2/php.ini

# /etc/php/8.3/cli/php.ini
sed -i 's/^\s*short_open_tag\s*=.*/short_open_tag = On/' /etc/php/8.3/cli/php.ini;
sed -i 's/^\s*post_max_size\s*=.*/post_max_size = 1024M/' /etc/php/8.3/cli/php.ini;
sed -i 's/^\s*upload_max_filesize\s*=.*/upload_max_filesize = 256M/' /etc/php/8.3/cli/php.ini;
sed -i 's/^\s*max_execution_time\s*=.*/max_execution_time = 300/' /etc/php/8.3/cli/php.ini;
sed -i 's/^\s*max_input_time\s*=.*/max_input_time = 300/' /etc/php/8.3/cli/php.ini;
sed -i 's/^\s*session.cookie_httponly\s*=.*/session.cookie_httponly = On/' /etc/php/8.3/cli/php.ini

# /etc/php/8.3/fpm/php.ini
sed -i 's/^\s*short_open_tag\s*=.*/short_open_tag = On/' /etc/php/8.3/fpm/php.ini;
sed -i 's/^\s*post_max_size\s*=.*/post_max_size = 1024M/' /etc/php/8.3/fpm/php.ini;
sed -i 's/^\s*upload_max_filesize\s*=.*/upload_max_filesize = 256M/' /etc/php/8.3/fpm/php.ini;
sed -i 's/^\s*max_execution_time\s*=.*/max_execution_time = 300/' /etc/php/8.3/fpm/php.ini;
sed -i 's/^\s*max_input_time\s*=.*/max_input_time = 300/' /etc/php/8.3/fpm/php.ini;
sed -i 's/^\s*session.cookie_httponly\s*=.*/session.cookie_httponly = On/' /etc/php/8.3/fpm/php.ini
```

```sh
systemctl restart apache2;
systemctl restart php7.4-fpm;
systemctl restart php8.3-fpm;
```

### Baixar e instalar GLPI
```sh
cd /tmp
wget https://github.com/glpi-project/glpi/releases/download/10.0.18/glpi-10.0.18.tgz
tar -zxvf glpi-10.0.18.tgz
mv glpi /var/www/html
```

### Ajustando dono da pasta glpi
```sh
sudo chown www-data:www-data /var/www/html/glpi/* -Rf
sudo find /var/www/html/glpi -type d -exec chmod 755 {} \;
sudo find /var/www/html/glpi -type f -exec chmod 644 {} \;
systemctl restart apache2;
systemctl enable apache2;
```

### Instalação do GLPI via web

```sh
http://<seu_ip>/glpi/install/install.php
```

### Instalação do GLPI via console

> A instalação via console é extremamente simples e rápida, caso o comando glpi-console não funcione, chame o php e aponte o diretório do console, seguindo o exemplo do segundo comando abaixo.

```sh
glpi-console glpi:database:install -Lpt_BR -H'localhost' -d'nome do banco de dados' -u'nome do usuário' -p'senha' --no-telemetry --force -n
php /var/www/html/glpi/bin/console glpi:database:install -Lpt_BR -H'localhost' -d'nome do banco de dados' -u'nome do usuário' -p'senha' --no-telemetry --force –n
```

> Em caso de erro, cheque as dependências com o comando abaixo para resolver e prosseguir.

```sh
glpi-console glpi:system:check_requirements
php /var/www/html/glpi/bin/console glpi:system:check_requirements
```
![image](https://github.com/user-attachments/assets/b5fe5005-5716-40d1-9856-ce6616e99b88)

>Pronto, seu GLPI foi instalado corretamente e você já pode começar a utilizá-lo. Acesse via web pelo IP ou DNS que configurou e sucesso!

## Configuração segura do webroot no GLPI - a "famosa" pasta public

>Nas versões mais recentes do GLPI 10.0.7 em diante, existe uma recomendação de segurança para configurarmos o diretório raiz do sistema, levando em consideração uma pasta public para "garantir que arquivos não públicos não possam ser acessados".

### Crie ou edite o arquivo .htacess
```sh
sudo vim /var/www/html/glpi/.htaccess
```
```sh
sudo vim /var/www/html/glpi/public/.htaccess 
```
```sh
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule ^(.*)$ index.php [QSA,L]
</IfModule>
```

# Créditos

Este tutorial foi criado com base nos seguintes tutoriais a baixo:

https://www.arthurschaefer.com.br/2022/02/como-instalar-o-glpi-10-no-debian-11/

https://dgez.com.br/docs/instalacao-glpi-10-no-debian-12/

https://verdanadesk.com/como-instalar-glpi-10/

https://www.reddit.com/r/glpi/comments/13e04jl/1007_warning_web_server_root_directory/?tl=pt-br
