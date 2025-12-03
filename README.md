#  Implementación de Firewall y Reglas de Seguridad en OpenStack (MicroStack)

**Autor:** José Torres  
**Curso:** CONFIGURACION DE REDES – Grupo 9  
**Tema:** Implementación de firewall virtual y reglas de seguridad en entorno OpenStack.

---

#  1. Introducción

En los entornos de computación en la nube, la seguridad perimetral y la segmentación de red son elementos fundamentales para garantizar que las máquinas virtuales permanezcan protegidas contra accesos no autorizados.  
OpenStack, a través del componente Neutron, proporciona *Security Groups*, un mecanismo de firewall virtual que filtra el tráfico de red de cada instancia.

Este laboratorio implementa un entorno completo usando Ubuntu Server + MicroStack, donde se configura una topología de red, se crea un firewall personalizado y se validan reglas de seguridad con pruebas reales.

---

#  2. Conceptos Fundamentales de Seguridad en Redes

## 🔒 Seguridad de red
Conjunto de técnicas, políticas y mecanismos diseñados para proteger:
- **Confidencialidad**
- **Integridad**
- **Disponibilidad**

Incluye filtrado, segmentación, inspección de tráfico, control de acceso y límites perimetrales.

##  Firewall
Un firewall es un sistema que **supervisa, filtra y controla** el tráfico entre redes según reglas predefinidas.

### Funciones principales:
- Prevenir accesos no autorizados
- Controlar servicios expuestos
- Proteger redes internas
- Segmentar zonas por nivel de confianza

---

#  3. Tipos de Firewalls

### 1️⃣ Filtrado de paquetes (L3/L4)
Analiza IP, puertos y protocolos.  
Usado en routers, switches y firewalls tradicionales.

### 2️⃣ Firewall con inspección de estado (Stateful)
Registra conexiones y permite respuestas únicamente si salieron desde la red interna.

### 3️⃣ Firewall de aplicación (L7)
Analiza contenido HTTP, DNS, SMTP, etc.

### 4️⃣ Firewalls virtuales en la nube
No existen físicamente; controlan tráfico entre máquinas virtuales.  
Ejemplo: Security Groups en OpenStack.

---

#  4. Security Groups en OpenStack

Un **Security Group** es un firewall virtual administrado por Neutron que actúa directamente sobre las interfaces de las instancias.

Características:
- Regula tráfico **ingress** (entrante) y **egress** (saliente).
- Por defecto: bloquea ingress y permite egress.
- Es *stateful*: si una instancia inicia una conexión saliente, la respuesta es permitida.
- Puede aplicarse a una o varias instancias.

Esto permite crear máquinas completamente aisladas del exterior.

---

#  5. Topologías de Red Usadas en el Proyecto

<img width="505" height="338" alt="Captura de pantalla 2025-12-02 143410" src="https://github.com/user-attachments/assets/f8a87d5d-651b-4e4d-b5ad-ddf2c3583c8c" />


#  6. Escenarios de Aplicación de Firewalls

**Escenario 1: Servidor aislado (como tu VM)**

Regla:

❌ Bloquear ICMP entrante

❌ Bloquear SSH entrante

✔ Permitir salida a Internet

Aplicación:

Servidor protegido para pruebas.

**Escenario 2: Servidor web público**

Reglas:

✔ Permitir HTTP (80)

✔ Permitir HTTPS (443)

❌ Bloquear SSH, ICMP, puertos internos

**Escenario 3: Red privada con salt-server**

Reglas:

SSH solo desde IP del administrador

Todo lo demás bloqueado.

#  7. Reglas a implementar
- Ingress (entrante): Bloqueado: todo
- Egress (saliente): Permitido: todo
Resultado:

Desde Windows → VM = bloqueado ✔

Desde VM → Internet = permitido ✔


##  OBJETIVO DE LA GUIA
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


## Conclusión General

 ### El proyecto implementó:
- Un firewall virtual efectivo
- Tráfico entrante completamente bloqueado
- Tráfico saliente permitido
- Instancia protegida mediante modelo stateful
- Arquitectura de red segmentada

Este tipo de configuración se utiliza en nubes privadas, producción y ambientes corporativos para proteger recursos internos.

## Link de video en youtube para ver mejora la teoria implementada en la practica: [Implementación de Firewall y reglas de seguridad Jose Torres](https://youtu.be/NRXGVJCAG2A)
<iframe width="560" height="315" src="https://www.youtube.com/embed/b1k_i0YLNWA?si=IR7-jkfeoIP77yJ5" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
## Documentacion adicional y explicacion teorica:
**Vista Previa**
<img width="1493" height="792" alt="Captura de pantalla 2025-12-02 153405" src="https://github.com/user-attachments/assets/a5f88db1-711d-43a7-9837-91b4ff14458a" />
[Ver documento completo](https://drive.google.com/file/d/16AMy5QHyGY919uOBYV4MaqqR2bfjuqRe/view?usp=sharing) 



