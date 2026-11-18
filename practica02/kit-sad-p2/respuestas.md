# Respuestas · Práctica 2 · Bodegas Atlántico

> Rellena este fichero en tu ordenador. Va en la entrega (`./lab.sh entregar`) y en la defensa te pueden preguntar por él.

## Bloque A · Piensa como un atacante (nmap)

Desde la calle: `./lab.sh cliente` y dentro `nmap -Pn -p 22,80,443,5432,8080 198.51.100.254`. Pega la salida:

```text
root@cliente:/# nmap -Pn -p 22,80,443,5432,8080 198.51.100.254
Starting Nmap 7.95 ( https://nmap.org ) at 2026-11-05 08:23 UTC
Nmap scan report for sad-p2-fw.sad-p2_calle (198.51.100.254)
Host is up (0.000039s latency).

PORT     STATE    SERVICE
22/tcp   filtered ssh
80/tcp   open     http
443/tcp  open     https
5432/tcp filtered postgresql
8080/tcp filtered http-proxy
MAC Address: 7A:55:9E:F0:6C:81 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 1.27 seconds
```

## Bloque A · El contador

Después de `./lab.sh comprobar`, mira `./lab.sh ver`. ¿Cuántos paquetes lleva el contador de A6? ¿Quién los ha mandado?

> Los ha mandado la máquina tienda (./lab.sh tienda) y fueron 8 paquetes que envié hacia la IP 172.16.10.20 que es el servidor ERP que se encuentra en la lan.

## Bloque B · La prueba del cifrado

En el firewall (`./lab.sh fw`): `tcpdump -ni wan host 198.51.100.62 -c 6`. Mientras, en otra terminal, el comercial
(`./lab.sh comercial`) hace `curl http://172.16.10.20:8080`. Pega lo que sale en el firewall:

```text
root@fw:/# tcpdump -ni wan host 198.51.100.62 -c 6
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on wan, link-type EN10MB (Ethernet), snapshot length 262144 bytes
11:31:55.927414 IP 198.51.100.62.37140 > 198.51.100.254.51820: UDP, length 32
11:32:01.047324 ARP, Request who-has 198.51.100.254 tell 198.51.100.62, length 28
11:32:01.047335 ARP, Reply 198.51.100.254 is-at a2:69:0b:ed:ed:80, length 28
11:32:21.015367 IP 198.51.100.62.37140 > 198.51.100.254.51820: UDP, length 32
11:32:26.135110 ARP, Request who-has 198.51.100.254 tell 198.51.100.62, length 28
11:32:26.135119 ARP, Reply 198.51.100.254 is-at a2:69:0b:ed:ed:80, length 28
6 packets captured
6 packets received by filter
0 packets dropped by kernel
```

En dos líneas: ¿qué se ve por la calle y qué NO se ve?

> Lo que se ve por la calle es solo tráfico UDP cifrado dirigido al puerto 51820 del firewall. Y no se ve ni la petición HTTP, ni el puerto 8080, ni la IP privada del ERP ni la IP interna del comercial en la VPN.

## Bloque C · Para la defensa (C4)

Del **mismo ataque** (la inyección a la web): ¿qué ve el vigilante de la red (el IDS) y qué ve el portero de la web? ¿Por
qué hacen falta los dos?

> Suricata (IDS/IPS) protege toda la red y sus servicios (puede ver los puertos, protocolos, conexiones), mientras que el WAF protege específicamente la lógica de la aplicación web mirando dentro de las peticiones HTTP/HTTPS donde el resto no puede ver.

## Bloque D · La nota del servidor (D0 y D4)

Nota del análisis **antes** de bastionar (D0):

> 58/100

Nota **después** (D4). ¿Por qué no llega a 100? ¿Significa que el servidor sigue inseguro? Comenta un aviso que siga saliendo.

> 63/100. Lynis revisa cosas que aquí no tocamos (particiones, banners, HTTPS) y otras que no tienen sentido en un contenedor. Lynis no es un marcador: cada sugerencia que queda, o se aplica o se justifica. No, que falten puntos hasta el 100 no significa que el sistema sea vulnerable ni esté mal protegido. El servidor ya cuenta con las medidas críticas necesarias para operar de forma segura en su entorno. Un aviso que sigue saliendo es SSH-7408 · Consider hardening SSH configuration · Port (set 22 to ), de lo que nos avisa es que cambiemos el puerto por defecto de SSH (el puerto 22) por otro distinto.
