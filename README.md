# 📝 Notas Previas a la Publicación (Revisión de Arquitectura)

El proyecto está muy bien estructurado y es un excelente ejercicio de administración de sistemas. Sin embargo, antes de publicarlo como una "puesta en producción", hay dos detalles críticos que debemos ajustar para mantener el rigor técnico:

1. **Escalabilidad del "Spoofing" local:** Llamar a esto un entorno de producción es un salto arriesgado. Depender de la edición manual del archivo `/etc/hosts` es funcional para un laboratorio o *Proof of Concept* (PoC), pero no escala. Un ingeniero de sistemas notaría que cualquier cambio de IP romperá la red, y mantener esto en decenas de clientes es inviable. Lo he enmarcado claramente como un **entorno de pruebas/laboratorio**. Para producción, necesitaríamos un servicio DNS ligero como `dnsmasq`.
2. **Contradicción en la seguridad:** Mencionas la "negociación de túneles seguros", pero luego en Pidgin habilitas *Permitir autenticación en claro sobre canales no cifrados*. Esto es una vulnerabilidad severa frente a ataques *Man-in-the-Middle*. Para un despliegue riguroso, lo ideal sería generar al menos certificados autofirmados con OpenSSL. He añadido una nota de advertencia en el README para justificar por qué se hizo así en este laboratorio.

---

# 🌐 Servidor de Mensajería Privada con XMPP (Ejabberd)

¡Bienvenido a este proyecto! Aquí documento cómo crear tu propio servidor de mensajería instantánea (como tu propio "WhatsApp" o "Telegram" privado y corporativo) utilizando el protocolo XMPP. 

Para lograrlo, hemos utilizado **Ejabberd**, uno de los motores de mensajería más robustos, instalado sobre un servidor Ubuntu, y **Pidgin** como la aplicación de chat para los usuarios.

---

## 📖 ¿Qué hace este proyecto?
En lugar de depender de servidores externos en la nube para enviar mensajes, este proyecto crea una red local y cerrada. Los mensajes viajan directamente desde el ordenador de un usuario, pasan por nuestro servidor privado, y llegan al destinatario. Es ideal para aprender sobre redes, privacidad y administración de sistemas.

---

## 🖥️ Requisitos del Entorno (Laboratorio)

Para replicar este proyecto, necesitarás crear máquinas virtuales (por ejemplo, con VirtualBox o VMware) con los siguientes requisitos mínimos:

**Para el Servidor (Ubuntu Server):**
* **CPU:** 1 Core
* **RAM:** 1 GB (Ejabberd es muy ligero)
* **Almacenamiento:** 10 GB
* **Red:** Adaptador en modo "Red Interna" o "Adaptador Puente".

**Para los Clientes (Ubuntu Desktop):**
* **CPU:** 1 Core
* **RAM:** 2 GB (Para poder usar el entorno gráfico sin problemas)
* **Software:** Cliente de mensajería `pidgin` instalado.

---

## 🏗️ Arquitectura y Topología

* **Servidor Central:** Ubuntu Server corriendo el servicio Ejabberd.
* **Dominio Virtual:** Hemos creado un dominio ficticio llamado `mosfraayo.local`.
* **Resolución de Nombres (El "Directorio Telefónico"):** Como no tenemos un servidor DNS real que traduzca `mosfraayo.local` a una dirección IP, hemos usado un truco local: editar el archivo `/etc/hosts` en todos los equipos. Esto fuerza a las máquinas a saber dónde encontrarse sin preguntar a internet.

---

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


