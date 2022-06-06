Empezamos por instalar git y clonamos un repositorio que hayamos creado. Modificamos algo en el repositorio local y lo añadimos, comiteamos y pusheamos.

Puede pedir que hagamos:

`git config --global user.email "tu email"`

`git config --global user.name "tu usuario"`

Donde tu email y tu usuario son los de github.

Podemos comprobar que se ha guardado con git status y hacer más cambios.

Para pushear hace falta crear unb personal acces token desde github. Nos vamos a nuestro perfil -> settings -> developer settings -> personal acces tpken ->. generate new token.

También podemos acceder por ssh. Para ello nos vamos en github a nuestro perfil -> settings -> ssh and gpg key y añadimos una nueva key ssh donde añadimos el textaco que sale del comando cat /home/alejandrorm/.ssh/id_rsa.pub (se puede ser pbcopy). Si elegimos hacer este último paso tenemos que hacer el clone del ssh no del http.

Creamos una branch y nos cambiamos entre ellas y subimos solo a una y esas cosas.
