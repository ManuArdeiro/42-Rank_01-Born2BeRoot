# 42_born2beroot

*   Contraseñas:
        -cifrado: GaussBoole2357
        -jolopez42: Logrosa.579
        -pass_test: Test.Password.2357

*   Decido utilizar Debian porque será mi primera máquina virtual y no tengo experiencia en administración de sistemas. Además es una de las versiones más extendidas de linux y de las más versátiles.

    https://www.linuxadictos.com/debian-vs-ubuntu.html

    https://www.linuxadictos.com/ranking-de-las-mejores-distribuciones-gnulinux-de-2015.html

*   Qués es SELinux y AppArmor?

    https://es.wikipedia.org/wiki/SELinux
    
    https://es.wikipedia.org/wiki/AppArmor
    
*   Para crear las particiones se utiliza LVM. https://blog.inittab.org/administracion-sistemas/lvm-para-torpes-i/

    ![image](https://user-images.githubusercontent.com/102896906/168761466-6e486702-7ee0-47a7-8029-1b7a0b6bba52.png)
    
    Recordatorio sobre particiones: https://www.wikiversus.com/informatica/discos-duros/particiones-primarias-vs-extendidas-vs-logicas/
    
    Se utiliza GRUB para el arranque: https://es.wikipedia.org/wiki/GNU_GRUB
    
    Los tipos de particiones de Linux son: https://www.laboratoriolinux.es/index.php/-noticias-mundo-linux-/software/18656-diferencias-entre-ext2-ext3-y-ext4.html
    
    Las particiones recomendadas para Linux son: https://www.daniloaz.com/es/la-importancia-de-particionar-correctamente-un-disco-en-linux/
    
    La partición swap: 
    
    https://blog.desdelinux.net/que-es-el-swap-en-linux-y-como-utilizarlo/

    https://hipertextual.com/2015/09/swap-en-linux
    
*   Elegido apt para la instalación de paquetes: https://juncotic.com/apt-vs-apt-get-vs-aptitude-algunas-notas/

*   Diferencia su y sudo: https://blog.desdelinux.net/cual-es-la-diferencia-entre-sudo-y-su/
   
    Para administrar permisos se utiliza sudo: https://www.linuxtotal.com.mx/index.php?cont=info_admon_014
    
    Comandos:
   
            -   adduser <username> sudo -> añadir usuario a sudo
            -   usermod -aG sudo <username> -> alternativa para añadir usuario a grupo
            -   getent group sudo -> comprobar los usarios de un grupo
            -   sudo -v -> comprobar credenciales
            -   sudo addgroup group -> añadir el grupo group
            -   sudo adduser user group -> añadir un usuario a un grupo
            
    Configuración:
    
            -   sudo nano /etc/sudoers.d/<filename> donde fiñename puede ser sudoconfig, archivo que se crea
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

*   Instalar ssh: https://es.wikipedia.org/wiki/Secure_Shell

        -   sudo apt install openssh-server
        -   dpkg -l | grep ssh -> verificar la correcta instalación

    Configuración: sudo vi /etc/ssh/sshd_config

        -   Línea 13 cambia de "#Port 22" a "Port 4242" para utilizar solo el puerto 4242.
        -   Cambiar linea 32 de "#PermitRootLogin prohibit-password" a "PermitRootLogin no" para no permitir SSH login como root.
    
    Para comprobar ssh: sudo service ssh status
 
 *  Instalar UFW: sudo apt install ufw.
 
        - sudo apt install ufw -> verificar instalación
        - sudo ufw enable -> se habilita
        - sudo ufw allow 4242 -> para permitir conexiones entrantes utilizando el puerto 4242
        - sudo ufw status -> comprobar estado

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
        - minlen=10 -> después de "password  requisite    pam_pwquality.so retry=3" 
        - ucredit=-1 dcredit=-1 -> a continuación, para contener al menos un uppercase y un número.
        - maxrepeat=3 -> a continuación, para permitir un máximo de 3 caracteres repetidos
        - reject_username -> para rechazar la contraseña si contiene el username de alguna forma
        - difok=7 -> número de cambios necesario del viejo al nuevo pass
        - enforce_for_root -> para aplicar la misma política al root

*   Comprobaciones: https://conpilar.kryptonsolid.com/como-eliminar-un-usuario-de-linux-de-un-grupo/
                    http://aplicacioneslibreuso.blogspot.com/2016/02/gestion-de-usuario-en-linux-elimina-un.html

        - $ passwd <- change user password

        - $ sudo adduser <username>
        - $ getent passwd <username>
        - $ sudo chage -l <username>
 
*   cron: https://www.redeszone.net/tutoriales/servidores/cron-crontab-linux-programar-tareas/

        - $ sudo crontab -u root -e
        - linea 23 pasa de:
        # m h  dom mon dow   command
        a:
        */10 * * * * sh /path/to/script
        .
        - $ sudo crontab -u root -l
