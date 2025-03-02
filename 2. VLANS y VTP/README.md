# **Manual de Configuración de VLANs y VTP en Switches Cisco**

## **1. Introducción**

Las VLANs (Virtual Local Area Networks) permiten segmentar una red en múltiples dominios de broadcast, mejorando el rendimiento y la seguridad.
El protocolo VTP (VLAN Trunking Protocol) ayuda a gestionar VLANs automáticamente en una red con múltiples switches Cisco.

## **2. Conceptos Básicos**

### **2.1 VLANs**

- Permiten dividir una red física en múltiples redes lógicas.
- Reducen el tráfico de broadcast.
- Mejoran la seguridad al aislar dispositivos.

### **2.2 VTP (VLAN Trunking Protocol)**

- Sincroniza la información de VLANs entre switches en el mismo dominio VTP.
- Tiene tres modos:
  - **Servidor**: Puede crear, modificar y eliminar VLANs.
  - **Cliente**: Recibe la configuración de VLANs pero no puede modificarla (Debe estar en el mismo dominio y contraseña del server).
  - **Transparente**: No participa en VTP pero permite el paso de información.

### **2.3 Enlaces de Acceso y Enlaces Troncales**

- **Enlace de acceso:** Conecta un solo dispositivo o puerto a una VLAN específica. Solo transmite tráfico de una VLAN.
  - Se configura con:

    ```bash
    switchport mode access
    switchport access vlan <ID_VLAN>
    ```

- **Enlace troncal:** Permite el tráfico de múltiples VLANs en un solo enlace entre switches.
  - Se configura con:

    ```bash
    switchport mode trunk
    switchport trunk allowed vlan <lista_VLANs>
    ```

## **3. Ejemplo de Configuración**

### **3.1. Topología**

La topología que vamos a configurar cuenta con:

- Dos VLANs:
  - **VLAN 10** (ADMON) → Red 192.168.10.0/24
  - **VLAN 20** (RRHH) → Red 192.168.20.0/24
- Tres switches:
  - **Servidor (Switch Root con VTP Server)**
  - **Cliente01 (Switch en modo Cliente VTP)**
  - **Cliente02 (Switch en modo Cliente VTP)**
- Cuatro computadoras asignadas a VLANs.

![TopologiaVLANSVTP](/assets/topologia_vlan_vtp.png)

### **3.2. Configuración de VLANs en el Switch Servidor**

```bash
# Entrar al modo de configuración global
enable
configure terminal

# Configurar el dominio VTP y la contraseña
vtp domain usac
vtp password 1234
vtp mode server
vtp version 2

# Crear VLANs
vlan 10
 name ADMINISTRACION
exit
vlan 20
 name RRHH
exit

# Configurar interfaces trunk
interface FastEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20
exit

interface FastEthernet0/2
 switchport mode trunk
 switchport trunk allowed vlan 10,20
exit
```

### **3.3. Configuración de los Switches Cliente 01 y 02**

```bash
# Entrar al modo de configuración global
enable
configure terminal

# Configurar el dominio VTP y la contraseña (deben ser iguales al Switch Server)
vtp domain usac
vtp password 1234
vtp mode client
vtp version 2
```

Los switches clientes recibirán automáticamente las VLANs desde el switch Servidor.

### **3.4. Configuración de los Enlaces Trunk y acceso en Switches Cliente**

Para permitir el tráfico de múltiples VLANs entre los switches, configuramos los enlaces trunk y las interfaces de acceso.

```bash
# Cliente01
interface FastEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20
exit

interface FastEthernet0/2
 switchport mode trunk
 switchport trunk allowed vlan 10,20
exit

interface FastEthernet0/11
 switchport mode access
 switchport access vlan 10
exit

interface FastEthernet0/12
 switchport mode access
 switchport access vlan 20
exit

# Cliente02
interface FastEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20
exit

interface FastEthernet0/2
 switchport mode trunk
 switchport trunk allowed vlan 10,20
exit

interface FastEthernet0/11
 switchport mode access
 switchport access vlan 10
exit

interface FastEthernet0/12
 switchport mode access
 switchport access vlan 20
exit
```

### 3.5. Verificación de la Configuración

Verificar las VLANs creadas en todos los switches:

```bash
show vlan brief
```

Verificar el estado del VTP:

```bash
show vtp status
```

Verificar los puertos en modo trunk:

```bash
show interfaces trunk
```

---

📌 **Nota:** Con estas configuraciones solamente los dispositivos que esten en la misma VLAN se podrán comunicar entre si.