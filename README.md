## Laboratorio de formulario
Adrián Donoso  Implementación de Aplicaciones Web

# estructura

    mi-formulario-php/
    ├── .vscode/launch.json
    ├── app/
    │   ├── index.php
    │   ├── encabezado.html
    │   ├── pie.html
    │   └── crear.php
    ├── docker-compose.yml
    └── README.md

# como se levanta

docker compose up -d

y se entra en http://localhost:8080 y para pararlo docker compose down

# como funciona

son dos paginas index.php pinta el formulario con el encabezado y el pie metidos con includes y crear.php recibe los datos

lo que los manda es esta linea

<form action="crear.php" method="post">

action dice a que pagina van y post dice que van en el cuerpo y no en la url y cada campo lleva un name que es la clave con la que php lo lee en $_POST

los idiomas son distintos porque el name es idiomas[] con corchetes y eso hace que php los meta todos en un array en vez de quedarse con el ultimo por eso van con un foreach

# lo que mas me ha gustado

que si no marcas nada el codigo falla por dentro sin que se note

mande el formulario sin tocar los idiomas ni la nacionalidad y la pagina sale igual pero en el log estaban los avisos

PHP Warning:  Undefined array key "idiomas" in /app/crear.php on line 23
PHP Warning:  foreach() argument must be of type array|object, null given on line 32

un radio o un checkbox sin marcar no se manda y el foreach se come un null y no se ve en pantalla porque esta imagen trae display_errors en Off

# seguridad

esto es lo que mas me ha llamado la atencion porque crear.php coge lo que le llega y lo escupe con un echo sin mirarlo

probe a meter esto en el nombre

<b>hola</b>

y fijate que en la pagina el hola sale en negrita o sea que el navegador se lo ha tragado como html y no como texto y ahi ya no es un fallo tonto porque si en vez del b meto un script es codigo mio corriendo en el navegador del que abra la pagina

luego fui a ver por donde iria esto el dia que haya base de datos y mande esto en el nombre

' OR '1'='1

aqui no hay base de datos asi que no peta nada pero llega entero a crear.php sin escapar y si alguien lo pega en una consulta a mano

INSERT INTO usuarios VALUES ('$nombre')

esa comilla cierra la cadena donde no toca y lo de detras ya se lo lee el motor como consulta y no como dato

es el mismo fallo en los dos sitios me fio de lo que viene de fuera y lo pego donde sea sin tocarlo al html se le pasa htmlspecialchars y a sql se le habla con consultas preparadas

# cosas que me pasaron

entrando directo a crear.php por la barra salen siete avisos de esos porque un get no trae nada en $_POST

la contraseña va en claro y al enviar me salto firefox preguntando si la guardaba y luego crear.php la escribe en pantalla

el enunciado pone cabecera.html en el arbol pero el codigo hace include encabezado.html asi que deje el del include

# repositorio

https://github.com/htfrfr/iaweb-formulario-php
