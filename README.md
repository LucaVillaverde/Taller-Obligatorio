# Documentación/Pasos
## ❗ Requisitos
### 💻 Equipos
        Todos los sistemas operativos sin interfaz gráfica.
        Tres equipos, uno con ansible en ubuntu server, otro ubuntu server que sera el DBSrv y,
        un último con CentOS Stream 9 que sera el APPSrv.
        En nuestro caso levantados como maquinas virtuales pero extrapolable para entornos reales.
        **Para las maquinas virtuales** Dos adaptadores de red por maquina, NAT y Red Interna.
        -----------------------------------------------
        |    Maquina     |  Dirección IP Red Interna   |
        |----------------|-----------------------------|
        | 🚀 Ansible     |      192.168.1.10/24        |
        | 🗄️ DBSrv       |      192.168.1.11/24        |
        | 🖥️ APPSrv      |      192.168.1.20/24        |
        ------------------------------------------------
        Contar con el paquete openssh-server en appsrv y dbsrv.
        Instalacion Ubuntu: "sudo apt install openssh-server".
        Instalación CentOS: "sudo dnf install openssh-server".
        Procure reemplazar [usuario] en cada instancia mencionada por el nombre de la cuenta de usuario
        usada en los equipos correspondientes.
## ⚙️ Configuración
### 🚀 Ansible
        1- ⚙️ Configuración de adaptador Red
        ❗Este paso te lo puedes saltar si tu maquina Ansible tiene ip asignada en la Red Interna❗
            1. Dentro de la maquina 🚀Ansible ejecutamos "sudo touch /etc/netplan/01-netcfg.yaml"
            2. Lo editamos con "sudo nano /etc/netplan/01-netcfg.yaml" y colocamos lo siguiente adentro:
        ❗Importante respetar la identación❗
            network:
                version: 2
                renderer: networkd
                ethernets:
                    enp0s8:
                        dhcp4: no
                        addresses:
                            - 192.168.1.10/24
            3. Ejecutar "sudo netplan apply" para aplicar la configuracion del archivo.
            4. Comprobar con "ip a" si el adaptador enp0s8 tiene ip asignada "192.168.1.10"

        2- 🔑 Generación de Llave SSH
            1. Ejecutar "ssh-keygen -ted25519".
            2. Si se desea coloque alguna frase de seguridad, en nuestro caso no lo hicimos.
### 🗄️ DBSrv
        1- ⚙️ Configuración de adaptador Red
        ❗Este paso te lo puedes saltar si tu maquina DBSrv tiene ip asignada en la Red Interna❗
            1. Dentro de la maquina 🗄️DBSrv ejecutamos "sudo touch /etc/netplan/01-netcfg.yaml"
            2. Lo editamos con "sudo nano /etc/netplan/01-netcfg.yaml" y colocamos lo siguiente adentro:
        ❗Importante respetar la identación❗
            network:
                version: 2
                renderer: networkd
                ethernets:
                    enp0s8:
                        dhcp4: no
                        addresses:
                            - 192.168.1.11/24
            3. Ejecutar "sudo netplan apply" para aplicar la configuracion del archivo.
            4. Comprobar con "ip a" si el adaptador enp0s8 tiene ip asignada "192.168.1.11".

        2- ⚙️ Configuración para Acceso con llave SSH
            1. Desde 🚀Ansible ejecutar "ssh-copy-id [usuario]@192.168.1.11".
            2. Colocar la contraseña del [usuario] cuando se solicite.
            3. Comprobar conexión ssh con "ssh [usuario]@192.168.1.11"

        3- 👑 Ejecución de comandos root sin contraseña
            1. Desde 🗄️DBSrv ejecutar "sudo visudo".
            2. Buscar "%sudo ALL=(ALL:ALL) ALL" y dejarlo así: "%sudo ALL=(ALL:ALL) NOPASSWD:ALL"
            3. Para nano, guardar con "Ctrl + O" y salir con "Ctrl + X".
            4. Para vi, guardar y salir con la siguiente secuencia:
                a. Dos puntos ":" y wq
### 🖥️ APPSrv
        1- ⚙️ Configuración de adaptador Red
        ❗Este paso te lo puedes saltar si tu maquina APPSrv tiene ip asignada en la Red Interna❗
            1. Dentro de la maquina 🖥️APPSrv ejecutamos "sudo touch /etc/netplan/01-netcfg.yaml"
            2. Lo editamos con "sudo nano /etc/netplan/01-netcfg.yaml" y colocamos lo siguiente adentro:
        ❗Importante respetar la identación❗
            network:
                version: 2
                renderer: networkd
                ethernets:
                    enp0s8:
                        dhcp4: no
                        addresses:
                            - 192.168.1.20/24
            3. Ejecutar "sudo netplan apply" para aplicar la configuracion del archivo.
            4. Comprobar con "ip a" si el adaptador enp0s8 tiene ip asignada "192.168.1.20".

        2- ⚙️ Configuración para Acceso con llave SSH
            1. Desde 🚀Ansible ejecutar "ssh-copy-id [usuario]@192.168.1.11".
            2. Colocar la contraseña del [usuario] cuando se solicite.
            3. Comprobar conexión ssh con "ssh [usuario]@192.168.1.20"
        
        3- 👑 Ejecución de comandos root sin contraseña
            1. Desde 🖥️APPSrv ejecutar "sudo visudo".
            2. Buscar "#%wheel ALL=(ALL) NOPASSWD: ALL" y dejarlo así: "%wheel ALL=(ALL) NOPASSWD: ALL"
            3. Para nano, guardar con "Ctrl + O" y salir con "Ctrl + X".
            4. Para vi, guardar y salir con la siguiente secuencia:
                a. Dos puntos ":" y wq
## 🚀 Instalación
### 💻 Ansible
        1. Instalar ansible-core y git con "sudo apt install ansible-core -y" y "sudo apt install git -y"
        2. Clonar este repositorio con "git clone https://github.com/LucaVillaverde/Taller-Obligatorio.git"
        3. Moverse al directorio con "cd Taller-Obligatorio"
        4. Ejecutar "ansible-galaxy collection install -r requirements.yml"
## ▶️ Ejecución
        1. En el equipo 💻 Ansible en la carpeta del repositorio.
           Ejecutar "ansible-playbook -i hosts.ini site.yml"
        2. Cuando termine comprobar manualmente el funcionamiento con el siguiente comando:
            "curl http://192.168.1.20/"
        Referencia de salida:
            HTTP/1.1 200 OK
            Date: Sun, 09 Aug 2026 18:27:47 GMT
            Server: Apache/2.4.62 (CentOS Stream)
            X-Powered-By: PHP/8.0.30
            Transfer-Encoding: chunked
            Content-Type: text/html; charset=UTF-8

            <h1>Lista de Cumpleaños</h1><table border='1'><tr><th>Nombre</th><th>Fecha</th></tr><tr><td>Frodo Baggins</td><td>2005-01-14</td></tr><tr><td>Aragorn</td><td>2004-02-09</td></tr><tr><td>Arwen Undomiel</td><td>1994-12-09</td></tr></table>⏎
## ❗Importante
        Por defecto, el proyecto está configurado para utilizar las credenciales definidas en /group_vars/all.yml.

        Estas credenciales son únicamente de ejemplo y están destinadas al entorno de laboratorio.
        Se recomienda reemplazarlas por credenciales propias y, en un entorno real,
        almacenarlas mediante Ansible Vault u otro mecanismo seguro para evitar que queden almacenadas en texto plano.  