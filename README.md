# Implementacion-de-Firewall-y-reglas-de-seguridad

## Integrantes: 
- **Jose Torres**

## 🧩 OBJETIVO
🔰 Instalar un entorno mínimo con OpenStack (Microstack) en Ubuntu dentro de VirtualBox
🔰 Crear una instancia (máquina virtual dentro de OpenStack)
🔰 Configurar y probar reglas de firewall (grupos de seguridad)

🧱 PASO 1. Preparar el entorno
🔸 1. Instalar Ubuntu Server en VirtualBox

**Requisitos mínimos:**
Procesador: 4 núcleos
RAM: 8 GB mínimo
Disco duro: 50 GB
Conexión a internet activa

Pasos:
Descarga Ubuntu Server desde aquí 👉
🔗 https://ubuntu.com/download/server

Abre VirtualBox → Nueva máquina
Nombre: UbuntuServer
Tipo: Linux
Versión: Ubuntu (64-bit)
Asigna: RAM 4096 MB o más
Procesadores: 2 o más
Disco duro: 40 GB (VDI, dinámico)
Monta la ISO descargada en “Almacenamiento → Controlador IDE → Elegir un archivo de disco”.
En “Red”, selecciona Adaptador puente (Bridge Adapter) para que tenga acceso a internet.
Inicia la máquina y sigue el asistente de instalación de Ubuntu:
Idioma: Español
Nombre del servidor: openstack(de cualquier nombre)
Usuario: admin(o el usuario de preferencia)
Contraseña: (algo corto)

https://github.com/user-attachments/assets/563c636c-0961-4f48-a650-70b97006bf13


🌐 PASO 2. Configurar IP fija en Ubuntu
Objetivo

Asegurar una IP estable para acceder al panel de OpenStack desde Windows.

Acciones

Dentro de Ubuntu, ejecutar:

ip a


Para identificar los nombres de las interfaces (enp0s3, enp0s8, etc.)

Editar el archivo de configuración:

sudo nano /etc/netplan/00-installer-config.yaml


Configurar la IP fija en enp0s8 (red interna):

network:
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8:
      dhcp4: no
      addresses:
        - 192.168.100.10/24
      nameservers:
        addresses: [8.8.8.8, 1.1.1.1]
  version: 2


Aplicar los cambios:

sudo netplan apply


Confirmar:

ip a s enp0s8


https://github.com/user-attachments/assets/df365e91-b014-4209-8b77-b149e392f245



→ Debe mostrar 192.168.100.10/24

En Windows, asignar IP al adaptador “VirtualBox Host-Only Network”:

IP: 192.168.100.20
Máscara: 255.255.255.0
DNS: 8.8.8.8


Probar conexión desde Windows:

ping 192.168.100.10


Si responde, tienes red interna activa.

##  Paso 3. ENTRAR por PuTTY a tu máquina Ubuntu
Abre PuTTY:

Host Name: 192.168.100.10

Port: 22

Connection type: SSH

Clic en Open.


https://github.com/user-attachments/assets/21258a8e-e6d0-48ac-a39f-e705c0f1e8b3

## Paso 4. Instalar Microstack desde putty

#### (si no eres root)
sudo -i

#### actualizar sistema
apt update && apt upgrade -y

#### instalar snapd si no está
apt install -y snapd

#### instalar microstack (usa --beta, recomendado para lab)
snap install microstack --beta

#### inicializar microstack (esto puede tardar 10–60 min)
#### (no interrumpas; espera a que termine)
sudo microstack init --auto --control

## Paso 5. Acceder a OpenStack (Dashboard)
Abrir el navegador en Windows: https://192.168.100.10

## Paso 6. Crear Security Group Protegido
En el OpenStack:
Ir a Project → Network → Security Groups → Create Security Group:
- Nombre: firewall-jtorres
- Editar reglas → Eliminar todas las reglas de Ingress. Mantener solo Egress (salida). Esto bloquea toda entrada pero permite salida a Internet.

## Paso 7. Crear Red Interna

Project → Network → Networks → Create Network

Nombre: red-interna

Subred: 192.168.50.0/24

## Paso 8. Crear la instancia protegida

Project → Compute → Instances → Launch Instance

Configuración:
- Name: vm-jtorres
- Image: cirros
- Flavor: m1.tiny
- Network: red-interna
- Security Group: firewall-jtorres
- Keypair: crear myykey

Verificar desde CLI: sudo microstack.openstack server list
Ejemplo de IP asignada: red-interna=192.168.50.168

## Paso 9. Pruebas de Firewall
❌ 1. Ping desde Windows → VM : ping 192.168.50.168
Resultado esperado: Host inaccesible
❌ 2. SSH desde Windows → VM: ssh cirros@192.168.50.168
Resultado esperado: Connection timed out
✔ 3. Ping desde VM → Internet. En consola de Cirros: ping 8.8.8.8 
Resultado esperado: Exitoso (egress permitido)


## CONCLUSIÓN

Se implementó un entorno mínimo de OpenStack con MicroStack.

Se configuró una red interna segura.

Se creó un Security Group que bloquea todo el tráfico entrante.

La instancia vm-jtorres quedó totalmente protegida.

Todas las pruebas de seguridad fueron exitosas.
