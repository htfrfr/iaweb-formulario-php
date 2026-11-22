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

crear.php mete en el echo lo que llega sin tocarlo asi que todo lo que escribas sale tal cual en la pagina

probe a poner esto en el nombre

    <b>hola</b>

y sale en negrita o sea que ya no es texto es html mio metido en la pagina y con un script en vez del b seria codigo corriendo en el navegador de quien la abra

aqui no hay base de datos pero el dia que estos datos entren en una consulta pegados a mano pasa lo mismo

    INSERT INTO usuarios VALUES ('$nombre')

probe a mandar esto en el nombre y llega entero a crear.php sin escapar

    ' OR '1'='1

esa comilla cerraria la cadena antes de tiempo y lo de detras entraria como parte de la consulta y eso es una inyeccion sql

en los dos casos la regla es la misma el dato que viene de fuera no se pega nunca a nada se escapa con htmlspecialchars para el html y se usan consultas preparadas para sql

# cosas que me pasaron

entrando directo a crear.php por la barra salen siete avisos de esos porque un get no trae nada en $_POST

la contraseña va en claro y al enviar me salto firefox preguntando si la guardaba y luego crear.php la escribe en pantalla

el enunciado pone cabecera.html en el arbol pero el codigo hace include encabezado.html asi que deje el del include
