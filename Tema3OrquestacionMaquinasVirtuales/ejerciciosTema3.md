##Ejercicios 6: Instalar una máquina virtual Debian usando Vagrant y conectar con ella.
Para realizar este ejercicio lo primero que he hecho ha sido instalar vagrant:
```
sudo apt-get install vagrant
```
Descargamos el archivo [vagrant_1.8.1_x86_64.deb](https://releases.hashicorp.com/vagrant/1.8.1/) para una versión 5.0.x de VirtualBox y lo instalamos:
```
sudo dpkg -i vagrant_1.8.1_x86_64.deb
```
Lo siguiente es descargar la imagen Debian como se ha explicado:
```
vagrant box add debian https://github.com/holms/vagrant-jessie-box/releases/download/Jessie-v0.1/Debian-jessie-amd64-netboot.box
```

![vagrantbox](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/Captura%20de%20pantalla%20de%202016-02-04%20234853_zpsxlqy3ra5.png)

Creamos el fichero Vagrantfile:
```
vagrant init debian
```
Y arrancamos la máquina:
```
vagrant up
```

![vagrantup](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/Captura%20de%20pantalla%20de%202016-02-04%20235142_zpshy94hyui.png)

Y para terminar conectamos con ella por SSH:
```
vagrant ssh
```

![vagrantssh](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/Captura%20de%20pantalla%20de%202016-02-04%20235209_zpsld4tyu1r.png)

##Ejercicios 7: Crear un script para provisionar `nginx` o cualquier otro servidor web que pueda ser útil para alguna otra práctica
He añadido en el fichero **Vagrantfile** lo siguiente:
```
config.vm.provision "shell",
	inline: "sudo apt-get update && sudo apt-get install -y nginx && sudo service nginx start"
```
Arrancamos la máquina con vagrant up y posteriormente ejecutamos la provisión con **vagrant provision**:

![vagrantprovision](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/Captura%20de%20pantalla%20de%202016-02-05%20101153_zpswvaodxir.png)

Conectamos por ssh como antes (**vagrant ssh**) y luego comprobamos que nginx se ha instalado y está funcionando:
```
sudo service nginx status
```

![nginxvagrant](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/Captura%20de%20pantalla%20de%202016-02-05%20101250_zps1hepxrat.png)

##Ejercicios 8: Configurar tu máquina virtual usando vagrant con el provisionador ansible

Lo primero que hacemos es instalar el provisionador de azure para Vagrant:
```
vagrant plugin install vagrant-azure
```
![pluginazure](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/Captura%20de%20pantalla%20de%202016-02-07%20220453_zpsggqexnuv.png)
Después de esto nos logueamos en azure como anteriormente (azure login) y hacemos **azure account doownload**  esto nos proporciona un enlace al que deberemos acceder para descargar nuestros credenciales y una vez descargados hacemos:
```
azure account import Azure-2-5-2016-credentials.publishsettings
```
Lo siguiente que hacemos es generar los certificados que se subirán a Azure para perminirnos realizar las conexiones con nuestra máquina:
```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout azurevagrant.key -out azurevagrant.key
chmod 600 azurevagrant.key
openssl x509 -inform pem -in azurevagrant.key -outform der -out azurevagrant.cer
```
Ahora subimos el fichero **azurevagrant.cer** a Azure en el apartado Management Certificates:
![managcert](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/Captura%20de%20pantalla%20de%202016-02-05%20164823_zpskywjh3fa.png)

Para autenticar Azure desde Vagranfile creamos un archivo .pem y lo concatemos el .key:
```
openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out azurevagrant.pem
cat azurevagrant.key > azurevagrant.pem
```
Luego creamos el fichero Vagranfile:
```
VAGRANTFILE_API_VERSION = '2'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = 'azure'
  config.vm.network "public_network"
  config.vm.network "private_network",ip: "192.168.56.10", virtualbox__intnet: "vboxnet0"
  config.vm.network "forwarded_port", guest: 80, host: 80
  config.vm.define "localhost" do |l|
    l.vm.hostname = "localhost"
  end

  config.vm.provider :azure do |azure|
    azure.mgmt_certificate = File.expand_path('/home/pgazquez/Escritorio/appdefinitiva/azurevagrant.pem')
    azure.mgmt_endpoint = 'https://management.core.windows.net'
    azure.subscription_id = '686945bb-88d5-4919-bd0d-d2e0aeb76c29'
    azure.vm_image = 'b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20151218-en-us-30GB'
    azure.vm_name = 'baresquesada'
    azure.vm_user = 'vagrant'
    azure.vm_password = 'Pedro#1992'
    azure.vm_location = 'Central US' 
    azure.ssh_port = '22'
    azure.tcp_endpoints = '80:80'
  end

  config.vm.provision "ansible" do |ansible|
    ansible.sudo = true
    ansible.playbook = "ansiblebares.yml"
    ansible.verbose = "v"
    ansible.host_key_checking = false 
  end
end
```
En este Vagranfile en el primer bloque indicamos la imagen de la caja, el id de subscripción de azure, el usario el password, el puerto SSH, etc. En el segundo bloque se definen las características que tendrá nuestra máquina y por último en el tercero indicar que provisione la máquina mediante ansible y el nombre del fichero yml (ansiblebares.yml en mi caso).
Ahora definimos la variable de entorno ANSIBLE_HOSTS:
```
export ANSIBLE_HOSTS=~/Escritorio/VagrantIV/ansible_hosts
```
Ahora añadimos lo siguiente en el archivo ansible_host:
```
[localhost]
192.168.56.10
```
Y definimos el archivo ansiblebares.yml:
```
- hosts: azure
  sudo: yes
  remote_user: vagrant
  tasks:
  - name: Instalar paquetes 
    apt: name=python-setuptools state=present
    apt: name=build-essential state=present
    apt: name=python-dev state=present
    apt: name=git state=present
  - name: Descargar aplicacion de GitHub
    git: repo=https://github.com/pedrogazquez/appBares.git dest=appBares clone=yes force=yes
  - name: Permisos de ejecucion
    command: chmod -R +x appBares
  - name: Instalar requisitos
    command: sudo pip install -r appBares/requirements.txt
  - name: ejecutar
    command: nohup sudo python appBares/manage.py runserver 0.0.0.0:80
```

Este instala los paquetes necesarios para ejecutar nuestra app después de descargar el repo de esta.
Ahora descargamos la box de azure con el siguiente comando:
```
vagrant box add azure https://github.com/msopentech/vagrant-azure/raw/master/dummy.box

```
![dummybox](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/Captura%20de%20pantalla%20de%202016-02-05%20165928_zps2kqtfjqy.png)

Y ahora podemos proceder a ejecutar provider para que cree la app con el siguiente comando:
```
vagrant up --provider=azure
vagrant provision (si la máquina ya está creada)
```
Y una vez ejecutado se puede acceder a [nuestra app de azure](http://baresquesada.cloudapp.net/rango/)
