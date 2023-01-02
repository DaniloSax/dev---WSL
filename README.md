
# Preparar ambiente de desenvolvimento no WSL

Guia definitivo de como preparar ambiente de desenvolvimento no WSL.





## Introdução

Durante a experiência de desenvolvimento, me deparei com um problema que persistiu mesmo após todas as possíveis soluções:

![Logo](https://media.discordapp.net/attachments/1047887960718188635/1057723159530459177/image.png?width=960&height=145)

Conversando com colegas e o próprio CTO Anderson. Concluímos que o problema era meu ambiente de desenvolvimento local.
Por alguma razão o Windows não permite a escrita dos proxies doctrine.
Melhor solução até o momento, é instalar e configurar um ambiente de desenvolviemnto Linux (WSL)

## Pré-requisitos

- WSL instalado com Ubuntu >= 20.04

## Instalação




### Mysql

```bash
 sudo apt install mariadb-server
```
siga os passos do mysql_secure_installation

```bash
sudo mysql_secure_installation
```

    

Habilite all permissões ao user root pra conseguir consexões externas
```bash
 mysql -u root -p
 grant all privileges on *.* to root@localhost;
 grant all privileges on *.* to root@'%';
```

Observe se o mysql está ouvindo na porta 3306

```bash
 sudo apt install net-tools
 netstat -tlnp
```



### PHP 8.1

```bash
sudo apt install php8.1
php -v
```
Extensões:
```bash
sudo apt install php8.1-mysql php8.1-curl
```
### Apache2
```bash
sudo apt install apache2
```
### Composer

```bash
 sudo apt install php-cli unzip
```
```bash
 curl -sS https://getcomposer.org/installer -o /tmp/composer-setup.php
```
```bash
 HASH=`curl -sS https://composer.github.io/installer.sig`
```
```bash
 php -r "if (hash_file('SHA384', '/tmp/composer-setup.php') === '$HASH') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
```
```bash
 sudo php /tmp/composer-setup.php --install-dir=/usr/local/bin --filename=composer
```

Inicie os serviços
```bash
sudo service mysql start
sudo service apache2 start
```
## Virtual Host (Opcional)

- habilitar 2 modulos:

```bash 
sudo a2enmod headers
sudo a2enmod rewrite
``` 

- criar arquivo eshop.conf ou o nome de sua preferência em /etc/apache2/sites-available/<eshop.conf>



- add o conteúdo:
 ```bash
 <VirtualHost api.eshop.local:80>
        ServerName api.eshop.local
        DocumentRoot /var/www/html/<repo>/api-laravel/public
        <Directory /var/www/html/<repo>/api-laravel/public>
                AllowOverride all
                Require all granted
        </Directory>
</VirtualHost>
 ```

 - Adicionar novas linhas no arquivo /etc/hosts

 
 ```bash
 ...

 127.0.0.1 api.eshop.local
 ::1   api.eshop.local
 ``` 
- Adicionar as mesmas linhas no arquivo hosts do windows (C:\Windows\System32\drivers\etc\hosts)
 
- Para finalizar
```bash
a2ensite eshop
sudo service apache2 restart
```
## FAQ

#### Erro ao executar composer install

As vezes o apache2 sobrescreve a versão do php8.1 com php7.*

solução é desabilitar o 7.* e hablitar o 8.1: 

```bash
sudo a2dismod php7.2
sudo a2enmod php8.1 
sudo update-alternatives --set php /usr/bin/php8.1
sudo update-alternatives --set phar /usr/bin/phar8.1
sudo update-alternatives --set phar.phar /usr/bin/phar.phar8.1

```

#### Erro ao instalar php8.1 

```bash 
E: Couldn't find any package by glob 'php8.1' 
```  

Solução: 

Add repositório no source.list
```bash
 sudo apt update
 sudo apt install software-properties-common
 sudo add-apt-repository ppa:ondrej/php
 sudo apt update
 sudo apt install php8.1
```


## Usado por

- [@EmutuaDigital](https://emutuadigital.com/)

