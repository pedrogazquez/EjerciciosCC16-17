##Ejercicios 1: Instalar chef en la máquina virtual que vayamos a usar
He instalado chef con el comando:
```
curl -L https://www.opscode.com/chef/install.sh | sudo bash
```
Aquí podemos ver el chef instalado correctamente mediante el comando chef-solo -v:

![chefinstalado](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/Captura%20de%20pantalla%20de%202016-02-03%20122606_zpse0mbwj3e.png)

##Ejercicios 2: Crear una receta para instalar nginx, tu editor favorito y algún directorio y fichero que uses de forma habitual.
Lo primero que he hecho ha sido crear los directorios donde irán las recetas de chef. Uno para nginx y otro para nano que es mi editor. 
```
mkdir -p chef/cookbooks/nginx/recipes
mkdir -p chef/cookbooks/nano/recipes

```
Lo siguiente será configurar los ficheros de las recetas de nginx y de nano. El nombre del fichero será "default.rb" y habrá uno en cada directorio de cada aplicación.
**chef/cookbooks/nginx/recipes/default.rb:**
```
package 'nginx'
directory "/home/pgazquez/Documentos/nginx"
file "/home/pgazquez/Documentos/nginx/LEEME" do
    owner "pgazquez"
    group "pgazquez"
    mode 00544
    action :create
    content "Directorio para nginx"
end
```

**chef/cookbooks/nano/recipes/default.rb:**
```
package 'nano'
directory "/home/pgazquez/Documentos/nano"
file "/home/pgazquez/Documentos/nano/LEEME" do
    owner "pgazquez"
    group "pgazquez"
    mode 00544
    action :create
    content "Directorio para nano"
end

```
Si no tenemos la carpeta Documentos la creamos. Lo siguiente es definir el archivo node.json que irá en el directorio chef/ y que tendrá la lista de recetas a ejecutar-

**chef/node.json**
```
{
    "run_list":["recipe[nginx]", "recipe[nano]"]
}

```
El último fichero que necesitamos para este ejercicio es el fichero de configuración "solo.rb" que irá también en el directorio chef y que incluirá las referencias pertinentes a los ficheros creados anteriormente y el directorio que los contiene.

**chef/solo.rb*
```
file_cache_path "/home/pgazquez/chef" 
cookbook_path "/home/pgazquez/chef/cookbooks" 
json_attribs "/home/pgazquez/chef/node.json"

```

En esta imagen vemos como queda el árbol del directorio chef con la orden tree:

![treechef](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/Captura%20de%20pantalla%20de%202016-02-03%20130524_zpsdbwbnqqn.png)

Ahora queda instalar los paquetes con la orden **sudo chef-solo -c chef/solo.rb** y comprobar que todo se ha instalado correctamente:

![todocorrecchefsolo](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/Captura%20de%20pantalla%20de%202016-02-03%20130540_zps2zhfjqtu.png)

Y ha instalado todo correctamente como podemos ver a continuación:

![cheftodocorre](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/Captura%20de%20pantalla%20de%202016-02-03%20130646_zpsl57y3vga.png)


##Ejercicios 3: Escribir en YAML la siguiente estructura de datos en JSON
```
{ uno: "dos",
  tres: [ 4, 5, "Seis", { siete: 8, nueve: [ 10, 11 ] } ] } 

```

YAML y JSON son estándares de representaciones de datos como XML, que hacen más fácil el intercambio de información entre servicios y aplicaciones. Aquí la estructura en YAML:

```
---
    uno: "dos"
    tres: 
        - 4
        - 5
        - "Seis"
        - 
            siete: 8
            nueve: 
                - 10
                - 11
```


##Ejercicios 4: 1.Desplegar la aplicación realizada con todos los módulos necesarios usando un playbook de Ansible.
Lo primero que hacemos es añadir lo siguiente en el archivo **ansible_hosts**:

![archivoansihosts](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/Captura%20de%20pantalla%20de%202016-02-04%20194256_zps8o5b7ync.png)

Luego definimos el archivo .yml que en mi caso se llama **scriptansible.yml** y contiene lo siguiente:
```
- hosts: azure
  sudo: yes
  remote_user: pgazquez
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
    command: nohup sudo python appBares/manage.py runserver 0.0.0.0:5050

```
Y lo he ejecutado con la orden **ansible-playbook -u pgazquez scriptansible.yml** y este ha sido el resultado:

![contenidoplaybook-ansible](http://i1042.photobucket.com/albums/b422/Pedro_Gazquez_Navarrete/Captura%20de%20pantalla%20de%202016-02-04%20230742_zpslrk2dbxe.png)
