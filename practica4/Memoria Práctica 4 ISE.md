# Memoria Práctica 4 ISE

###### Ejercicio 1

Vamos a empezar por la descarga de Phoronix. Para ello podemos ir a su página oficial http://www.phoronix-test-suite.com/?k=downloads .

Empiezo en Uuntu. En mi caso me interesa más la instlación mediante terminal, para la cual realizo los siguientes pasos:

```bash
wget http://phoronix-test-suite.com/releases/repo/pts.debian/files/phoronix-test-suite_10.8.3_all.deb
sudo dpkg -i phoronix-test-suite_10.8.3_all.deb
sudo apt -f install
```

En realidad el último paso puede no ser necesario pero así viene indicado en la documentación.

Una vez que hayamos esto hecho podemos usar el comando `phoronix-test-suite` para ver el conjunto de opciones disponibles.

![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/1.1.png)

Para ver los tests disponibles utilizamos la orden `phoronix-test-suite list-available-suites`. Pero obtenemos numerosos fallos. Esto de debe a que necesitamos la herramienta unzip. Para instalarlo hacemos `sudo apt install unzip`. Una vez que la tenemos repetimos el comando anterior y obtenemos la siguiente salida:

![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/1.3.png)

En mi caso voy a ejecutar los tests pts/memory y pts/chess. Para bajarse y ejecutar los tests existe un comando único, `phoronix-test-suite benchmark pts/nombre`. Además haciendo `phoronix-test-suite info pts/nombre` obtendremos información al respecto. Si aplicamos este comando al chess, por ejemplo, podemos ver los tests que ejecuta. Vamos a ejecutar el test pts/tscp y el test pts/idle:

> Aquí mi máquina virtual, a tal punto que también se rompió la propia aplicación de virtualBox, dejó de funcionar y tuve que reinstalar todo. Esto explica el cambio de terminal.

![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/1.6.png)

![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/1.7.png)

Vamos a darle a que sí queremos, pero en caso de que no quisiéramos visualizarlos en el momento bastaría con hacer `phoronix-test-suite result-file-to-text <file>`, donde file es el archivo que hemos indicado antes.

![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/1.8.png)

Ahora vamos con el idle. Este nos pedirá una cantidad de tiempo en minutos. Indico el valor 1:

![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/1.9.png)

Este test es un poco especial, porque no nos da una puntuación, solo nos informa si nuestro sistema ha aguantado estar en idle la cantidad de tiempo indicada. Para más información véase [Timed Idle Benchmark - OpenBenchmarking.org](https://openbenchmarking.org/test/pts/idle).

Vamos ahora con CentOS. Para ello nos vamos a su documentación oficial para instalarlo y vemos que hay que hacer:

```bash
sudo yum install wget php-cli php-xml php-json bzip2
sudo wget https://phoronix-test-suite.com/releases/phoronix-test-suite-8.4.1.tar.gz
sudo tar xvfz phoronix-test-suite-8.4.1.tar.gz
cd phoronix-test-suite
sudo ./install-sh
```

Para ver los tests disponibles hacemos `/usr/bin/phoronix-test-suite list-available-suites`. Nos pedirá que aceptemos los términos y condiciones y el envío de datos anónimos. Vamos ahora a ejecutar los mismos tests pero teniendo en cuenta que el comando para llamar a phoronix es `/usr/bin/phoronix-test-suite`. 

![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/1.9.2.png)

Para tscp:

![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/1.10.png)

Y para idle:

![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/1.11.png)

El test idle en ambos tiene poco que comentar. Se esperaba que ambos se pasará y así ha ocurrido. En el test tscp podemos ver que en Ubuntu va un poco mejor.

###### Ejercicio 2

Antes que nada descargamos jMeter en nuestro ordenador desde https://jmeter.apache.org/download_jmeter.cgi 

Cuando lo ejecutamos nos aparece la siguiente ventana:

![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/2.1.png)

Vamos instalar docker en Ubuntu Server. Los pasos realizados a continuación son tal cual del guión de la asignatura:

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt update
apt search docker-ce
sudo apt install docker-ce
sudo systemctl status docker #start and enable en caso de que no este
```

Ahora solo queda que nos añadamos como usuario docker:

```bash
sudo usermod -aG docker alejandrorm
exit #Y nos volvemos a logear
docker run hello-wold #Para comprobar si funciona
```

![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/2.2.png)

Ahora que ya tenemos docker vamos a por docker-compose.

```bash
sudo apt install docker-compose
docker-compose --version #Para comprobar
```

Ahora toca instalar la aplicación. Para ello usamos el repositorio https://github.com/davidPalomar-ugr/iseP4JMeter 

```bash
git clone https://github.com/davidPalomar-ugr/iseP4JMeter.git
cd iseP4JMeter
docker-compose up
sudo ufw allow 3000/tcp
```

![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/2.3.png)

Nos vamos ahora a JMeter. Vamos a ir satisfaciendo las condiciones del enunciado:

- El test debe tener parametrizados el Host y el Puerto en el Test Plan.
  
  ![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/2.4.png)

- Debe hacer dos grupos de hebras distintos para simular el acceso de los
  alumnos y los administradores.
  
  ![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/2.5.1.png)
  
  ![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/2.5.2.png)

- El login de alumno, su consulta de datos (recuperar datos alumno) y login
  del adminsitrador son peticiones HTTP.
  
  ![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/2.6.png)

- Los alumnos deben coger sus credenciales de alumnos.csv
  
  ![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/2.7.png)
  
  ![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/2.8.png)

- Use una expresión regular (Regular Expressión Extractor) para extraer el
  token JWT que hay que añadir a la cabecera de las peticiones (usando
  HTTP Header Manager)
  
  ![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/2.9.png)

- Añadimos esperas aleatorias a cada grupo de hebras (Gaussian Random
  Timer)
  
  ![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/2.10.png)

- Vamos con las peticiones GET de los alumnos
  
  ![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/2.11.png)
  
  ![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/2.12.png)

- Añadimos la autorización para acceder a la API
  
  ![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/2.13.png)

- Hacemos todo análogamente para los administradores hasta que nos quede así:
  
  ![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/2.14.png)

- Ahora decimos como queremos visualizar los resultados
  
  ![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/2.15.png)

Ya estaría todo listo, solo faltaría darle a correr y obtenemos los siguientes resultados:

![](/Users/alejandrorubiomartinez/Desktop/ugr/ISE/practica4/2.16.png)
