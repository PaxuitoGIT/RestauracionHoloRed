# RestauracionHoloRed

# Examen Final de Redes II  
## La Restauración de la HoloRed Galáctica

### Introducción

En una galaxia muy, muy lejana, la guerra civil ha dejado la infraestructura de comunicaciones en ruinas. La HoloRed galáctica – la red de comunicaciones casi instantánea de la República, ahora bajo control imperial – se ha visto fragmentada. Los rebeldes luchan por restablecer enlaces seguros entre sistemas estelares aislados. Eres un joven aprendiz de Jedi con talento en ingeniería de redes, reclutado por la Alianza Rebelde para afrontar este desafío. Armado con tu sable de luz y tus conocimientos de TCP/IP, te embarcas en misiones épicas para reconectar la galaxia.

---

## Parte I – Misiones de Conocimiento Teórico (60%)

### **Misión 1: Reconexión en la Base Eco (Hoth) – Direccionamiento IP y Subredes**

#### **Situación**
La Base Eco queda incomunicada tras un bombardeo. Cada departamento (Mando, Defensa, Médico, Hangar) necesita su propia subred IP por seguridad y cantidad de dispositivos. Dispones del bloque 172.16.0.0/24.

#### **Objetivo**
Dividir 172.16.0.0/24 en subredes para cada departamento y una para el enlace troncal.

#### **Solución Detallada**

##### **Análisis de requerimientos**

| Departamento       | Hosts necesarios | Hosts útiles requeridos | Tamaño de subred |
|--------------------|------------------|------------------------|------------------|
| Comando Central    | 50               | ≥50                    | 64               |
| Defensa Perimetral | 30               | ≥30                    | 32               |
| Centro Médico      | 20               | ≥20                    | 32               |
| Hangar y Taller    | 14               | ≥14                    | 16               |
| Enlace Troncal     | 2 (mínimo)       | 2                      | 4                |

##### **Cálculo de subredes (VLSM)**

- **Comando Central**: 64 hosts = /26 (2^6 = 64; 62 útiles)
- **Defensa Perimetral**: 32 hosts = /27 (2^5 = 32; 30 útiles)
- **Centro Médico**: 32 hosts = /27 (2^5 = 32; 30 útiles)
- **Hangar/Taller**: 16 hosts = /28 (2^4 = 16; 14 útiles)
- **Enlace Troncal**: 4 hosts = /30 (2^2 = 4; 2 útiles)

##### **Asignación de subredes**

| Departamento       | Red             | Máscara         | Rango de hosts útiles     |
|--------------------|-----------------|-----------------|--------------------------|
| Comando Central    | 172.16.0.0/26   | 255.255.255.192 | 172.16.0.1 – 172.16.0.62 |
| Defensa Perimetral | 172.16.0.64/27  | 255.255.255.224 | 172.16.0.65 – 172.16.0.94|
| Centro Médico      | 172.16.0.96/27  | 255.255.255.224 | 172.16.0.97 – 172.16.0.126|
| Hangar y Taller    | 172.16.0.128/28 | 255.255.255.240 | 172.16.0.129 – 172.16.0.142|
| Enlace Troncal     | 172.16.0.144/30 | 255.255.255.252 | 172.16.0.145 – 172.16.0.146|

![Diagrama1](images/imagen1.png "Diagrama1")

---

### **Misión 2: Sabiduría de Yoda – Algoritmos de Enrutamiento y Rutas**

#### **Situación**
Debes explicar a Yoda la diferencia entre enrutamiento estático y dinámico, y comparar protocolos de vector de distancia (RIP) vs estado de enlace (OSPF).

#### **Respuesta Detallada**

El enrutamiento es el proceso mediante el cual un router selecciona la mejor ruta para enviar paquetes de datos de una red a otra. Existen dos grandes enfoques:

#### **Enrutamiento Estático**

- **Definición:**  
  Las rutas se configuran manualmente por un administrador de red en el router, permaneciendo fijas hasta que se cambian manualmente.
