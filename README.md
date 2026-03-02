## 🌐 Implementación de Servidor de Mensajería XMPP (Ejabberd)

#### 📌 Descripción del Proyecto

Este repositorio documenta el despliegue, configuración y puesta en producción de un servidor de mensajería instantánea basado en el protocolo XMPP utilizando Ejabberd sobre Ubuntu Server. Se incluye la configuración de clientes Pidgin para la validación de tráfico de red y negociación de túneles seguros.

#### 🏗️ Arquitectura y Topología

** Servidor XMPP: ** Ubuntu Server (Ejabberd)

** Dominio Virtual: ** mosfraayo.local

** Clientes: ** Máquinas virtuales Ubuntu Desktop con Pidgin XMPP client.

Resolución DNS: Al carecer de un servidor DNS centralizado (Bind9), la resolución del dominio virtual se ha forzado mediante spoofing local editando el archivo /etc/hosts tanto en las máquinas clientes como en el propio servidor para evitar colisiones del motor Erlang.

#### ⚙️ Configuración del Servidor (Ejabberd)

El archivo principal de configuración (/etc/ejabberd/ejabberd.yml) es extremadamente sensible a la indentación. Se omitió el uso de tabulaciones en favor de espacios estructurados.

** Configuraciones Críticas: **

Declaración del Host:

```
hosts:
  - "mosfraayo.local"
```

Lista de Control de Acceso (ACL) para el Administrador:

```
acl:
  admin:
    user:
      - "admin@mosfraayo.local"
```

Red de Confianza (Bypass de restricciones locales):

```
access_rules:
  local:
    allow: local
  trusted_network:
    allow: all
```

<img width="200" height="53" alt="image" src="https://github.com/user-attachments/assets/79e9e2a2-c768-492c-84e2-fed8a6bda194" />
<img width="328" height="120" alt="image" src="https://github.com/user-attachments/assets/e0461226-b4c0-489b-85a5-03cfe361fee5" />
<img width="158" height="70" alt="image" src="https://github.com/user-attachments/assets/ef090dea-e082-4561-82c4-7d3557f8023a" />
<img width="179" height="46" alt="image" src="https://github.com/user-attachments/assets/8ba47168-dce5-45f3-aa2b-6a98e08e5f31" />



#### 💻 Configuración de Clientes (Pidgin)

La conexión de clientes XMPP a un servidor sin certificados SSL/TLS firmados por una Autoridad Certificadora (CA) requiere configuración explícita para evitar que el cliente rechace la conexión.

Parámetros de Seguridad Negociados:

Puerto: 5222 (Estándar de cliente a servidor XMPP).

Seguridad de la conexión: Requerir cifrado (Fuerza la negociación mediante STARTTLS en lugar de cifrado desde el origen).

Autenticación: Habilitar Permitir autenticación en claro sobre canales no cifrados.


<img width="575" height="471" alt="image" src="https://github.com/user-attachments/assets/eadb47eb-486f-41f5-adfb-3336b1500f39" />
<img width="378" height="556" alt="image" src="https://github.com/user-attachments/assets/e627c9d6-797e-4a57-8ff3-9b835cd120fe" />
<img width="544" height="613" alt="image" src="https://github.com/user-attachments/assets/3feaa15a-b296-4718-a356-95f4b681ed21" />
<img width="406" height="309" alt="image" src="https://github.com/user-attachments/assets/67bbccde-ebcb-4f00-b632-83d9268014dd" />

#### COMPROBACIÓN DE MENSAJE

<img width="504" height="588" alt="image" src="https://github.com/user-attachments/assets/b88fe355-5b43-49ad-8077-d16686562bfc" />
<img width="452" height="431" alt="image" src="https://github.com/user-attachments/assets/0b8b80bc-8d17-4bc7-ae49-2753d9368dce" />


