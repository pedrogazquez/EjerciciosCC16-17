##Ejercicios 1: Crear una máquina virtual ubuntu e instalar en ella un servidor nginx para poder acceder mediante web.
Lo primero que he hecho ha sido instalar las herramientas necesarias para usar azure desde el terminal:
```
sudo apt-get install nodejs-legacy
sudo apt-get install npm
sudo npm install -g azure-cli
```

Lo siguiente que hacemos es login en azure con: **azure login**, y abrimos la pestaña del enlace web e introducimos el código que nos ha dado el terminal:
![loginaz](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/Captura%20de%20pantalla%20de%202016-02-02%20212354_zps7l5kmkun.png)

Aquí el enlace web que se abre:

![webAzure](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/Captura%20de%20pantalla%20de%202016-02-02%20212342_zpsjalkn1y5.png)

De esta forma nos logueamos en azure y así podremos trabajar con azure en el terminal.
Lo siguiente que hacemos es ver las imágenes que se pueden instalar y a solicitar la información sobre la que vamos a instalar(Ubuntu):
```
azure vm image list
azure vm image show b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140414-en-us-30GB
```
![listandoUbu](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/Captura%20de%20pantalla%20de%202016-02-02%20213151_zps1p2law7x.png)

Lo siguiente es crear la máquina virtual con el siguiente comando:
```
azure vm create ubuntu-pgazquez b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140414-en-us-30GB pgazquez contraseña --location "North Europe" --ssh
```
Creando la máquina:
![creandoUbuntuAzure](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/instalandoUbuntu_zpsnjhtla2l.png)

Lo siguiente que hacemos es arrancar la máquina y conectar con ella por ssh:
```
azure vm start ubuntu-pgazquez
ssh pgazquez@ubuntu-pgazquez.cloudapp.net
```
![sshAzure](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/Captura%20de%20pantalla%20de%202016-02-02%20215148_zpshiqaag8u.png)

Ahora dentro de la máquina actualizamos el sistema e instalamos nginx:
```
sudo apt-get update
sudo apt-get install nginx
```
Y seguidamente arrancamos nginx en la máquina virtual:

![nginxstatus](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/Captura%20de%20pantalla%20de%202016-02-02%20221644_zps5aqozcan.png)

La arrancamos desde el anfitrión y abrimos el puerto 80 de la máquina con el comando:
```
azure vm endpoint create ubuntu-pgazquez 80 80
```
![abriendoports](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/Captura%20de%20pantalla%20de%202016-02-02%20221712_zpswwi5ules.png)

Introducimos en el navegador nuestro nombre de dominio y comprobamos que todo funciona correctamente:
![welcometonginx](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/Captura%20de%20pantalla%20de%202016-02-02%20221746_zpsh7lioy9l.png)
Para acabar apagamos la máquina usando **azure vm shutdown ubuntu-pgazquez**
![azureshutdown](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/Captura%20de%20pantalla%20de%202016-02-02%20221916_zpssfxqv1az.png)
