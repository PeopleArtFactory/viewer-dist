# viewer-dist
Instalacion de modudo:

Paso 1: Descarga e instalación del visor de exposiciones en tu propio servidor web.

Para hacer más sencillo el proceso de exportación, toda la información de cada exposición se guarda en un único archivo de texto (en formato json) por lo que no es necesario la instalación de ningún sistema de base de datos adicional en el nuevo servidor.

El visor de exposiciones ha sido programado como una single-page application (SPA), o aplicación de página única, de forma que todos los códigos (HTML, JavaScript, y CSS)  están ya comprimidos, se cargan una sola vez y pueden ser ejecutados desde cualquier servidor web o hosting compartido, con unos mínimos ajustes en su configuración para que puedan servir sitios estáticos (como bloquear el cacheo de la página index.html). 

En el caso de instalar el modulo en un servidor web ya existente, dispones de varias opciones, dependiendo la infraestructura de destino. Todas ellas requieren una configuración relativamente sencilla, como se indica en esta guía de referencia: https://quasar.dev/quasar-cli/developing-spa/deploying

Si vas a realizar una instalación desde cero, en un nuevo servidor virtual (VM), con acceso remoto SSH y sistema operativo Linux Ubuntu, de forma general, pensamos que una de las mejores opciones, puedes ser usar el entorno Dokku, siguiendo estos sencillos pasos:

1.1.	Antes de iniciar la instalación, te recomendamos  elegir un dominio o subdominio y vincularlo a la IP del nuevo servidor, también, establecer unas claves SSH de acceso, para poder conectarte al mismo mediante consola y que añadas las siguientes líneas al archivo de configuración (normalmente ubicado en .ssh\config):

	Host tudominio.com
	    HostName tudominio.com
	    User dokku
	    IdentityFile  (tu ruta de acceso a clave privada SSH: ~/.ssh/id_rsa)
	    

1.2.	Instala Dokku en tu servidor de destino, escribiendo en la consola estas dos líneas:

	wget https://raw.githubusercontent.com/dokku/dokku/v0.21.4/bootstrap.sh

	sudo DOKKU_TAG=v0.21.4 bash bootstrap.sh

(Cuando se instalen todas las librerías, abre el navegador y ve al dominio elegido o la IP del servidor para completar el proceso de instalación. Si el primer campo de esta en blanco, deberás introducir la clave pública SSH, en el segundo campo puedes añadir la IP del servidor o dominio elegido y el último campo debe quedar sin marcar, para poder definir, más tarde, los dominios asignados a cada aplicación, de manera personalizada).


1.3.	Una vez esté disponible la nueva infraestructura crea una nueva aplicación:

	dokku apps:create paf-viewer
	

1.4.	Descarga y descomprime el código del módulo en tú maquina local, mediante este enlace:

	https://github.com/PeopleArtFactory/viewer-dist/archive/master.zip


1.5.	Entra en la nueva carpeta recién creada, y añade el servidor Dokku como repositorio remoto Git, escribiendo en consola:

	git remote add dokku dokku@tudominio.com:paf-viewer
	

1.6.	Ya puedes publicar el módulo en el servidor, simplemente, haciendo un push al nuevo repositorio remoto dokku en su rama master:

	git push dokku master


1.7.	De forma automática, Dokku realizará los ajustes necesarios para desplegar correctamente la aplicación pero es necesario vincular un dominio, para poder acceder a ella, a través de Internet. Escribe en la consola de tu servidor de destino:
	
	dokku domains:add paf-viewer tudominio.com

1.8.	Para poder acceder con certificado de seguridad (https), puedes instalar el correspondiente plugin oficial de Letsencrypt para Dokku, desde su repositorio y seguir las instrucciones que encontraras en la documentación:

	https://github.com/dokku/dokku-letsencrypt
	

Cuando termine todo el proceso, el visor deberá estar disponible a través del dominio elegido y debes poder cargar la galería de ejemplo:

https://tudominio.com/g/demo




Paso 2: Descarga de exposiciones desde PeopleArtFactory.com. 

Ve al menú de publicación (en la  última pestaña del modo de edición), de la exposición que quieras añadir y pulsa el botón “Exportar galería”. Pasados unos segundos, se descargará en tu máquina local un único archivo comprimido (.zip), con todos los datos, enlaces y los contenidos audiovisuales que usa tu galería.



Paso 3: Carga de los contenidos de cada galería.

El módulo del visor está configurado, de forma predetermina, para  que todas las carpetas de las diferentes exposiciones se guarden junto al resto de archivos pero, si lo deseas, puedes ubicar este almacén general en cualquier otro servidor independiente, con acceso a Internet, pues su ruta de acceso es configurable (modificando, en un editor de texto, el archivo config.json). Recuerda que, si vas a utilizar una ubicación en otro servidor (u otro puerto dentro del mismo), debes tener en cuenta la configuración de seguridad CORS.

Si mantienes las rutas por defecto, para añadir una nueva exposición solo tienes que descomprimir todo el contenido del archivo .zip (obtenido en el paso anterior) en una subcarpeta dentro de la carpeta storage. Puedes subir los archivos directamente, mediante algún tipo de cliente web o sistema de FTP, pero, si has desplegado el sistema de publicación de Dokku (descrito en el paso 1), solo tienes que, una vez hayas ubicado correctamente los archivos en tu máquina local, hacer un commit  y un nuevo push:

	git add .
	git commit –m “Nombre de la exposición”
	git push dokku master

Una vez completados estos pasos, ya deberías poder acceder a la exposición, utilizando esta ruta y como alias el nombre de la carpeta donde se ubican todos los archivos:
  
           https://tudominio.com/g/alias <---- Nombre de la carpeta de la exposición

