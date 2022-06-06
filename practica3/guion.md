# Guión práctica 3 de ISE

Vamos a empezar simulando que se nos rompe un disco duro del raid y vamos a cambiarlo en caliente.

Sin encender nuestra máquina (vamos a usar Ubuntu) en la configuracion de virtual box nos vamos a discos y marcamos la opción de conectable en caliente.

Ahora sí arrancamos nuestra máquina y hacemos `cat /proc/mdstat` para ver la información de nuestraos discos en el raid. Por seguridad tomamos una instantánea pues vamos a romperla.

Con la máquina todavía encendida nos vamos a la configuración de virtual box, discos, seleccionamos desconectar un disco duro y le damos a aceptar. Si nos fijamos en la máquina va a haber un mensaje de error, pero no va a dejar de funcionar. Podemos comprobar el estado con `cat /proc/mdstat` y `lsblk`.

Vamos a volver ahora a la configuración de virtual box y añadimos un nuevo disco y lo comprobamos con la orden `dmesg`. Vamos ahora a particionar el disco para que sea igual que el que no se ha roto.

Para ello hacemos `sudo fdisk /dev/sda` (o sdb dependiendo cual hayamos añadido). La secuencia de letras para replicar el otro disco es:

- [n p 1 \<enter> 4096] 

- [n p 2 4097 618496] 

- [n p \<enter> \<enter>]

- [w]

Para añadir al raid hacemos lo mismo que en la práctica primera, `sudo mdadm --add /dev/md0 /dev/sda2` (o sdb2) y `sudo mdadm --add /dev/md1 /dev/sda3` (o sdb3). Si ahora hacemos `cat /proc/mdstat` vamos a ver una barra de progreso que se actualiza cada vez que lo hacemos. Hacemos captura que se vean las flechitas.

Vamos a crear un script que nos avise cuando nos falla un disco.

El script es el siguiente en Python que llamaremos mon_raid.py:

```python
import re
f=open(’/proc/mdstat’) 
for line in f:
    b=re.findall(’\[[U]∗[_]+[U]∗\]’,line ) 
    if (b!=[]):
        print("−−ERROR EN RAID−−") 
print("−−OK Script−−")
```

Vamos a crear un timer que nos ejecute el script mediante un servicio del sistema. Para ello creamos dos ficheros:

/etc/systemd/system/mon_raid.timer

```bash
[ Unit ]
Description=Monitor RAID status [ Timer ]
OnCalendar=minutely
[ Install ]
WantedBy=timers . target}
```

/etc/systemd/system/mon_raid.service

```bash
[ Unit ]
Description=Monitor RAID status [ Service ]
Type=simple
ExecStart=/usr/bin/python3 /home/alberto/mon−raid .py
```

Los iniciamos con `systemctl start mon_raid.timer` y `sudo systemctl enable mon_raid.timer`. Hacemos `journalctl -u mon_raid` cuando haya pasado algún minutillo y nos saldrán las ejecuciones del script por el demonio cron. Hacemos captura de esto.
