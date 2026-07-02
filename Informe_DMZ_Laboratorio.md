
# Informe de configuración de DMZ con Cisco Packet Tracer


### 1. Objetivo del laboratorio

> Configurar una DMZ segura en Cisco Packet Tracer, incluyendo el aislamiento de servicios críticos dentro de la DMZ, el control de tráfico de redes mediante listas ACLs, exponer de forma controlada servicios web y la configuración segura de NAT.

### 2. Topología implementada

> Describe la red. 

- Cantidad de redes: LAN Interna, DMZ, Red Externa
- Dispositivos usados: 
Un router central Cisco ISR 2911
3 Switches Cisco 2960 para cada segmento.
PC_Internal: Representa un usuario en tu red interna.
Server-PT Web_DMZ: El servidor web que residirá en la DMZ.
PC_External: Simula un cliente en Internet que intenta acceder a tus servicios.

- Breve descripción de la función de cada zona (LAN, DMZ, Externa): 
LAN: Red interna, contiene 1 PC y Switch interno. En esta zona se manejan los recursos privados y la comunicación interna-
DMZ: Es la zona intermedia donde esta el servidor web. Esta aislada de la LAN y de la red Externa.
Externa: Es la red externa (internet), contiene 1 Pc externa y 1 switch externo. 

### 3. Plan de direccionamiento IP

Completa la tabla con las IPs asignadas (puedes copiarla del enunciado si no cambió).

| Dispositivo             | IP              | Máscara           | Gateway           |
|-------------------------|------------------|-------------------|-------------------|
| PC_Internal             |  192.168.1.10    | 255.255.255.0     |192.168.1.1        |
| Server_DMZ              |  192.168.2.10    | 255.255.255.0     |192.168.2.1        |
| PC_External             |  192.168.3.10    | 255.255.255.0     |192.168.3.1        |
| Router_FW Gi0/0 (LAN)   |  192.168.1.1     |                   |                   |
| Router_FW Gi0/1 (DMZ)   |  192.168.2.1     |                   |                   |
| Router_FW Gi0/2 (Ext)   |  192.168.3.1     |                   |                   |


### 4. Configuración aplicada (resumen)

> Resume los comandos o pasos más relevantes que ejecutaste. Usa texto + fragmentos de código cuando sea necesario.

- Interfaces configuradas con `ip address`

Router_FW>enable
Router_FW#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
Router_FW(config)#hostname Router_FW
Router_FW(config)#interface GigabitEthernet0/0
Router_FW(config-if)#ip address 192.168.1.1 255.255.255.0
Router_FW(config-if)#no shutdown
Router_FW(config-if)#exit
Router_FW(config)#interface GigabitEthernet0/1
Router_FW(config-if)#ip address 192.168.2.1 255.255.255.0
Router_FW(config-if)#no shutdown
Router_FW(config-if)#exit
Router_FW(config)#interface GigabitEthernet0/2
Router_FW(config-if)#ip address 192.168.3.1 255.255.255.0
Router_FW(config-if)#no shutdown
Router_FW(config-if)#exit
Router_FW(config)#end
Router_FW#
%SYS-5-CONFIG_I: Configured from console by console
- NAT:
Router_FW(config)#interface GigabitEthernet0/1
Router_FW(config-if)#ip nat inside
Router_FW(config-if)#exit
Router_FW(config)#interface GigabitEthernet0/2
Router_FW(config-if)#ip nat outside
Router_FW(config-if)#exit
Router_FW(config)#ip nat inside source static 192.168.2.10 192.168.3.1
Router_FW(config)#end
- ACLs:

Router_FW(config)#access-list 101 permit tcp any host 192.168.3.1 eq 80
Router_FW(config)#interface GigabitEthernet0/2
Router_FW(config-if)#ip access-group 101 in

Router_FW(config)#access-list 102 permit tcp 192.168.2.0 0.0.0.255 192.168.1.0 0.0.0.255 established
Router_FW(config)#access-list 102 deny ip 192.168.2.0 0.0.0.255 192.168.1.0 0.0.0.255
Router_FW(config)#access-list 102 permit ip any any
Router_FW(config)#end
                                

### 5. Verificaciones realizadas

> Describe las pruebas y su resultado. 

- Desde PC Externa accedí al servidor web DMZ (192.168.3.1), logrando acceder a la pagina web.
- Desde PC Externa mediante Command Prompt: hice Ping a la interfaz WAN/IP pública del servidor. El resultado fue Request timed out
- Desde PC_Internal accedi al servidor web DMZ (192.168.2.10), logrando acceder a la pagina web.
- Desde Server-PT Web_DMZ mediante command prompt hice ping y el resultado fue Request timed out bloqueando el acceso.


### 6. Conclusiones y recomendaciones

> ¿Qué aprendiste con este ejercicio? ¿Qué mejorarías?

Aprendi a aplicar NAT y ACLs, en una red segmentada en LAN, DMZ y red externa. Al configurar el router de la forma establecida se controla el tráfico entre las distintas zonas, protegiendo la red interna con reglas de acceso.

También entendí la importancia de la DMZ como una zona intermedia donde se puede alojar un servidor web, sin exponer directamente la red interna.
### 7. Capturas de evidencia

> Adjunta aquí (o en un PDF anexo) las capturas solicitadas: pings, navegador, comandos `show`, etc.
