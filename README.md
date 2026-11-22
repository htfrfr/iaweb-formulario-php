# Laboratorio de formularios

Adrian Donoso - Implementacion de Aplicaciones Web

Practica de formularios con php, otra vez sobre el entorno de docker con nginx
y php-fpm de la practica del entorno.

## que hay aqui

    mi-formulario-php/
    ├── .vscode/launch.json     configuracion del debug con xdebug
    ├── app/
    │   ├── index.php           la pagina con el formulario
    │   ├── encabezado.html     el titulo de arriba
    │   ├── pie.html            el pie
    │   └── crear.php           la pagina que recibe los datos
    ├── docker-compose.yml
    └── README.md

## como se levanta

    docker compose up -d

y se entra en http://localhost:8080. para pararlo `docker compose down`.

## como funciona el codigo

son dos paginas, una manda los datos y la otra los recibe.

**index.php** pinta el formulario y de paso mete el encabezado y el pie con
includes igual que en la practica anterior. lo importante del formulario es
esta linea

    <form action="crear.php" method="post">

`action` dice a que pagina se le mandan los datos y `method="post"` dice que van
en el cuerpo de la peticion y no en la url. cada campo lleva un `name` y ese
nombre es la clave con la que luego php lo lee.

**crear.php** no pinta ningun formulario, solo lee lo que le ha llegado en
`$_POST` y lo escribe. `$_POST` es un array que rellena php solo con los datos
que vienen del formulario, la clave es el `name` del campo

    $nombre = $_POST["nombre"];

y para los idiomas es distinto porque el name es `idiomas[]` con corchetes. eso
hace que php meta todos los marcados en un array en vez de quedarse solo con el
ultimo, por eso se recorre con un foreach y se pintan en una lista.

## los tipos de campo y lo que mandan

- **text y password** mandan lo que escribes. el password solo se ve con puntos
  en pantalla, pero va igual de claro que el resto
- **select** manda el `value` de la opcion elegida, no el texto que se ve. o sea
  manda `formacion-profesional` y no "Formación profesional"
- **radio** los dos comparten el mismo `name` y por eso solo puedes elegir uno,
  manda el value del que marques
- **checkbox** cada uno manda su value y como el name acaba en `[]` llegan todos
  juntos en un array

lo he probado mandando el formulario desde el navegador con adrian, universidad,
hispana, y español e ingles marcados, y crear.php devuelve

    Se creará el usuario adrian 1234 universidad hispana adrian@ejemplo.com ejemplo.com
    Idiomas:
      - español
      - inglés

## lo que he visto al intentar romperlo

**si no marcas nada el codigo falla por dentro aunque no se note.** mande el
formulario sin tocar los idiomas ni la nacionalidad y la pagina sale igual, con
la lista de idiomas vacia y sin ningun error en pantalla. pero mirando el log
del contenedor con `docker logs php-practicas` si estaban

    PHP Warning:  Undefined array key "idiomas" in /app/crear.php on line 23
    PHP Warning:  Undefined array key "nacionalidad" in /app/crear.php on line 24
    PHP Warning:  foreach() argument must be of type array|object, null given on line 32

pasa porque un checkbox o un radio que no marcas no se manda, directamente no
existe esa clave en `$_POST`, y el foreach se come un null. no se ve en el
navegador porque esta imagen trae `display_errors` en Off y `log_errors` en On,
asi que los avisos van al log y no a la pagina. si esto fuera de verdad habria
que comprobar antes con `isset()` o poner `$_POST["idiomas"] ?? []`.

**entrando directo a crear.php sin pasar por el formulario pasa lo mismo.** si
pones http://localhost:8080/crear.php en la barra sale la pagina con todos los
huecos vacios y siete warnings en el log, porque un GET no trae nada en $_POST.

**los datos no se filtran.** crear.php coge lo que llega y lo imprime tal cual
con `echo`, asi que si en el nombre metes etiquetas html se van a colar en la
pagina. para que sea seguro habria que sacarlo con `htmlspecialchars()`.

**la contraseña va en claro.** cuando envie el formulario desde el navegador,
firefox me salto con el aviso de si queria guardar la contraseña, y luego
crear.php la imprime en pantalla junto al resto. entre que esto va por http sin
cifrar y que se escribe tal cual, de una contraseña de verdad no se hace nada
de esto.

## cosas del enunciado

igual que en la practica del include, el arbol de carpetas pone `cabecera.html`
pero el codigo hace `include("encabezado.html")`, y ademas el enunciado titula
el fichero como `app/encabezado.php` cuando el contenido es html puro y el
include lo pide con .html. he dejado `encabezado.html`, que es lo que busca el
include.

tambien hay que acordarse del `chmod -R o+rX .` si sale un 403 al levantarlo,
por el umask de la maquina, esta explicado en el README de la practica del
include.

## repositorio

https://github.com/htfrfr/mi-formulario-php
