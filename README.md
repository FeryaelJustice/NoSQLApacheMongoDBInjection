# NoSQLApacheMongoDBInjection

Bienvenidos al tutorial de NoSQL Injection con Apache2 y MongoDB.

Veremos cómo se encuentra y mitiga un ataque tipo Injection en bases de datos NoSQL.
Esta vulnerabilidad permite al atacante obtener datos y colarse en el sistema haciéndose pasar por un usuario sin conocer su contraseña.

## VÍDEO

Vídeo: [VÍDEO EXPLICATIVO Y TUTORIAL DE NoSQL Apache MongoDB Injection](https://youtu.be/4nq2OQDwkMo).

## REQUISITOS

Necesitamos:

1. MongoDB.

2. Apache2.

3. Un servidor o máquina preferiblemente Linux para hacer esta tarea.

## ANÁLISIS

Primero instalaremos Apache2 y MongoDB.

Segundo, configuraremos la ruta de los registros de MongoDB con este comando.
`mongod --dbpath /var/lib/mongo --logpath /var/log/mongodb/mongod.log`

Tercero, crearemos el usuario uoc en la base de datos UOC e insertaremos varios usuarios en la colección usuario de MongoDB. MongoDB crea la colección y la base de datos al usarlas, así que no hace falta ejecutar create.
`mongo`
`use UOC`
`db.createUser({user: "uoc", pwd: "Contrasenya123", roles: ["readWrite"]})`
```
db.users.insertMany([
{
"username": "jordi",
"password": "jordi123"
},
{
"username": "sergi",
"password": "sergi123"
},
{
"username": "audrey",
"password": "audrey123"
}
]);
```

Cuarto, instalamos PECL, el gestor de extensiones de PHP, con este comando.

Quinto, configuraremos mongo con pecl para que esté integrado con php.
```
$ sudo pecl install mongodb
$ sudo find / -name "php.ini"
$ sudo su
$ echo "extension=mongodb.so" >> /etc/php/7.4/apache2/php.ini
$ exit
$ sudo systemctl restart apache
```

Sexto, instalamos Composer siguiendo este [tutorial de instalación](https://installati.one/kalilinux/composer/). Composer es una herramienta popular para administrar dependencias de PHP. Omitimos los pasos de instalación, que también están disponibles en su sitio oficial. Una vez instalado, ejecutamos el siguiente comando para añadir a PHP las dependencias de MongoDB e integrarlas con Apache.

(Para evitar problemas, cambio el propietario del directorio /var/www/html): `sudo chown -R sergi:sergi /var/www/html/`
`sudo composer require mongodb/mongodb`

## MITIGACIÓN

Para mitigarlo, sanitizaremos los valores de entrada del formulario y los convertiremos en cadenas. Lo veremos en los vídeos del tutorial de YouTube; básicamente,
consiste en reemplazar las variables del formulario en mongo.php (código):
```php
// $uname = $_POST["uname"];
// $psw = $_POST["psw"];
$uname = filter_var($_POST["uname"], FILTER_SANITIZE_STRING);
$psw = filter_var($_POST["psw"], FILTER_SANITIZE_STRING);
```

## AUTORES

Vicente Pedro García y Fernando González.
