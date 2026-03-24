## 🔎 Investigación ¿Qué es una dirección IP?


| Concepto | Definición simple | Nivel técnico breve | Ejemplo |
|---|---|---|---|
| IP | Es la “dirección” que identifica un dispositivo en una red. | Sirve para que la información sepa a dónde llegar y desde dónde salió. | Tu notebook, tu celular o un servidor tienen una IP. |
| IPv4 | Es la versión más usada de direcciones IP. Se escribe con números y puntos. | Usa 32 bits, por eso tiene una cantidad limitada de direcciones disponibles. | `192.168.1.10` |
| IPv6 | Es la versión más nueva de IP. Usa números y letras en bloques. | Usa 128 bits, por lo que permite muchísimas más direcciones que IPv4. | `2001:0db8:85a3::8a2e:0370:7334` |

# ⚙️ 2. Tipos de IP (clave para developers)

## Diferencia entre:

### IP pública vs privada
- **IP pública:** es la dirección con la que tu red se muestra en internet.
- **IP privada:** es la dirección interna que usan los dispositivos dentro de una red local.

### IP estática vs dinámica
- **IP estática:** no cambia fácilmente.
- **IP dinámica:** cambia automáticamente según el router o el proveedor de internet.

| Tipo | ¿Qué es? | ¿Cuándo se usa? | Ejemplo real |
|---|---|---|---|
| Pública | Es la IP visible desde internet. | Se usa cuando un sitio, API o servidor debe ser accesible desde fuera. | Un servidor web en producción. |
| Privada | Es una IP interna dentro de una red local. | Se usa para conectar equipos dentro de casa, oficina o empresa. | Tu PC con `192.168.0.15` |
| Estática | Es una IP fija o que cambia muy poco. | Se usa en servidores, impresoras o servicios que deben mantenerse siempre ubicables. | Un backend empresarial con IP fija |
| Dinámica | Es una IP asignada automáticamente y puede cambiar. | Se usa en la mayoría de hogares y dispositivos comunes. | El router asignando IP a un celular |

---

# 💻 3. Conexión directa con desarrollo (CRÍTICO)

Responder con ejemplos reales:

## ¿Qué IP usa tu backend cuando está en:

### Local (`localhost`)
En desarrollo local, normalmente el backend usa:

- `localhost`
- `127.0.0.1`

Eso significa que el servidor está corriendo **en tu propio computador**.

### Producción (ej: servidor en internet)
En producción, normalmente usa:

- una **IP pública**
- o un **dominio** que apunta a esa IP, como `api.misitio.com`

---

## ¿Por qué no puedes acceder a `localhost` de otro computador?
Porque `localhost` siempre significa **“este mismo equipo”**.

Entonces:
- si tú escribes `localhost` en tu computador, apuntas a tu propio equipo
- si otra persona escribe `localhost` en su computador, apunta al suyo, no al tuyo

Por eso `localhost` no sirve para conectarse desde otro dispositivo.

---

## ¿Qué pasa cuando haces:

```bash
npm run dev
```

Generalmente se levanta un servidor de desarrollo en una dirección como:

```bash
http://localhost:3000
```

o

```bash
http://localhost:5173
```

Eso quiere decir que:

- la aplicación se está ejecutando en tu computador
- por defecto solo tú puedes verla localmente
- no está expuesta a internet ni a otros equipos, a menos que se configure

---

## O levantas un servidor en `127.0.0.1`?
Pasa casi lo mismo que con `localhost`:

- `127.0.0.1` es la IP de loopback
- apunta al mismo computador
- otros dispositivos no pueden acceder directamente a esa dirección en tu máquina

---

## ¿Qué IP usa una base de datos en la nube?
Normalmente usa:

- una **IP privada interna** dentro del proveedor cloud
- o un **hostname / DNS** que apunta a esa base de datos

Ejemplo:
- una base en AWS RDS suele conectarse mediante un endpoint tipo:
  `mi-db.xxxxxx.us-east-1.rds.amazonaws.com`

En producción, por seguridad, muchas veces la base de datos **no se expone directamente a internet**.

---

# 🌍 4. Caso práctico real (debugging)

## Escenario:
> “Tu frontend funciona, pero no logra conectarse al backend.”

### ¿Podría ser problema de IP? ¿por qué?
Sí, perfectamente.

Podría ser un problema de IP porque el frontend puede estar intentando conectarse a:

