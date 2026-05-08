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

![Configuració de ports de xarxa]<img width="1280" height="800" alt="01_setup_welcome" src="https://github.com/user-attachments/assets/9036c953-70de-4f83-85ce-e6bcf7eb5324" />

*Configuració de les interfícies on escolta el servei web i el DNS*

![Creació d'usuari i contrasenya]<img width="1280" height="800" alt="02_interfaces_config" src="https://github.com/user-attachments/assets/a663febd-f3e8-4040-b282-d035457daa56" />

*Configuració de l'usuari administrador i contrasenya*

## 3. Filtratge de Tràfic i Llistes de Bloqueig

Per demostrar la funcionalitat de bloqueig d'anuncis i rastreig, s'ha afegit una regla personalitzada de bloqueig per al domini `ads.tracker.com`. Les regles es poden configurar des de la pestanya **Filters > Custom filtering rules**.

![Regla personalitzada aplicada]<img width="1280" height="800" alt="03_credentials_config" src="https://github.com/user-attachments/assets/d392bf18-91ac-4e9a-8908-1ea6304d1fe3" />

*S'ha afegit `||ads.tracker.com^` a les regles de filtratge customitzades*

A continuació, hem comprovat el funcionament de la resolució DNS i el filtratge configurant la nostra màquina per utilitzar AdGuard Home com a DNS. Com es pot veure en el test següent, la resolució per al domini bloquejat retorna `0.0.0.0`, la qual cosa significa que AdGuard ha bloquejat amb èxit la consulta i ha protegit la xarxa:

```
> nslookup ads.tracker.com 127.0.0.1
Servidor:  localhost
Address:  127.0.0.1

Nombre:  ads.tracker.com
Addresses:  ::
          0.0.0.0
```

## 4. Estadístiques del Dashboard

Al Dashboard principal d'AdGuard Home es pot veure en temps real l'evolució de les peticions DNS resoltes i bloquejades, incloent el percentatge d'estalvi de dades, consultes malicioses interceptades i quins dominis han estat més consultats i bloquejats en les últimes hores.

![Estadístiques i monitoratge - Dashboard](<img width="1280" height="800" alt="09_dashboard_blocked" src="https://github.com/user-attachments/assets/7814795a-f040-4bbd-9869-4f9c4097ea53" />)
*Estadístiques globals mostrant les consultes processades i els dominis bloquejats des del panell de control principal*

## Conclusió

Hem desplegat de manera satisfactòria AdGuard Home actuant com a proxy DNS local mitjançant Docker, comprovant com proporciona bloqueig d'anuncis i rastreig a tota la xarxa amb estadístiques fàcils de revisar i gestionar des del seu panell web.
