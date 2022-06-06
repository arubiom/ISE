# Lección 3 de la práctica 2

En esta práctcia vamos a configurar LAMP (Linux, Apache, Mysql, PHP) en nuestros servers, tanto Ubuntu como CentOS. Una explicación rápida de lo que está pasando se puede ver en el siguiente esquema:

Empezamos con ubuntu:

Instalamos tasksel y lo ejecutamos con sudo. Desmarcamos todas las opciones y marcamos LAMP server (al ok se va con tabulador). Si da un error de apt-get update hacemos sudo apt update. En el mensaje de abort Kernel le damos a sí.

Comprobamos con systemctl status los demonios apache2, mysql y los iniciamos con systemctl start. Con php -a podemos entrar en una consola de php.

Nos conectamos desde un navegador externo a ubuntu buscando [http://192.168.56.105](http://192.168.56.105) Si queremos modificar la pagina modificamos el fichero /var/www/html/index.html. Por ejemplo probar a escribir algo debajo del </span> del body (quien sepa html pos que lo use).

Vamos con CentOS:

Hacemos sudo yum install httpd y lo iniciamos con sudo systemctl start. Abrimos el cortafuefos con sudo firewall-cmd --permanent --add-service=http y lo recargamos xon sudo firewall-cmd --reload. Nos conectamos desde un navegador externo a [http://192.168.56.110](http://192.168.56.110).

Nos instalamos con sudo yum install mariadb y mariadb-server y lo iniciamos con systemctl start mariadb. Vamos a poner usuario y contraseña a mariadb. Hacemos mysql_secure_installation. Cuando nos pida contraseña nos pide la de mariadb, actualmente ninguna así que le damos al intro. Le decimos que sí queremos introducir una contraseña y al resto le damos sí a todo.

Instalamos php con sudo yum install php y probamos que funcione con php -a. Salimos con exit. Instalamos con sudo yum install php-mysqli las funciones necesarias. Hacemos sudo nano /var/www/html/index.php y modificamos:

```php
<?

echo("Holi");

$link = mysqli_connect('127.0.0.1::3306','root','practicas,ise');

if (!$link)
    die("No se puede conectar a la db");

echo("Conectado a la db");

?>
```

Ahora hacemos sudo php /var/www/html/index.php y también nos conectamos a http://192.168.56.110/index.php desde un navegador externo.

Hacemos nano /etc/httpd/conf/httpd.conf y nos vamos hasta

```html
<IfModule dir_module>
    DirectoryIndex index.html
</IfModule dir_module>
```

Y la cambiamos por

```html
<IfModule dir_module>
    DirectoryIndex index.html index.php
</IfModule dir_module>
```

Reiniciamos httpd y nos conectamos desde el servidor externo. Si no aparece nada click derecho ver código fuente y veremos que no se ha conectado.

Hacemos sudo getsebool -a para ver todas las variables booleana que tenemos. Usamos | grep httpd y vemos httpd_can_networfk_connect_db en off. La cambiamos con sudo setsebool -P httpd_can_network_connect_db on. Recargamos el navegador.

Modificamos /etc/ssh/shhd_config y passwordAuthetication a yes (reiniciamos el demonio sshd con systemctl) e instalamos epel-release y luego fail2ban y hacemos sudo systemctl start fail2ban. Lo ejecutamos con sudo fail2ban-client y comprobamos el estado de las carceles con sudo fail2ban-client status.

Hacemos sudo nano /etc/fail2ban/jail.conf, pero nos pide que no lo toquemo0s que en cambio usemos el local. Hacemos cp -a /etc/fail2ban/jail.conf /etc/fail2ban/jail.local y lo modificamos hasta # SSH servers y escribimos enabled = true. Si encontramos #bantime.maxtime podemos cambiar el tiempo de baneo. Hacemos sudo systemctl restart fail2ban.

Nos conectamos con ssh alejandrorm@192.168.56.110 -p 22022 desde otra máquina. Si nos equivocamos unas cuantas veces nos baneará. Ahora desde CentOS si hacemos sudo fail2ban-client status nos saldrá la nueva cárcel de nombre X con sudo fail2ban-client status X. Nos desbaneamos con sudo fail2ban-client set X unbanip nuestraip.
