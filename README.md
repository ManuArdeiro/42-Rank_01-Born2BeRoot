# 42_born2beroot

*   Instalación paso a paso: https://www.youtube.com/watch?v=OQEdjt38ZJA

*   Se utilizará Debian.

    https://www.linuxadictos.com/debian-vs-ubuntu.html

    https://www.linuxadictos.com/ranking-de-las-mejores-distribuciones-gnulinux-de-2015.html

*   Qués es SELinux y AppArmor?

    https://es.wikipedia.org/wiki/SELinux
    
    https://es.wikipedia.org/wiki/AppArmor
    
*   Para crear las particiones se utiliza LVM. https://blog.inittab.org/administracion-sistemas/lvm-para-torpes-i/

    ![image](https://user-images.githubusercontent.com/102896906/168761466-6e486702-7ee0-47a7-8029-1b7a0b6bba52.png)
    
    Recordatorio sobre particiones: https://www.wikiversus.com/informatica/discos-duros/particiones-primarias-vs-extendidas-vs-logicas/
    
    Se utiliza GRUB para el arranque: https://es.wikipedia.org/wiki/GNU_GRUB
    
    Particiones de Linux: https://www.laboratoriolinux.es/index.php/-noticias-mundo-linux-/software/18656-diferencias-entre-ext2-ext3-y-ext4.html
    
    Particiones recomendadas: https://www.daniloaz.com/es/la-importancia-de-particionar-correctamente-un-disco-en-linux/
    
    La partición swap: 
    
    https://blog.desdelinux.net/que-es-el-swap-en-linux-y-como-utilizarlo/

    https://hipertextual.com/2015/09/swap-en-linux
    
*   Elegido apt para la instalación de paquetes: https://juncotic.com/apt-vs-apt-get-vs-aptitude-algunas-notas/

*   Diferencia su y sudo: https://blog.desdelinux.net/cual-es-la-diferencia-entre-sudo-y-su/
   
*   Para administrar permisos se utiliza sudo: https://www.linuxtotal.com.mx/index.php?cont=info_admon_014
    
        $ sudo hostnamectl set-hostname <new_hostname>
        $ hostnamectl status

        useradd -> crear un usuario.
        userdel -r : borra un usuario y sus archivos asociados.
        id -u : ver el user ID.
        users : shows a list of all currently logged in users.
        
        groupadd -> crear un grupo.
        groupdel -> deletes a group.
        groups : displays the groups of a user.
        getent group -> comprobar los usarios de un grupo
        adduser <username> <grupo> -> añadir username a grupo
        usermod : modificar el usuario -> usermod -aG sudo <username>
                -l para username
                -c para full name
                -g para grupos por group ID
        cat /etc/passwd | cut -d ":" -f 1 : displays a list of all users on the machine.
        cat /etc/passwd | awk -F '{print $1}' : same as above.

        gpasswd -a -> adds a user to a group.
        gpasswd -d -> removes a user from a group.
        id -g : shows a user’s main group ID.
            
    Configuración:
    
            -   sudo nano /etc/sudoers.d/<filename> donde filename puede ser sudoconfig, archivo que se crea
            -   sudo mkdir /var/log/sudo para guardar los logs
            -   En el archivo sudoconfig:
                Defaults        passwd_tries=3
                Defaults        badpass_message="<custom-error-message>"
                // Defaults        logfile="/var/log/sudo/<filename>" para guardar todo en un archivo
                Defaults        log_input,log_output
                Defaults        iolog_dir="/var/log/sudo"
                Defaults        requiretty
                Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
Cuando se establece requiretty, sudo debe ejecutarse desde una sesión de terminal registrada (a tty). Esto evita que sudo se use desde demonios u otros procesos separados como cronjobs o complementos de servidores web. También significa que no puede ejecutarlo directamente desde una llamada ssh sin configurar una sesión de terminal. Esto puede prevenir ciertos tipos de ataques de escalada. Por ejemplo, si tengo una forma de modificar el crontab para un usuario que tiene permisos de sudo NOPASSWD, podría usarlo para iniciar un trabajo como root. Con requiretty, no puedo hacer eso...
    
*   Creación de usuarios en unix: https://www.todavianose.com/crear-usuario-en-linux-y-asignarle-un-directorio/

*   Cambiando el hostname: https://es.wikipedia.org/wiki/Archivo_hosts

        - $ hostnamectl -> para comprobar el actual
        - $ hostnamectl set-hostname new_hostname -> cambia el hostname
        - $ sudo nano /etc/hosts -> cmabiar también el nonbre en este archivo y reiniciar:
                127.0.0.1       new_hostname

*   Instalar ssh: https://es.wikipedia.org/wiki/Secure_Shell

        -   $ sudo apt install openssh-server
        -   $ dpkg -l | grep ssh -> verificar la correcta instalación
        -   $ sudo systemctl status ssh
        -   $ sudo systemctl restart ssh
        -   $ sudo systemctl status ssh
        -   Configurar el VirtualBox: Network -> Advanced -> Port Forwarding -> Rule1 from 4242 to 4242
        -   $ sudo service sshd status
 
*   $ cat /etc/*release -> saber S.O.

Fuente: https://www.enmimaquinafunciona.com/pregunta/49986/como-puedo-reiniciar-el-servicio-ssh

    Configuración: 
    
        - $ sudo vim /etc/ssh/sshd_config
                -   Línea 13 cambia de "#Port 22" a "Port 4242" para utilizar solo el puerto 4242.
                -   Cambiar linea 32 de "#PermitRootLogin prohibit-password" a "PermitRootLogin no" para no permitir SSH login como root.
    
    Para comprobar ssh: 
    
        - $ sudo service ssh status
        - $ sudo grep Port /etc/ssh/sshd_config
 
 *  Instalar UFW:
 
        - $ sudo apt install ufw
        - $ sudo ufw enable -> se habilita
        - $ sudo ufw status numbered
        - $ sudo ufw allow 4242 -> para permitir conexiones entrantes utilizando el puerto 4242
        - $ sudo ufw allow 4241
        - $ sudo ufw status numbered
        - $ sudo ufw delete 4241
        - $ sudo ufw status numbered

*   Conectar a un servidor via SSH:

        - ssh <username>@<ip-address> -p 4242
        - logout para terminar
        - exit para salir de SSH

*   Política de contraseñas: https://www.zonasystem.com/2020/04/gestion-de-las-politicas-de-contrasenas-en-linux-logindefs-pam-pwquality-cracklib.html

        - $ sudo vi /etc/login.defs
        - 160 PASS_MAX_DAYS   30
        - 161 PASS_MIN_DAYS   2
        - 162 PASS_WARN_AGE   7
        - $ sudo apt install libpam-pwquality
        - $ dpkg -l | grep libpam-pwquality
        - $ sudo vi /etc/pam.d/common-password
        - Ddespués de "password [success=2 default=ignore] pam_unix.so obscure sha512" se añade:
                minlen=10 -> longitud mínima
                Resultado:
                    password [success=2 default=ignore] pam_unix.so obscure sha512
        - Despues de "password requisite pam_pwquality.so retry=3" se añade:
                ucredit=-1 dcredit=-1 -> a continuación, para contener al menos un uppercase y un número.
                maxrepeat=3 -> a continuación, para permitir un máximo de 3 caracteres repetidos
                usercheck=0 ->
                reject_username -> para rechazar la contraseña si contiene el username de alguna forma
                difok=7 -> número de cambios necesario del viejo al nuevo pass
                enforce_for_root -> para aplicar la misma política al root                
                Resultado:
                    password requisite pam_pwquality.so retry=3 lcredit =-1 ucredit=-1 dcredit=-1 maxrepeat=3 usercheck=0 difok=7 enforce_for_root

*   Comprobaciones: https://conpilar.kryptonsolid.com/como-eliminar-un-usuario-de-linux-de-un-grupo/

                    http://aplicacioneslibreuso.blogspot.com/2016/02/gestion-de-usuario-en-linux-elimina-un.html

        - $ passwd <- change user password
        - $ sudo adduser <username>
        - $ getent passwd <username>
        - $ sudo chage -l <username>
        - $ sudo groupadd <groupname>
        - $ getent group <groupname>
        - $ cut -d: -f1 /etc/passwd -> comprobar usuarios locales
        - 
 
*   cron: https://www.redeszone.net/tutoriales/servidores/cron-crontab-linux-programar-tareas/

    wall: https://www.computerfiction.com/como-usar-el-comando-wall-en-linux/

        - $ systemctl enable cron
        - $ sudo crontab -u root -e
        - linea 23 pasa de:
        # m h  dom mon dow   command
        a:
        */10 * * * * sh /path/to/script
        .
        - $ sudo crontab -u root -l
        - archivo monitoring.sh -> https://github.com/yavuzsonmez/Born2beRoot/blob/master/monitoring.sh
        - $ sudo vim /usr/local/bin/monitoring.sh y pegar el texto
        - $ sudo chmod 755 /usr/local/bin/monitoring.sh
        - $ sudo systemctl enable cron
        - $ sudo crontab -e, también:
        - $ sudo crontab -u root -e
        # m h  dom mon dow   command
        a:
        */10 * * * * sh /usr/local/bin/monitoring.sh
    Así se ejecuta cada 10 minutos (x:10, x:20, x:30,...), pero para quelo haga 10 minutos después del arranque tenemos que meter un retraso:
        - $ sudo apt install bc -> basic calculator


*   Qué es un servidor web? https://es.wikipedia.org/wiki/Servidor_web#:~:text=Un%20servidor%20web%20o%20servidor,aplicaci%C3%B3n%20del%20lado%20del%20cliente

*   Lighttpd: https://es.wikipedia.org/wiki/Lighttpd

*   MariaDB: https://es.wikipedia.org/wiki/MariaDB

*   Wget: https://es.wikipedia.org/wiki/GNU_Wget

*   Instalamos utilidades:

        - $ sudo apt-get install wget

        - $ sudo apt-get install vim

        - $ sudo apt-get install zsh

        - $ zsh --version

        - $ sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"

*       WORDPRESS:
                - GIT: 
                        - $ apt-get update -y
                        - $ apt-get upgrade -y
                        - $ apt-get install git -y
                        - $ git --version
                - CURL:
                        - $ sudo apt install curl
                        - $ sudo curl -sSL https://packages.sury.org/php/README.txt | sudo bash -x
                        - $ sudo apt update 
                - Lighttpd: antes nos aseguramos que no está ya instalado Apache como dependencias de otros paquetes, como PHP:
                        - systemctl status apache2
                        - $ sudo apt purge apache2 -> si es necesario desinstalar
                        - $ sudo apt install lighttpd
                        - $ dpkg -l | grep lighttpd
                        - $ sudo lighttpd -v
                        - $ sudo systemctl start lighttpd
                        - $ sudo systemctl enable lighttpd
                        - $ sudo systemctl status lighttpd
                        - $ sudo ufw allow http
                        - $ sudo ufw status
                - MariaDB:
                        - $ sudo apt install mariadb-server
                        - $ dpkg -l | grep mariadb-server
                        - $ sudo mysql_secure_installation -> script interactivo para cambiar opciones por defecto que son inseguras
                                Enter current password for root (enter for none): #Just press Enter (do not confuse database root with system root)
                                Set root password? [Y/n] n
                                Remove anonymous users? [Y/n] Y
                                Disallow root login remotely? [Y/n] Y
                                Remove test database and access to it? [Y/n] Y
                                Reload privilege tables now? [Y/n] Y
                        - sudo mariadb -> para entrar a la consola de MAriaDB
                                > CREATE DATABASE <database-name>;
                                > GRANT ALL ON <database-name>.* TO '<username>'@'localhost' IDENTIFIED BY '<password>' WITH GRANT OPTION; 
                                        crear usuario ydarle todos los permisos
                                > FLUSH PRIVILEGES; 
                                > exit
                        - $ mariadb -u <username-2> -p -> Comprobar que todo ok logándose
                            Enter password: <password-2>
                            MariaDB [(none)]> SHOW DATABASES;
                            MariaDB [(none)]> exit
                - Instalando PHP: 
                        - $ sudo apt install php-cgi php-mysql
                        - $ dpkg -l | grep php
                - Descargando e instalando WordPress:
                        - $ sudo wget http://wordpress.org/latest.tar.gz -P /var/www/html
                        - $ sudo tar -xzvf /var/www/html/latest.tar.gz
                        - $ sudo rm /var/www/html/latest.tar.gz
                        - $ sudo cp -r /var/www/html/wordpress/* /var/www/html
                        - $ sudo rm -rf /var/www/html/wordpress
                        - $ sudo cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php
                        - $ sudo nano /var/www/html/wp-config.php
                                23 define( 'DB_NAME', '<database-name>' );^M
                                26 define( 'DB_USER', '<username-2>' );^M
                                29 define( 'DB_PASSWORD', '<password-2>' );^M
                        - $ sudo lighty-enable-mod fastcgi -> activa el módulo FastCGI, que es una variación de la ya conocida Common Gateway Interface (CGI
                                o Interfaz Común de Entrada). El principal objetivo de FastCGI es reducir la carga asociada con el hecho de interconectar el
                                servidor web y los programas Common Gateway Interface, permitiéndole a un servidor atender más peticiones a la vez.
                        - $ sudo service lighttpd force-reload
                - Descargando y configurando FTP:
                        - $ sudo apt install vsftpd
                        - $ dpkg -l | grep vsftpd
                        - $ sudo ufw allow 21
                        - $ sudo nano /etc/vsftpd.conf
                                31 #write_enable=YES
                        - $ sudo mkdir /home/<username>/ftp
                        - $ sudo mkdir /home/<username>/ftp/files
                        - $ sudo chown nobody:nogroup /home/<username>/ftp ->El comando chown permite cambiar el propietario de un archivo o directorio en sistemas tipo UNIX. Puede especificarse tanto el nombre de un usuario, así como el identificador de usuario y el identificador de grupo.
                        - $ sudo chmod a-w /home/<username>/ftp
                                <~~~>
                                user_sub_token=$USER
                                local_root=/home/$USER/ftp
                                <~~~>
                        - Para evitar que el usuario acceda a ficheros o comandos fuera del directorio descomentar esta línea:
                                114 #chroot_local_user=YES
                        - $ sudo vi /etc/vsftpd.userlist
                          $ echo <username> | sudo tee -a /etc/vsftpd.userlist
                                <~~~>
                                userlist_enable=YES
                                userlist_file=/etc/vsftpd.userlist
                                userlist_deny=NO
                                <~~~>
                        - $ ftp <ip-address>

*   Contraseñas :
        - cifrado: GaussBoole2357
        - root: Gauss.Boole.2357/Temporal.2357
        - jolopez-: Logrosa.2357/Temporal.5795       
        - ardeiro: Chancela.2357/Temporal.2357
        - pass_test: Logrosa.579    
        - MariaDB       ->      root password = "Gauss.Boole.2357" 
                                database= bonus_db

*   Paso a paso:
 
        - su -
        - apt install sudo
        - dpkg -l | grep sudo
        - adduser <username> sudo / usermod -aG sudo <username>
        - getent group sudo
        - reboot
        - sudo -v
        - sudo apt update
        - sudo mkdir /var/log/sudo
        - sudo vi /etc/sudoers.d/sudoconfig
                Defaults        passwd_tries=3
                Defaults        badpass_message="Write the right password, please!! Are you drunk?"
                Defaults        logfile="/var/log/sudo/<filename>"
                Defaults        log_input,log_output
                Defaults        iolog_dir="/var/log/sudo"
                Defaults        iolog_dir="/var/log/sudo"
                Defaults        requiretty
                Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
        -sudo apt install openssh-server
        - dpkg -l | grep ssh
        - sudo vi /etc/ssh/sshd_config
                13 #Port 22 -> Port 4242
                32 #PermitRootLogin prohibit-password -> PermitRootLogin no
                
        - sudo service ssh status / systemctl status ssh
        - sudo apt install ufw
        - dpkg -l | grep ufw
        - sudo ufw enable
        - sudo ufw allow 4242
        - sudo ufw status
        - Para iniciar y cerrar sesión:
                ssh <username>@<ip-address> -p 4242
                logout / exit
        - sudo vi /etc/login.defs
                160 PASS_MAX_DAYS   99999 -> PASS_MAX_DAYS   30
                161 PASS_MIN_DAYS   0 -> PASS_MIN_DAYS   2
                162 PASS_WARN_AGE   7 -> 
        - sudo apt install libpam-pwquality
        - dpkg -l | grep libpam-pwquality
        - sudo vi /etc/pam.d/common-password
                25 password        requisite                       pam_pwquality.so retry=3
                minlen=10
                ucredit=-1 dcredit=-1
                maxrepeat=3
                reject_username
                difok=7
                enforce_for_root
                password requisite pam_pwquality.so retry=3 minlen=10 ucredit=-1 dcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root
        - sudo adduser <username>
        - getent passwd <username>
        - sudo chage -l <username>
        - sudo addgroup user42
        - sudo adduser <username> user42 / sudo usermod -aG user42 <username>
        - getent group user42
        - sudo crontab -u root -e
                23 # m h  dom mon dow   command -> 23 */10 * * * * sh /path/to/script
        - sudo crontab -u root -l
        - sudo service cron stop
        - sudo /etc/init.d/cron stop
        - sudo apt install lighttpd
        - dpkg -l | grep lighttpd
        - sudo ufw allow 80
        - sudo apt install mariadb-server
        - dpkg -l | grep mariadb-server
        - sudo mysql_secure_installation
                Enter current password for root (enter for none): #Just press Enter (do not confuse database root with system root)
                Set root password? [Y/n] n
                Remove anonymous users? [Y/n] Y
                Disallow root login remotely? [Y/n] Y
                Remove test database and access to it? [Y/n] Y
                Reload privilege tables now? [Y/n] Y
        - sudo mariadb
                MariaDB [(none)]> CREATE DATABASE <database-name>;
                MariaDB [(none)]> GRANT ALL ON <database-name>.* TO '<username-2>'@'localhost' IDENTIFIED BY '<password-2>' WITH GRANT OPTION;
                MariaDB [(none)]> FLUSH PRIVILEGES;
                MariaDB [(none)]> exit
        - mariadb -u <username-2> -p
                $ mariadb -u <username-2> -p
                Enter password: <password-2>
                MariaDB [(none)]>
                MariaDB [(none)]> SHOW DATABASES;
                        +--------------------+
                        | Database           |
                        +--------------------+
                        | <database-name>    |
                        | information_schema |
                        +--------------------+
                MariaDB [(none)]> exit                
        - sudo apt install php-cgi php-mysql
        - dpkg -l | grep php
        - sudo apt install wget
        - sudo wget http://wordpress.org/latest.tar.gz -P /var/www/html
        - sudo tar -xzvf /var/www/html/latest.tar.gz
        - sudo rm /var/www/html/latest.tar.gz
        - sudo cp -r /var/www/html/wordpress/* /var/www/html
        - sudo rm -rf /var/www/html/wordpress
        - sudo cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php
        - sudo vi /var/www/html/wp-config.php
                23 define( 'DB_NAME', 'database_name_here' );^M -> define( 'DB_NAME', '<database-name>' );^M
                26 define( 'DB_USER', 'username_here' );^M -> define( 'DB_USER', '<username-2>' );^M
                29 define( 'DB_PASSWORD', 'password_here' );^M -> define( 'DB_PASSWORD', '<password-2>' );^M
        - sudo lighty-enable-mod fastcgi
        - sudo lighty-enable-mod fastcgi-php
        - sudo service lighttpd force-reload
        - sudo apt install vsftpd
        - dpkg -l | grep vsftpd
        - sudo ufw allow 21
        - sudo vi /etc/vsftpd.conf
                31 #write_enable=YES -> write_enable=YES
        - sudo mkdir /home/<username>/ftp
        - sudo mkdir /home/<username>/ftp/files
        - sudo chown nobody:nogroup /home/<username>/ftp
        - sudo chmod a-w /home/<username>/ftp
                <~~~>
                user_sub_token=$USER
                local_root=/home/$USER/ftp
                <~~~>
                114 #chroot_local_user=YES -> chroot_local_user=YES
        - sudo vi /etc/vsftpd.userlist
        - echo <username> | sudo tee -a /etc/vsftpd.userlist
                <~~~>
                list_enable
                userlist_file=/etc/vsftpd.userlist
                userlist_deny=NO
                <~~~>
        - ftp <ip-address>
        - https://www.rosehosting.com/blog/how-to-install-clamav-on-debian-9-and-scan-for-vulnerabilities/
        - sudo systemctl stop clamav-freshclam.service
        - sudo systemctl stop clamav-daemon.service
