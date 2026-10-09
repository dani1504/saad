# Práctica 1 de SAD · SecureCorp — Respuestas

**Nombre y apellidos:Daniel González Castro**
**Usuario:dgonzalez**

Responde con tus palabras, en 1-3 líneas. En la defensa te preguntaré lo mismo en voz alta.

**Contraseñas que has usado** (solo porque es un laboratorio; en una empresa, jamás en un fichero):

- Tu usuario: Daniel2026
- mtorres: Marta2026

---

**1. (A1)** ¿Quién es el `issuer` de tu `ca.crt`? 
 El certificado de la CA ha sido firmado por el propio tercero de confianza, ya que es un certificado de autofirmado.
¿Hasta qué fecha es válido?
5 07:09:45 2036
¿Por qué el `subject`
y el `issuer` de la CA son iguales y los de `ldap.crt` no?
Porque en la CA ha sido un certificado de autofrimado, es decir, el que ha firmado y creado el certificado es la misma entidad. Mientrás que en el servidor LDAP, el subject es el mismo servidor y el issuer es quien nos ha firmado la petición de certificado (ldap.csr).

**2. (A3)** Pega el comando y el resultado de tus dos búsquedas:


```
a) miembros de rrhh:

ldapsearch -x -LLL -H ldap://ldap.securecorp.local -b "ou=groups,dc=securecorp,dc=local" "(cn=rrhh)" member
dn: cn=rrhh,ou=groups,dc=securecorp,dc=local
member: uid=lromero,ou=people,dc=securecorp,dc=local
member: uid=mtorres,ou=people,dc=securecorp,dc=local

b) cn y mail de todas las personas:

ldapsearch -x -LLL -H ldap://ldap.securecorp.local -b "ou=people,dc=securecorp,dc=local" "(objectClass=inetOrgPerson)" cn mail
dn: uid=lromero,ou=people,dc=securecorp,dc=local
cn: Lucia Romero
mail: lromero@securecorp.local

dn: uid=dgonzalez,ou=people,dc=securecorp,dc=local
cn: Daniel Gonzalez
mail: dgonzalez@securecorp.local

dn: uid=mtorres,ou=people,dc=securecorp,dc=local
cn: Marta Torres
mail: mtorres@securecorp.local

```

**3. (A4)** ¿Por qué la clave `ldap.key` tiene que ser de `openldap` y tener permisos 600?

Se ejecuta con el usuario y grupo del sistema openldap y no con root por cuestión de seguridad, así que si no fuera el dueño no podría leer la clave. Lleva permisos 600 porque así nos aseguramos de que solo la pueda leer openldap y no otro usuario del sistema.

**4. (A4)** ¿Qué valor has puesto en `SLAPD_SERVICES` y por qué?
SLAPD_SERVICES="ldaps:/// ldapi:///"

Pues porque slapd escucha lo que diga en la linea de SLAPD_SERVICES, entonces lo que he cambiado ha sido ldaps:/// para que escuche por el puerto 636, cifrado.

**5. (A4)** Antes de añadir `TLS_CACERT` en el cliente, `ldaps://` no funcionaba. ¿Por qué?

Porque el certificado del servidor LDAP está autofirmado por la CA de la empresa, la cual es conocida para el cliente. Al añadir TLS_CACERT con la ruta del certificado de la CA, le estamos indicando al cliente que confíe en los certificados firmados por la propia CA, haciendo que funcione el ldaps://.

**6. (B3)** Pega la salida de `klist` con tus dos tickets. ¿Para qué sirve cada uno? ¿Ha viajado tu
contraseña por la red?

```
klist
Ticket cache: FILE:/tmp/krb5cc_0
Default principal: dgonzalez@SECURECORP.LOCAL

Valid starting     Expires            Service principal
10/08/26 11:12:04  10/08/26 21:12:04  krbtgt/SECURECORP.LOCAL@SECURECORP.LOCAL
	renew until 10/15/26 11:12:04
10/08/26 11:12:54  10/08/26 21:12:04  host/web.securecorp.local@SECURECORP.LOCAL
	renew until 10/15/26 11:12:04
```
- El primero krbtgt/SECURECORP.LOCAL@SECURECORP.LOCAL es la "pulsera del parque de atracciones", es decir, sirve para autentificarte ante el servidor de Kerberos y poder solicitar tickets para otros servicios sin tener que volver a introducir tu contraseña.

- El segundo host/web.securecorp.local@SECURECORP.LOCAL es el ticket de servicio "el ticket de la atracción", es decir, sirve para poder acceder y autenticarse en la máquina web de SecureCorp.

No, en ningún momento ha viajado nuestra contraseña por la red. Ya que hemos usado el servicio de Kerberos, su función principal es evitar esto.

**7. (C)** En el `docker-compose.yml`, ¿qué diferencia hay entre `build:` e `image:`? ¿Qué
significa la línea `- "8081:80"` del servicio `phpldapadmin`?

La diferencia es que el build: le indica a Docker compose donde se encuentra un Dockerfile para que construya una imagen nueva desde cero antes de encender la máquina. Y el image: le indicamos a Docker compose el nombre de una imagen que ya existe para crear el contenedor directamente.

- La línea - "8081:80" es una redirección de puertos. Significa que el puerto 8081 de tu ordenador anfitrión se conecta internamente con el puerto 80 del contenedor phpldapadmin.

**8. (C)** ¿Por qué en la máquina `web` no has tenido que escribir a mano `TLS_CACERT`, y en el
cliente sí? ¿Qué pasaría con esa línea del cliente si hicieras `./lab.sh reset`?

No la hemos tenido que escribir a mano ya que en el Dockerfile de web ya viene la línea que tal cual dice que cuando corramos esa imagen haga "RUN echo  "TLS_CACERT /pki/ca/ca.crt" >> /etc/ldap/ldap.conf". Es decir que confíe en la CA desde que nace web. 

Esa orden borra las másquinas actuales por completo y empezar de cero, pero los certificados y LDIF se conserva. Al escribirlo a mano en cliente ese cambio se perdería y habría que volver a editarlo a mano.

En web no pasaría eso ya que lo tenemos guardado de forma que cada vez que se levante el servicio tenga el ldap.conf configurado correctamente.