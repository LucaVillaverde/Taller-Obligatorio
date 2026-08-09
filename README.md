# Documentación/Pasos
## ❗ Requisitos
### 💻 Equipos
        Todos los sistemas operativos sin interfaz gráfica.
        Tres equipos, uno con ansible en ubuntu server, otro ubuntu server que sera el DBSRV y,
        un último con CentOS Stream 9 que sera el APPSRV.
        En nuestro caso levantados como maquinas virtuales pero extrapolable para entornos reales.
        **Para las maquinas virtuales** Dos adaptadores de red por maquina, NAT y Red Interna.
        -----------------------------------------------
        |    Maquina     |  Dirección IP Red Interna   |
        |----------------|-----------------------------|
        | 🚀 ansible     |      192.168.1.10/24        |
        | 🐳 appsrv      |      192.168.1.11/24        |
        | 🗄️ dbsrv       |      192.168.1.20/24        |
        ------------------------------------------------
## ⚙️ Configuración
        **Este paso te lo puedes saltar si tu maquina ansible tiene ip asignada en la Red Interna**
        Dentro de la maquina ansible y, ejecutamos "sudo touch /etc/netplan/01-netcfg.yaml"
        Lo editamos con "sudo nano /etc/netplan/01-netcfg.yaml" y colocamos lo siguiente adentro:
        **Importante respetar la identación**
        network:
            version: 2
            renderer: networkd
            ethernets:
                enp0s8:
                    dhcp4: no
                    addresses:
                        - 192.168.1.10/24
## 🚀 Instalación
## 📚 Documentación
## 🤝 Contribuciones
## 🛡️ Seguridad