- **Ventajas:**  
  - Mayor control y seguridad, pues solo se permiten rutas explícitas.
  - Consumo mínimo de recursos del router (memoria y CPU).
  - Fácil de implementar en redes pequeñas o topologías simples.
- **Desventajas:**  
  - No es flexible: ante caídas o cambios en la red, es necesario modificar la configuración manualmente.
  - No escala bien para redes grandes o en constante cambio.
  - Mayor riesgo de errores humanos.

#### **Enrutamiento Dinámico**

- **Definición:**  
  Los routers intercambian información de enrutamiento entre ellos y ajustan sus tablas automáticamente, adaptándose a cambios en la red.
- **Ventajas:**  
  - Alta adaptabilidad: actualizan rutas automáticamente ante caídas o cambios.
  - Escalabilidad: adecuado para redes grandes y complejas.
  - Menor intervención humana.
- **Desventajas:**  
  - Mayor consumo de recursos (CPU, memoria, ancho de banda).
  - Complejidad en configuración y administración.
  - Posibilidad de inestabilidad si las configuraciones no son correctas.

#### **Protocolos de Enrutamiento Dinámico**

- **De Vector de Distancia:**  
  Ejemplo: RIP (Routing Information Protocol).
  - Cada router informa a sus vecinos de la distancia (número de saltos) hasta los destinos conocidos.
  - Más simple, pero más lento en convergencia y susceptible a bucles de enrutamiento.
- **De Estado de Enlace:**  
  Ejemplo: OSPF (Open Shortest Path First).
  - Cada router comparte el estado de sus enlaces con todos los routers del área, lo que permite construir una topología completa y calcular la ruta óptima.
  - Más rápido y eficiente, pero requiere mayor capacidad de procesamiento y memoria.

#### **Comparación ante fallos y escalabilidad**

- En redes grandes o cuando un nodo cae, los protocolos de estado de enlace (OSPF) reaccionan más rápido y de forma más eficiente que los de vector de distancia (RIP), pues tienen una visión global de la red y recalculan solo lo necesario.

![RIPOSPF](images/imagen2.png "RIPOSPF")

---

### **Misión 3: Los Nombres del Holonet – DNS y Resolución de Nombres**

#### **Situación**
Explica cómo el DNS traduce nombres simbólicos a direcciones IP y su importancia.

#### **Respuesta Detallada**

- **Definición:**  
  El Sistema de Nombres de Dominio (DNS) es un sistema jerárquico y distribuido que permite traducir nombres de dominio, como `holonet.rebelion.org`, en direcciones IP que los dispositivos pueden utilizar para conectarse entre sí.
- **Funcionamiento básico:**  
  1. El usuario solicita acceso a un nombre (ejemplo: `echo.base`).
  2. El equipo consulta a un servidor DNS, que responde con la dirección IP correspondiente consultando sus “registros”.
  3. El registro más común es el **registro A**, que asocia un nombre de dominio a una dirección IPv4.
  4. Si el servidor DNS local no tiene la respuesta, consulta a otros servidores en la jerarquía hasta obtenerla.

- **Ejemplo de resolución:**
  - El usuario escribe en el navegador: `holonet.rebelion.org`
  - El equipo pregunta al servidor DNS.
  - El servidor responde con, por ejemplo: `172.16.0.10`
  - El navegador se conecta a esa IP.

- **Importancia:**
  - Facilita la comunicación usando nombres memorables en lugar de números.
  - Es esencial para el funcionamiento de servicios y aplicaciones en red.

- **¿Qué ocurre si el DNS falla?**
  - No es posible traducir nombres a IP: la comunicación se ve interrumpida.
  - Los servicios que dependen de nombres (correo, web, etc.) dejarían de funcionar hasta restaurar el DNS.

![DNS](images/imagen3.png "DNS")

---

### **Misión 4: “Es una trampa… de protocolos!” – TCP vs UDP**

#### **Situación**
Comparar TCP y UDP en la transmisión de datos, con ejemplos galácticos.