- una IP incorrecta
- un puerto incorrecto
- `localhost` cuando el backend está en otro equipo
- una URL vieja o mal configurada

---

### ¿Qué revisarías primero?
Primero revisaría:

1. La URL que usa el frontend para llamar al backend
2. Si el backend realmente está levantado
3. El puerto correcto
4. Si se está usando `localhost` cuando no corresponde
5. Si el backend permite conexiones desde esa red
6. CORS, firewall y variables de entorno

---

### ¿Qué errores típicos ocurren? (ej: IP mal configurada, puerto, firewall)
Errores típicos:

- IP mal configurada
- Puerto incorrecto
- Backend apagado
- Uso incorrecto de `localhost`
- Firewall bloqueando el puerto
- CORS bloqueando la solicitud
- DNS o dominio mal configurado
- Variables de entorno mal definidas

---

## Ejemplo real
Supongamos esto:

Frontend:
```bash
http://localhost:5173
```

Backend:
```bash
http://192.168.1.50:3000
```

Pero en el frontend quedó esto:

```js
fetch("http://localhost:3000/api")
```

Eso funciona solo si el backend está en el mismo computador que abre el frontend.

Si pruebas desde otro equipo, fallará, porque ese `localhost` apuntará al otro equipo, no al servidor real.

---

# 🧪 5. DHCP y DNS (lo mínimo que TODO developer debe saber)

## ¿Qué hace DHCP?
DHCP asigna automáticamente a un dispositivo:

- una dirección IP
- la puerta de enlace
- el DNS

Gracias a DHCP no tenemos que configurar manualmente la red cada vez que conectamos un equipo.

---

## ¿Qué hace DNS?
DNS traduce nombres de dominio a direcciones IP.

Ejemplo:
- tú escribes `google.com`
- DNS busca a qué IP corresponde ese nombre
- luego tu navegador se conecta a esa IP

---

## ¿Qué pasa cuando escribes `google.com` en el navegador?
Flujo simple:

1. Tu equipo se conecta a la red
2. DHCP le asigna una IP y un servidor DNS
3. Escribes `google.com`
4. El navegador pregunta al DNS: “¿Qué IP tiene ese dominio?”
5. El DNS responde con una IP
6. El navegador se conecta a esa IP
7. El servidor responde y carga la página

---

## Explicarlo paso a paso (flujo simple)
“Primero mi computador recibe una IP automáticamente gracias a DHCP. Después, cuando escribo un dominio como `google.com`, el DNS traduce ese nombre a una IP real. Con esa IP, el navegador puede encontrar el servidor y cargar la página.”

---

# 🔁 6. Analogía obligatoria

Crear analogía:

- **IP =** dirección de una casa
- **DNS =** agenda de contactos
- **DHCP =** persona que asigna casas automáticamente


## Analogía completa
“Si internet fuera una ciudad, la IP sería la dirección exacta de una casa. El DNS sería como una agenda de contactos donde buscas un nombre para saber en qué dirección vive. Y el DHCP sería como una persona que asigna automáticamente una casa o dirección disponible a cada uno que llega.”

---

# 🧠 BONUS (Nivel Bootcamp Pro)

## ¿Qué es NAT y por qué es importante?
NAT (Network Address Translation) permite que varios dispositivos con IP privada compartan una misma IP pública para salir a internet.

### ¿Por qué es importante?
- ahorra direcciones IP públicas
- permite que muchos dispositivos naveguen usando una sola conexión
- mejora la organización de la red local

Ejemplo:
- en tu casa, tu notebook, celular y Smart TV tienen IP privadas
- pero todos salen a internet usando la misma IP pública del router

---

## ¿Cómo funciona una IP en Docker o contenedores?
En Docker, cada contenedor puede tener una IP interna dentro de una red virtual.

Pero normalmente no accedes a esa IP directamente, sino que publicas un puerto, por ejemplo:

```bash
localhost:3000 -> contenedor:3000
```

Eso significa que:
- Docker conecta el puerto de tu máquina con el puerto del contenedor
- desde fuera usas `localhost`
- por dentro Docker redirige al contenedor correcto

---

## ¿Qué pasa con las IP en AWS (EC2)?
Una instancia EC2 normalmente tiene:

- una **IP privada** para comunicarse dentro de AWS
- y puede tener una **IP pública** si debe recibir tráfico desde internet

En producción, muchas veces se usa:
- un dominio
- un balanceador de carga
- o una Elastic IP

Así no dependes de recordar una IP manualmente.
