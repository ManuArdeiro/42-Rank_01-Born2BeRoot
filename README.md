# 42_born2beroot

*   Decido utilizar Debian porque será mi primera máquina virtual y no tengo experiencia en administración de sistemas. Además es una de las versiones más extendidas de linux y de las más versátiles.

    https://www.linuxadictos.com/debian-vs-ubuntu.html

    https://www.linuxadictos.com/ranking-de-las-mejores-distribuciones-gnulinux-de-2015.html

*   Para crear las particiones se utiliza LVM. https://blog.inittab.org/administracion-sistemas/lvm-para-torpes-i/

    ![image](https://user-images.githubusercontent.com/102896906/168761466-6e486702-7ee0-47a7-8029-1b7a0b6bba52.png)
    
    Se utiliza GRUB para el arranque: https://es.wikipedia.org/wiki/GNU_GRUB
    
    Los tipos de particiones de Linux son: https://www.laboratoriolinux.es/index.php/-noticias-mundo-linux-/software/18656-diferencias-entre-ext2-ext3-y-ext4.html
    
    Las particiones recomendadas para Linux son: https://www.daniloaz.com/es/la-importancia-de-particionar-correctamente-un-disco-en-linux/
    
    La partición swap: 
    
        -   https://blog.desdelinux.net/que-es-el-swap-en-linux-y-como-utilizarlo/

        -   https://hipertextual.com/2015/09/swap-en-linux
    
*   Para administrar permisos se utiliza sudo: https://www.linuxtotal.com.mx/index.php?cont=info_admon_014
    
    Comandos:
   
            -   adduser <username> sudo -> añadir usuario a sudo
    
            -   usermod -aG sudo <username> -> alternativa para añadir usuario a grupo sudo
 
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