#### **Respuesta Detallada**

- **TCP (Transmission Control Protocol):**
  - Protocolo orientado a la conexión y confiable.
  - Establece una conexión antes de transmitir datos (handshake).
  - Garantiza que los datos lleguen completos y en orden, usando mecanismos de control de flujo, confirmación (ACK) y retransmisión.
  - Ideal para transferencias donde la integridad es crítica (por ejemplo, envío de planos estratégicos).
  - **Ejemplo galáctico:** Transferencia de los planos de la Estrella de la Muerte, comunicaciones administrativas.

- **UDP (User Datagram Protocol):**
  - Protocolo sin conexión y no confiable.
  - No verifica si los datos llegaron ni su orden.
  - Menor sobrecarga y latencia, ideal para transmisiones en tiempo real donde la velocidad es prioritaria sobre la integridad.
  - **Ejemplo galáctico:** Transmisión de video en directo desde un X-Wing, o envío de coordenadas de combate.

- **Resumen:**
  - **TCP:** Fiabilidad > Velocidad.
  - **UDP:** Velocidad > Fiabilidad.

![TCPvsUDP](images/imagen4.png "TCPvsUDP")

---

### **Misión 5: Comunicación Segura o lado oscuro – Criptografía y Seguridad**

#### **Situación**
Explica la diferencia entre cifrado simétrico y asimétrico, su uso y la importancia de autenticación/no repudio.

#### **Respuesta Detallada**

- **Cifrado simétrico**
  - Utiliza una única clave secreta compartida para cifrar y descifrar mensajes.
  - Rápido y eficiente para grandes volúmenes de datos.
  - El reto es el intercambio seguro de la clave.
  - **Ejemplo galáctico:** Leia y Luke comparten una frase clave secreta, ambos la usan para cifrar y descifrar sus mensajes.

- **Cifrado asimétrico**
  - Usa un par de claves: pública (se puede compartir) y privada (se mantiene en secreto).
  - Cualquier persona puede cifrar un mensaje usando la clave pública, pero solo el destinatario con la clave privada puede descifrarlo.
  - Facilita la comunicación segura sin haber compartido una clave previamente.
  - **Ejemplo galáctico:** La Alianza envía un mensaje a un nuevo aliado usando su clave pública; solo el aliado puede descifrarlo.

- **Autenticación y no repudio**
  - **Autenticación:** Permite verificar que el mensaje proviene del remitente legítimo.
  - **No repudio:** El remitente no puede negar haber enviado el mensaje.
  - Se logran con firmas digitales y certificados.
  - Es fundamental para evitar la manipulación o suplantación en la comunicación rebelde.

- **Importancia de protocolos seguros**
  - Protocolos como SSH cifran la administración remota de equipos, evitando que el Imperio intercepte credenciales.
  - Telnet transmite todo en texto plano, exponiendo la red rebelde a ataques.

![Criptografía](images/imagen5.png "Criptografia")

---

## **Bibliografía**

1. [Definición y tipos de enrutamiento dinámico - Universidad VIU](https://www.universidadviu.com/es/actualidad/nuestros-expertos/definicion-y-tipos-de-enrutamiento-dinamico#:~:text=,IS)
2. [Sistema de nombres de dominio - Wikipedia](https://es.wikipedia.org/wiki/Sistema_de_nombres_de_dominio#:~:text=tipos%20de%20informaci%C3%B3n%20a%20cada,151%20de%20cada%20dominio)
3. [Características de TCP y UDP - LinkedIn](https://www.linkedin.com/advice/0/youre-trying-establish-network-connection-rwzie?lang=es&originalSubdomain=es#:~:text=1%20Caracter%C3%ADsticas%20de%20TCP)
4. [Cifrado simétrico vs asimétrico - Blog Mailfence](https://blog.mailfence.com/es/cifrado-simetrico-vs-asimetrico/#:~:text=El%20cifrado%20sim%C3%A9trico%20utiliza%20una,el%20mensaje%20se%20descifra%20correctamente)

---
---
