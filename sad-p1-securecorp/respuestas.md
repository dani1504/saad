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

**7. (C)** En el `docker-compose.yml`, ¿qué diferencia hay entre `build:` e `image:`? ¿Qué
significa la línea `- "8081:80"` del servicio `phpldapadmin`?


**8. (C)** ¿Por qué en la máquina `web` no has tenido que escribir a mano `TLS_CACERT`, y en el
cliente sí? ¿Qué pasaría con esa línea del cliente si hicieras `./lab.sh reset`?

