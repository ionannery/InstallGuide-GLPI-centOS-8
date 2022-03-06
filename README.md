# Instalação e configurações do servidor Apache no CentOS 8 e instalação do GLPI




- Instalar aplicativos úteis: 

```
dnf -y install bsdtar bzip2 curl wget tar zip nano sudo
```


- Editar o SELinux para modo permissivo: 

```
nano /etc/sysconfig/selinux
```

  SELINUX=permissive

## Configurar o Firewall:

**1. Liberar a porta TCP/80:** 

```
firewall-cmd --zone=public --permanent --add-service=http
```

**2. Liberar a porta TCP/443:** 

```
firewall-cmd --zone=public --permanent --add-service=https
```

**3. Reiniciar o servidor:** 

`reboot`

**4. Validar as portas liberadas no firewall:** 

```
firewall-cmd --list-services
```

**5. Validar a configuração do SELinux:**

`getenforce`



## Instalação e configuração do Maria DB


**1. Instalar o Mariadb CentOS 8** 

```
dnf module install mariadb
```

**2. Inicialiar e colocar o serviço automático:** 

```
systemctl enable --now mariadb
```

**3. Criar a base de dados:** 

```
mysql -u root -e "create database glpi character set utf8";
```

**Criar o usuário do GLPI:** 

```
mysql -u root -e "create user 'glpi'@'localhost' identified by '123456'";
```

**4. Conceder privilégios ao usuário do GLPI:** 

```
mysql -u root -e "grant all privileges on glpi.* to 'glpi'@'localhost' with grant option";
```

**5. Carregar a tabela de fuso horário:** 

```
mysql_tzinfo_to_sql /usr/share/zoneinfo | mysql -p -u root mysql
```

**6. Conectar no Mariadb:** 

```
mysql -u root
```

**7. Conceder privilégios ao usuário do GLPI na tabela de time zone:** 

```
GRANT SELECT ON `mysql`.`time_zone_name` TO 'glpi'@'localhost';
```

**8. Atualizar privilégios:** 

```
FLUSH PRIVILEGES;
```

- Sair: quit


## Instalar o PHP:

**1. Instalar o repositório remi:**

```
dnf -y install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
```

**2. Habilitar o repositório:** 

```
dnf module enable php:remi-7.3
```

**3. Instalar extensões:**

```
dnf -y install php \
    php-curl \
    php-fileinfo \
    php-gd \
    php-json \
    php-mbstring \
    php-mysqli \
    php-session \
    php-zlib \
    php-simplexml \
    php-xml \
    php-cli \
    php-domxml \
    php-imap \
    php-ldap \
    php-openssl \
    php-xmlrpc \
    php-pecl-apcu \
    php-intl \
    php-zip \
    php-sodium
```

**4. Instalar a extensão php-cas:** 

```
dnf --enablerepo=remi install php-pear-CAS
```

**5. Instalar o apache:** 

```
dnf -y install  httpd
```

**6. Criar o arquivo de configuração do GLPI:**  

```
nano /etc/httpd/conf.d/glpi.conf
```

**7. Adicionar o conteúdo do arquivo de configuração do GLPI:**

```
<Directory /var/www/html/glpi>
    AllowOverride All
</Directory>
```

**8. Inicializar o apache:** 

```
systemctl  enable --now  httpd
```

**9. Verificar o status do Apache:** 

```
systemctl status httpd.service
```


## Instalar o GLPI:

**1. Baixar o GLPI:** 

```
wget https://github.com/glpi-project/glpi/releases/download/9.5.6/glpi-9.5.6.tgz
```

**2. Descompactar o instalador:** 

```
tar xvf  glpi-9.5.6.tgz -C /var/www/html
```

**3. Alterar o dono do diretório:** 

```
chown  apache. -Rf /var/www/html/glpi/
```

**4. Entrar no diretório:** 

```
cd /var/www/html/glpi
```

**5. Validar os requisitos:** 

```
sudo -u apache php bin/console glpi:system:check_requirements
```

**6. Instalar o GLPI:** 

```
sudo -u apache php bin/console db:install -u glpi -d glpi -p 123456 -L pt_BR -vvv
```

**7. Acessar o GLPI:** http://IP-DO-GLPI/glpi

- Usuário: glpi  
    
- Senha: glpi
