# 🚀 Entendiendo ejabberd: Tu Propio Servidor de Mensajería

## 📌 ¿Qué es exactamente ejabberd?
En términos sencillos, **ejabberd es una oficina de correos digital para mensajes instantáneos**. 

Mientras que aplicaciones comerciales (como WhatsApp o Telegram) usan servidores cerrados y controlados por empresas, `ejabberd` te permite montar, configurar y controlar tu propio servidor de mensajería desde cero. 

Técnicamente hablando, es un servidor **XMPP** (Extensible Messaging and Presence Protocol) robusto, altamente escalable y de código abierto.

---

## 📖 Un poco de historia (El secreto de su rendimiento)
**ejabberd** nació en el año 2002, creado por Alexey Shchepin. Su nombre es un acrónimo de *Erlang Jabber Daemon*:

* **Jabber:** Era el nombre original del protocolo que hoy conocemos como XMPP.
* **Erlang:** Es el lenguaje de programación en el que está escrito. Erlang fue diseñado originalmente por Ericsson para controlar centrales telefónicas, por lo que es excepcionalmente bueno manejando miles de conexiones simultáneas sin colapsar.

> **Dato curioso para impresionar:** En sus inicios, WhatsApp construyó toda su infraestructura backend modificando intensamente una versión de `ejabberd` para poder soportar a millones de usuarios al mismo tiempo.

---

## ⚙️ ¿Cómo funciona la arquitectura?
El ecosistema no funciona por arte de magia; se divide en dos piezas fundamentales que se comunican entre sí:

| Componente | Rol en la red | Ejemplo en nuestra práctica |
| :--- | :--- | :--- |
| **El Servidor** | El motor central. Recibe, enruta y almacena los mensajes. Gestiona quién está online y quién no. | **ejabberd** (Corriendo en la máquina virtual Ubuntu) |
| **El Cliente** | La interfaz gráfica que usa el usuario final para escribir y leer. | **Pidgin** (Conectado mediante el puerto 5222) |

**El flujo de un mensaje es el siguiente:**
1. El *Usuario A* escribe un mensaje en su Cliente.
2. El Cliente envía el mensaje al Servidor a través del protocolo XMPP.
3. El Servidor busca dónde está conectado el *Usuario B* y se lo entrega.

---

## 🛠️ ¿Cómo se utiliza y administra?
Una vez instalado en un sistema Linux, `ejabberd` no tiene una interfaz gráfica con botones; se administra completamente desde la terminal, lo que nos da un control absoluto.

### 1. Configuración Principal
Todas las reglas del servidor (el dominio, la seguridad, los puertos y quién es el administrador) se definen en un único archivo de texto estructural:

```bash
/etc/ejabberd/ejabberd.yml
```

### 2. Gestión de Usuarios
Para interactuar con el servidor y crear las cuentas que luego usaremos en Pidgin, utilizamos su herramienta de línea de comandos llamada `ejabberdctl` (*ejabberd control*).

**Ejemplo de creación de un usuario:**
```bash
sudo ejabberdctl register alumno1 midominio.local contraseña123
```

**Ejemplo para ver quién está conectado en este momento:**
```bash
sudo ejabberdctl connected_users
```

---

## 🧠 Perspectiva Crítica: ¿Por qué montar esto hoy en día?
Un observador externo podría preguntar: *"Si ya existen plataformas gratuitas, ¿para qué tomarse la molestia de configurar ejabberd y Pidgin?"*

La respuesta radica en la **soberanía digital y la privacidad**:

* **Propiedad de los datos:** En un servidor XMPP propio, los historiales de chat, las contraseñas y los metadatos residen en tu disco duro (como en la máquina virtual de 54GB que configuramos), no en los servidores de una corporación que los usa para publicidad.
* **Federación:** Al igual que el correo electrónico, XMPP es descentralizado. Un usuario de tu servidor podría (si lo configuras para ello) chatear con un usuario de un servidor XMPP al otro lado del mundo, sin depender de una plataforma central.
* **Seguridad a medida:** Te obliga a entender cómo funcionan los puertos (5222), las conexiones cifradas (STARTTLS) y la resolución de dominios en redes locales.

## ⚙️ Configuración del Servidor (Ejabberd)

El cerebro de nuestro servidor se configura en un archivo llamado `ejabberd.yml`. **Nota importante:** Este archivo es muy estricto con los espacios (no uses la tecla *Tab*, usa la barra espaciadora).

**1. Declaración de nuestro dominio:**
Le decimos al servidor cómo se llama nuestra red.

```yaml
hosts:
  - "mosfraayo.local"
```

**2. Permisos de Administrador:**
Definimos quién es el jefe del servidor.

```yaml
acl:
  admin:
    user:
      - "admin@mosfraayo.local"
```

**3. Red de Confianza:**
Configuramos las reglas de acceso para permitir que los equipos de nuestra red local puedan comunicarse sin bloqueos.

```
access_rules:
  local:
    allow: local
  trusted_network:
    allow: all
```

**Evidencias de la configuración del servidor:**
<img width="200" height="53" alt="image" src="https://github.com/user-attachments/assets/5e1aebf9-0ee1-4e75-83e9-a15f74f5edfe" />
<img width="328" height="120" alt="image" src="https://github.com/user-attachments/assets/4f76cd7b-8919-4e61-81b8-b012fb685e51" />
<img width="158" height="70" alt="image" src="https://github.com/user-attachments/assets/27db8baa-78fe-4018-b4e8-664476c0073c" />
<img width="179" height="46" alt="image" src="https://github.com/user-attachments/assets/517dac95-1b72-4fcc-b211-a22057b98eca" />


## 💻 Configuración de Clientes (Pidgin)
Para que los usuarios puedan chatear, configuramos el programa Pidgin. Como estamos en un entorno de pruebas y no hemos generado certificados de seguridad oficiales (SSL/TLS), debemos decirle a Pidgin que acepte nuestra conexión de forma manual.

**Parámetros clave configurados en Pidgin:**

* **Puerto:** 5222 (Es la "puerta" estándar por la que entran los mensajes XMPP).

* **Seguridad de la conexión:** Requerir cifrado. Esto intenta forzar un túnel seguro (STARTTLS).

* **Autenticación:** Permitir autenticación en claro. (⚠️ Nota de seguridad: Esto se habilita exclusivamente para este entorno de laboratorio para evitar que el cliente rechace la conexión por falta de certificados firmados por una CA.)

**Evidencias de la configuración del cliente:**
<img width="575" height="471" alt="image" src="https://github.com/user-attachments/assets/5c93a662-9fd4-4a85-9653-436b8935ff34" />
<img width="589" height="624" alt="image" src="https://github.com/user-attachments/assets/b2317c90-2499-48ea-a439-81e4b0bf75fc" />
<img width="797" height="585" alt="image" src="https://github.com/user-attachments/assets/eda7db3c-f122-4056-9846-e278ff52aa7b" />
<img width="406" height="309" alt="image" src="https://github.com/user-attachments/assets/75fdc9f6-122b-4a70-a0f0-cb304944938c" />


## ✉️ Comprobación de Funcionamiento
¡La prueba final! Aquí podemos ver cómo dos usuarios conectados a nuestra red privada logran enviarse mensajes instantáneos de forma exitosa.

<img width="439" height="512" alt="image" src="https://github.com/user-attachments/assets/ac67b97b-7bd8-41cc-a91f-415e5e003be8" />
<img width="452" height="431" alt="image" src="https://github.com/user-attachments/assets/5c411b88-4e1b-4cc4-9e96-e47dddeeab90" />


