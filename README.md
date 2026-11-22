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

y se entra en http://localhost:8080, para pararlo docker compose down

# como funciona

son dos paginas, index.php pinta el formulario con el encabezado y el pie metidos con includes, y crear.php recibe los datos

lo que manda los datos es esta linea

    <form action="crear.php" method="post">

action dice a que pagina van y post dice que van en el cuerpo y no en la url, cada campo lleva un name y ese name es la clave con la que php lo lee en $_POST

crear.php no pinta formulario, solo lee $_POST y lo escribe, los idiomas son distintos porque el name es idiomas[] con corchetes y eso hace que php los meta todos en un array en vez de quedarse con el ultimo, por eso van con un foreach

# lo que mas me ha gustado

ver que cada campo manda una cosa distinta, el select manda el value y no el texto que se ve, los dos radio comparten name y por eso solo puedes elegir uno, y los checkbox llegan juntos en un array por los corchetes

y que si no marcas nada el codigo falla por dentro sin que se note, mande el formulario sin tocar los idiomas ni la nacionalidad y la pagina sale igual pero en docker logs php-practicas estaban los avisos

    PHP Warning:  Undefined array key "idiomas" in /app/crear.php on line 23
    PHP Warning:  foreach() argument must be of type array|object, null given on line 32

un checkbox o un radio sin marcar no se manda, no existe esa clave en $_POST y el foreach se come un null, no se ve en el navegador porque esta imagen trae display_errors en Off y log_errors en On, para que no pasara habria que mirarlo antes con isset

# cosas que me pasaron

entrando directo a crear.php por la barra del navegador salen siete avisos de esos, un get no trae nada en $_POST

crear.php imprime lo que llega tal cual con echo asi que si metes etiquetas html en el nombre se cuelan en la pagina, iria con htmlspecialchars

la contraseña va en claro, al enviar me salto firefox preguntando si la guardaba y luego crear.php la escribe en pantalla, con una de verdad no se hace nada de esto

el enunciado pone cabecera.html en el arbol pero el codigo hace include encabezado.html, deje el del include
