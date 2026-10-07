# 🏫 Infraestructura de Red Interconectada y Jerárquica para Centro Educativo (Sedes: Pelayo y Córcega)

Este repositorio contiene el diseño, despliegue físico y configuración lógica de la infraestructura de red para un centro educativo dividido en dos edificios (**Sede Pelayo** y **Sede Córcega**). El proyecto simula un entorno empresarial/académico real, aislando el tráfico por departamentos mediante VLANs, optimizando el enrutamiento inter-VLAN y securizando servicios críticos.

---

## 🗺️ Arquitectura y Direccionamiento Lógico

La red se ha segmentado geográficamente utilizando direccionamiento privado diferenciado para evitar colisiones y estructurar de forma eficiente las subredes del centro:

*   **Sede Pelayo:** Redes basadas en el espacio de direccionamiento Clase B (`172.16.0.0` hasta `172.25.0.0`).
*   **Sede Córcega:** Redes basadas en el espacio de direccionamiento Clase C (`192.168.0.0` hasta `192.168.6.0`).
*   **Enlaces Troncales Inter-Sede:** Implementación de enlaces redundantes mediante las redes de tránsito `192.168.100.0/24` y `192.168.200.0/24`.

### 📊 Tabla de Segmentación de Redes (Muestra Principal)

| Edificio / Planta | Departamento / Aula | Dirección de Red | Máscara | Enrutamiento / Gateway |
| :--- | :--- | :--- | :--- | :--- |
| **Pelayo (1ª Planta)** | Aula 10 | `172.18.0.0` | `255.255.255.0` | Subinterfaz Router Central |
| **Pelayo (1ª Planta)** | Aula 20 | `172.21.0.0` | `255.255.255.0` | Subinterfaz Router Central |
| **Pelayo (1ª Planta)** | Taller | `172.16.0.0` | `255.255.255.0` | Subinterfaz Router Central |
| **Pelayo (Planta Baja)**| Zona DMZ (Servidores) | `172.19.0.0` | `255.255.255.0` | Subinterfaz dedicada DMZ |
| **Córcega (1ª Planta)** | Aulas A10 / A20 / A30 | `192.168.1.0` - `.3.0`| `255.255.255.0` | Router de Distribución (Stick) |
| **Córcega (Planta Baja)**| Dirección / Secretaría | `192.168.4.0` - `.6.0`| `255.255.255.0` | Router Planta Baja |

---

## 🚀 Tecnologías e Implementaciones Destacadas

1.  **Enrutamiento Inter-VLAN (Router-on-a-Stick):** Configuración de subinterfaces lógicas en routers troncales utilizando encapsulación estándar **IEEE 802.1Q (`dot1Q`)**, maximizando el aprovechamiento de los interfaces físicos GigabitEthernet.
2.  **Topología Física y Cableado Estructurado:** Modelado a nivel físico real en Packet Tracer, estructurando organizadamente los *Racks* de telecomunicaciones (Paneles de parcheo, Switches Catalyst 2950, Regletas PDU y Routers).
3.  **Seguridad Perimetral (DMZ):** Creación de una Zona Desmilitarizada para aislar los servidores críticos (HTTP y FTP de la escuela) del tráfico general de las aulas de alumnos.
4.  **Infraestructura Inalámbrica Segura:** Despliegue de *Wireless Access Points* (WAPs) configurados bajo canales no solapados (1, 6 y 11) en la banda de 2.4 GHz y canales de 5 GHz, utilizando cifrado robusto `WPA2-AES`.
5.  **Políticas de Enrutamiento Determinista:** Implementación de tablas de enrutamiento estático optimizadas (`ip route`) para un control granular del tráfico inter-sede y balanceo de caminos hacia la salida simulada a Internet.

---

## 🛠️ Detalles de Configuración (CLI de Equipos)

A continuación se adjuntan muestras directas del archivo de configuración (*Running-Config*) de los nodos principales de la red:

<details>
<summary>💻 Ver Configuración de Subinterfaces e Interconexión (Router Córcega 1ª Planta)</summary>

```text
interface GigabitEthernet0/0
 no ip address
 duplex auto
 speed auto
!
interface GigabitEthernet0/0.1
 encapsulation dot1Q 10
 ip address 192.168.1.1 255.255.255.0
!
interface GigabitEthernet0/0.2
 encapsulation dot1Q 20
 ip address 192.168.2.1 255.255.255.0
!
interface GigabitEthernet0/0.3
 encapsulation dot1Q 30
 ip address 192.168.3.1 255.255.255.0
!
interface GigabitEthernet1/0
 ip address 192.168.0.1 255.255.255.0
!
ip classless
ip route 0.0.0.0 0.0.0.0 192.168.0.2
```
</details>

<details>
<summary>🔌 Ver Configuración de Modos Trunk y Access (Switches Catalyst)</summary>

```text
interface GigabitEthernet0/1
 switchport trunk allowed vlan 2-1001
 switchport mode trunk
!
interface FastEthernet0/1
 switchport access vlan 10
 switchport mode access
!
interface FastEthernet0/13
 switchport access vlan 20
 switchport mode access
```
</details>

---

## 🧪 Pruebas de Diagnóstico y Conectividad (Pings)

La red ha sido validada al 100% mediante auditorías ICMP desde hosts internos, demostrando los siguientes hitos de conectividad:
*   **Conectividad Interna:** ICMP exitoso entre equipos de diferentes plantas y aulas (ej. alcance completo hacia gateways locales `192.168.6.2` y `192.168.5.2`).
*   **Conectividad Inter-Edificios:** Tráfico fluido a través de los routers troncales por las redes `192.168.100.2` y `192.168.200.2`.
*   **Salida WAN / Internet:** Simulación exitosa de resolución y salida perimetral con `ping 80.0.0.3` (Servidor de Internet externo) con tiempos de respuesta óptimos (<1ms en entorno de simulación).

---

## 📂 Contenido del Repositorio

*   `/PKT_Files`: Contiene el archivo original `.pkt` ejecutable en Cisco Packet Tracer.
*   `/Documentation`: Capturas de pantalla del diseño lógico, diagramas físicos de los armarios rack de ambas plantas y reportes de pruebas de ping.

---

## ⚖️ Licencia

Este proyecto está bajo la **Licencia MIT**. Siéntete libre de clonarlo, auditar sus rutas o usar la topología física como base para tus propios laboratorios académicos.
