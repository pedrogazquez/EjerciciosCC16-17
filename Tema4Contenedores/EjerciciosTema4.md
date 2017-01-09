##Ejercicios 1: Instala LXC en tu versión de Linux favorita. Normalmente la versión en desarrollo, disponible tanto en GitHub como en el sitio web está bastante más avanzada; para evitar problemas sobre todo con las herramientas que vamos a ver más adelante, conviene que te instales la última versión y si es posible una igual o mayor a la 1.0.
He instalado LXC y como se puede comprobar en la siguiente captura la versión que he instalado ha sido 1.0.6:
![lxc](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/Captura%20de%20pantalla%20de%202015-11-27%20104136_zpskoyc7qst.png)

##Ejercicios 2: Instalar una distro tal como Alpine y conectarse a ella usando el nombre de usuario y clave que indicará en su creación.

He instalado Alpine con el siguiente comando:

```
sudo lxc-create --name alpine-edge -t alpine -- --release edge
```

![imagenContainer](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/alpine_zps4wtkjg4i.png)

Y me he conectado a el con:

```
lxc-attach -n alpine-edge
```

##Ejercicios 3: Provisionar un contenedor LXC usando Ansible o alguna otra herramienta de configuración que ya se haya usado



##Ejercicios 4: Instalar una imagen alternativa de Ubuntu y alguna adicional, por ejemplo de CentOS.

He instalado ubuntu a partir de docker con **sudo docker pull ubuntu**:
![dockUb](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/Captura%20de%20pantalla%20de%202015-12-05%20133647_zpshhqmufwo.png)

Y he hecho lo mismo para centos usando **sudo docker pull centos**

He buscado y he encontrado [aquí](https://github.com/dockerfile/mongodb): lo he instalado con **sudo docker pull library/mongo**.
Y aquí se puede ver que la imagen se ha instalado correctamente: 

![mongodb](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/Captura%20de%20pantalla%20de%202015-12-08%20192716_zpszpdzsrh9.png)


##Ejercicios 5: Crear a partir del contenedor anterior una imagen persistente con commit.

#Ejercicios 6: Reproducir los contenedores creados anteriormente usando un Dockerfile.

Para ello he hecho uso del siguiente Dockerfile:

```
FROM ubuntu:latest

MAINTAINER Pedro Gazquez Navarrete <pedrogazqueznavarrete@gmail.com>


# Añadiendo herramientas Python
RUN apt-get -y update
RUN apt-get -y install python-setuptools
RUN apt-get -y install python-dev
RUN apt-get -y install python-pip
RUN apt-get -y install supervisor

#Instalando la API de BOTS de Telegram
RUN pip install pyTelegramBotAPI

#Añadiendo usuario
RUN useradd -m docker && echo "docker:docker" 

USER docker
CMD /bin/bash
```

Lo siguiente ha sido ejecutar el comando:

```
sudo docker pull pedrogazquez/funnybot
```

Para crear el contenedor, este comando se ejcuta dentro del repositorio (que anteriormente hemos clonado):

![dockerPull](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/dockerpull_zpscowuxd08.png)

Ahora arrancaremos el contenedor con el comando:

```
sudo docker run -it pedrogazquez/funnybot bash
```

Y como podemos ver a continuación se arranca correctamente y tiene instalado todo lo defnido en el Dockerfile:

![dockerRun](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/dcokerrun_zpsguihjgxb.png)
