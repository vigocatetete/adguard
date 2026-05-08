[README.md](https://github.com/user-attachments/files/27524713/README.md)
# Pràctica AdGuard Home - Filtratge DNS amb Docker

Aquest document detalla el procés de desplegament i configuració d'AdGuard Home utilitzant Docker Compose, així com la demostració del filtratge de consultes DNS.

## 1. Desplegament amb Docker Compose

Per al desplegament, s'ha utilitzat la imatge oficial `adguard/adguardhome`. S'han exposat els ports necessaris (53 per DNS, 80 per la interfície web i 3000 per la configuració inicial) i s'han definit els volums persistents `work` i `conf` per assegurar que la configuració i les dades sobrevisquin als reinicis del contenidor.

L'arxiu `docker-compose.yml` resultant és el següent:

```yaml
version: '3.3'
services:
  adguardhome:
    image: adguard/adguardhome
    container_name: adguardhome
    restart: unless-stopped
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "80:80/tcp"
      - "3000:3000/tcp"
    volumes:
      - ./work:/opt/adguardhome/work
      - ./conf:/opt/adguardhome/conf
```

## 2. Configuració Inicial

Un cop aixecat el contenidor amb `docker-compose up -d`, hem accedit a la interfície inicial pel port 3000 per fer la configuració. 

Hem especificat les interfícies de xarxa per l'entorn web i el servidor DNS, i hem creat les credencials d'administrador.


## 3. Filtratge de Tràfic i Llistes de Bloqueig

Per demostrar la funcionalitat de bloqueig d'anuncis i rastreig, s'ha afegit una regla personalitzada de bloqueig per al domini `ads.tracker.com`. Les regles es poden configurar des de la pestanya **Filters > Custom filtering rules**.

*S'ha afegit `||ads.tracker.com^` a les regles de filtratge customitzades*

A continuació, hem comprovat el funcionament de la resolució DNS i el filtratge configurant la nostra màquina per utilitzar AdGuard Home com a DNS. Com es pot veure en el test següent, la resolució per al domini bloquejat retorna `0.0.0.0`, la qual cosa significa que AdGuard ha bloquejat amb èxit la consulta i ha protegit la xarxa:

```
> nslookup fcbarcelona.es 127.0.0.1
Servidor:  localhost
Address:  127.0.0.1

Nombre:  fcbarcelona.es
Addresses:  ::
          0.0.0.0
```

## 4. Estadístiques del Dashboard

Al Dashboard principal d'AdGuard Home es pot veure en temps real l'evolució de les peticions DNS resoltes i bloquejades, incloent el percentatge d'estalvi de dades, consultes malicioses interceptades i quins dominis han estat més consultats i bloquejats en les últimes hores.

## Conclusió

Hem desplegat de manera satisfactòria AdGuard Home actuant com a proxy DNS local mitjançant Docker, comprovant com proporciona bloqueig d'anuncis i rastreig a tota la xarxa amb estadístiques fàcils de revisar i gestionar des del seu panell web.

Comprobación de que funciona.
<img width="1919" height="297" alt="imagen" src="https://github.com/user-attachments/assets/e4ecae35-3e89-47ad-a586-939eb8718a92" />

