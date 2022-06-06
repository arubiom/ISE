#### Ejercicio 1

Lo primero que vamos a hacer es aprovechar que ya tenemos un LAMP para instalarlo en Ubuntu. Para ellos primero nos descargamos el repositorio con `wget https://repo.zabbix.com/zabbix/5.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_5.0-1+focal_all.deb`. Ahora instalamos los paquetes haciendo `dpkg -i zabbix-release_5.0-1+focal_all.deb` y por ultimo actualizamos con `apt update`.

Ahora necesitamos instalar el frontend, el server y los agentes con el comando `apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-agent`.

Vamos a iniciar una consola mysql para root con el comando `mysql -uroot -p` y hacemos lo siguiente:

```sql
create database zabbix character set utf8 collate utf8_bin;
create user zabbix@localhost identified by 'password';
grant all privileges on zabbix.* to zabbix@localhost;
quit;
```

Ahora toca modificar los ficheros /etc/zabbix/zabbix_server.conf y /etc/zabbix/apache.conf. En el primero de ellos cambiamos la línea `DBPassword=password` y en el segundo `php_value date.timezone Europe/London`.

Vamos ahora a reiniciar los servicios con `systemctl restart zabbix-server zabbix-agent apache2`  y `systemctl enable zabbix-server zabbix-agent apache2`.

Ya podemos entrar desde un navegador externo a la dirección http://192.168.105/zabbix y estremos en una pantalla como la siguiente:

![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica3/captura7.png)

Recordamos que por por defecto el puerto es 10050, el usuario Admin y la contraseña zabbix.

Ahora vamos a proceder a monotorizar Ubuntu:

Para ello nos vamos a la pestaña de Hosts y en el que se llama Zabbix Server creamos dos nuevos ítems como el siguiente que monitorice el servicio ssh:

![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica3/captura8.png)

Y otro para el servicio de http:

![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica3/captura9.png)

El resto de opciones no las tocamos.

Con esto ya podemos ir a la pestaña Monitoring -> Lastest Data y seleccionamos uno de los dos servicios que queramos monitorizar.

Vamos ahora a la parte de CentOS. Como siempre, esta parte incluye más complicaciones. Para empezar necesitamos instalar el agente:

Para ello modificamos el fichero /etc/selinux/config. Cambiamos la línea `SELINUX=disable`. Con esto hacemos los comandos `sudo yum install https://repo.zabbix.com/zabbix/5.0/rhel/8/x86_64/zabbix-release-5.0-1.el8.noarch.rpm`y `sudo yum install zabbix-agent`.

Ahora modificamos el ficehro /etc/zabbix/zabbix_agentd.conf añadiendo lo siguiente:

```vbnet
Server=192.168.56.105, 127.0.0.1
ServerActive=192.168.56.105, 127.0.0.1
Hostname=CentOS
```

Con esto ahora vamos a desactivar el firewall como siempre con `sudo firewall-cmd --permanent --add-port=10050/tcp`y lo recargamos con `sudo firewall-cmd --reload`. Ahora iniciamos el servicio con `sudo systemctl enable zabbix-agent`, `sudo systemctl start zabbix-agent` y `service zabbix-agent start`.

Ahora nos vamos a Zabbix y añadimos un nuevo host:

![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica3/captura10.png)

Ahora ya sólo tendríamos que crear los items anteriores pero para este nuevo host.

#### Ejercicio 2

Empezamos por instalar ansible con `sudo apt install ansible` en Ubuntu.

Vamos a añadir nuestras ips al fichero de hosts haciendo `sudo nano /etc/ansible/hosts` y en las ips que aparecen al principio añadimos la 105 y la 110 de Ubuntu y CentOS respectivamente.

![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica3/captura1.png)

Vamos a iniciar CentOS y desde Ubuntu hacemos `ansible all -m ping -u alejandrorm`.

![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica3/captura2.png)

El error que sale se debe a que se está intentando conectar a ssh por el camino predeterminado, el puerto 22, pero nosotros lo cambiamos para que fuera por el puerto 22022. Hacemos `sudo nano /etc/ansible/ansible.cfg` y descomentamos remote_port y añadimos 22022.

![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica3/capturaa3.png)

Si utilizamos otra vez `ansible all -m ping -u alejandrorm` ya funciona:

![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica3/captura4.png)

Ahora vamos a hacer que se ejecute el script que nos informa del estado de nuestro RAID. Empezamos probándolo con `ansible all -a "python3 /home/alejandrorm/mpon-raid.py" -u alejandrorm`.

![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica3/captura5.png)

Obtenemos un fallo porque no hemos añadido el script en CentOS entonces no tiene qué ejecutar. Con `scp -P 22022 /home/alejandrorm/mon-raid.py  alejandrorm@192.168.56.110:/home/alejandrorm/mon-raid.py` debería bastar para pasarlo por ssh.

![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica3/captura6.png)
