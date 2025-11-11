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

