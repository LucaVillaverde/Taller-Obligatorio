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
        | 🚀 ansible     |      192.168.1.10/24        |
        | 🖥️ appsrv      |      192.168.1.11/24        |
        | 🗄️ dbsrv       |      192.168.1.20/24        |
        ------------------------------------------------
        Contar con el paquete openssh-server en appsrv y dbsrv.
        Instalacion Ubuntu: "sudo apt install openssh-server".
        Instalación CentOS: "sudo dnf install openssh-server".
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

        2- ⚙️ Configuración para Acceso Root
            1. En 🗄️DBSrv Ejecutar "sudo nano /etc/ssh/sshd_config" para editar el archivo de configuración.
            2. Ir al final y colocar lo siguiente:
                PermitRootLogin yes
                PubkeyAuthentication yes
            3. Guardar con Ctrl + O y salir con Ctrl + X.
            4. Ejecutar "sudo systemctl restart ssh" y "sudo systemctl restart sshd"
            5. Asignar contraseña al usuario root con "sudo passwd root"
            6. Desde el equipo 🚀Ansible ejecutar "ssh-copy-id root@192.168.1.11"
            7. Colocar la contraseña del usuario root cuando se solicite.
            8. Probar a usar "ssh root@192.168.1.11" para comprobar el funcionamiento.
            9. En 🗄️DBSrv Ejecutar "sudo nano /etc/ssh/sshd_config" para editar el archivo de configuración.
            10. Ir al final y cambiar el valor "yes" de PermitRootLogin por "prohibit-password":
                PermitRootLogin prohibit-password
                PubkeyAuthentication yes
            11. Guardar con Ctrl + O y salir con Ctrl + X.
            12. Ejecutar "sudo systemctl restart ssh" y "sudo systemctl restart sshd"
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

        2- ⚙️ Configuración para Acceso Root
            1. En 🖥️APPSrv Ejecutar "sudo nano /etc/ssh/sshd_config" para editar el archivo de configuración.
            2. Ir al final y colocar lo siguiente:
                PermitRootLogin yes
                PubkeyAuthentication yes
            3. Guardar con Ctrl + O y salir con Ctrl + X.
            4. Ejecutar "sudo systemctl restart ssh" y "sudo systemctl restart sshd"
            5. Asignar contraseña al usuario root con "sudo passwd root"
            6. Desde el equipo 💻Ansible ejecutar "ssh-copy-id root@192.168.1.20"
            7. Colocar la contraseña del usuario root cuando se solicite.
            8. Probar a usar "ssh root@192.168.1.20" para comprobar el funcionamiento.
            9. En 🖥️DBSrv Ejecutar "sudo nano /etc/ssh/sshd_config" para editar el archivo de configuración.
            10. Ir al final y cambiar el valor "yes" de PermitRootLogin por "prohibit-password":
                PermitRootLogin prohibit-password
                PubkeyAuthentication yes
            11. Guardar con Ctrl + O y salir con Ctrl + X.
            12. Ejecutar "sudo systemctl restart ssh" y "sudo systemctl restart sshd"
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
            "curl -i http://192.168.1.20/"
        Referencia de salida:
            HTTP/1.1 200 OK
            Date: Sun, 09 Aug 2026 18:27:47 GMT
            Server: Apache/2.4.62 (CentOS Stream)
            X-Powered-By: PHP/8.0.30
            Transfer-Encoding: chunked
            Content-Type: text/html; charset=UTF-8

            <h1>Lista de Cumpleaños</h1><table border='1'><tr><th>Nombre</th><th>Fecha</th></tr><tr><td>Frodo Baggins</td><td>2005-01-14</td></tr><tr><td>Aragorn</td><td>2004-02-09</td></tr><tr><td>Arwen Undomiel</td><td>1994-12-09</td></tr></table>⏎     

## 📚 Documentación
## 🤝 Contribuciones
## 🛡️ Seguridad