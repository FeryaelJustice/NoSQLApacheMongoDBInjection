# NoSQLApacheMongoDBInjection

Bienvenidos al tutorial de NoSQL Injection con Apache2 y MongoDB.

Veremos cómo se encuentra y mitiga un ataque tipo Injection en bases de datos NoSQL.
Esta vulnerabilidad permite al atacante saber datos colarse en el sistema haciéndose pasar por un usuario sin saber su contraseña.

## VIDEO

Vídeo: [VÍDEO EXPLICATIVO Y TUTORIAL DE NoSQL Apache MongoDB Injection](https://youtu.be/4nq2OQDwkMo).

## REQUISITOS

Necesitamos:

1. MongoDB.

2. Apache2.

3. Un servidor o máquina preferiblemente Linux para hacer esta tarea.

## ANÁLISIS

Primero instalaremos Apache2 y MongoDB.

Segundo, configuraremos la ruta de logs de mongo con este comando.
`mongod --dbpath /var/lib/mongo --logpath /var/log/mongodb/mongod.log`

Tercero, crearemos el usuario uoc dentro de la base de datos UOC y también insertamos varios usuarios en la colección de mongo usuario. Todo esto se crea por defecto aunque no exista esta colección ni base de datos, en mongo se crean al usarlo, no hace falta un create.
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

Cuarto, instalamos pecl, que son extensiones para php con este comando.

Quinto, configuraremos mongo con pecl para que esté integrado con php.
```
$ sudo pecl install mongodb
$ sudo find / -name "php.ini"
$ sudo su
$ echo "extension=mongodb.so" >> /etc/php/7.4/apache2/php.ini
$ exit
$ sudo systemctl restart apache
```

Sexto, instalamos composer [TUTORIAL DE INSTALACION DE COMPOSER](https://installati.one/kalilinux/composer/), la cual es una herramienta popular de administración de dependencias para PHP y la cual omitiremos el tutorial de su instalación, podéis encontrar en su web oficial el tutorial para instalarlo, y una vez instalado, ejecutaremos el siguiente comando para añadir a php las dependencias de mongo de be y terminar de vincularlos, y todo esto estará en apache.

(Para evitar problemas cojo la propiedad del directorio /var/www/html) -> `sudo chown -R sergi:sergi /var/www/html/`
`sudo composer require mongodb/mongodb`

## MITIGACIÓN

Para mitigarlo, sanitizaremos los valores de entrada del formulario forzándolos a que sean estrings. Lo veremos en los vídeos del tutorial de youtube, pero básicamente
es reemplazar las variables del formulario en el mongo.php (código):
```php
// $uname = $_POST["uname"];
// $psw = $_POST["psw"];
$uname = filter_var($_POST["uname"], FILTER_SANITIZE_STRING);
$psw = filter_var($_POST["psw"], FILTER_SANITIZE_STRING);
```

## AUTORES

Vicente Pedro García y Fernando González.
