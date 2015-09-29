# Slides

## sysconf15-docker

Am 25. September 2015 habe ich auf der ersten SysEleven Konferenz einen Talk über docker gehalten. Ich wollte zeigen, dass die Entscheidung für oder gegen docker nicht nur auf Seiten der Entwickler getroffen werden kann. Es stellen sich ganz plötzlich eine ganze Batterie an Fragen, die in der Hauptsache Operations betreffen.

### Mein Fazit:

#### Architektur der eigenen Applikationen

* Microservices statt Monolith

     * Anwendung mit Web, Mobile und REST-API trennen
     * Was nicht heißt, dass diese keinen Code teilen dürfen: libraries
     * Erleichtert aber das Tauschen von komponenten
     * … und das skalieren von einzelnen "Problemzonen"
     
* Trennung von Applikation und Daten

  * schreibe keine Daten in den Container

* externe Resourcen (DB-Server) via Umgebungsvariablen konfigurierbar
* Konnektoren zu etcd und consul, ggf. mittels confd
* Logging konfigurierbar

  * Im Container keine Logfiles schreiben
  * stdout/-err

* Systemanwendungen und -bibliotheken benutzen

  * selbst kompilierte third-party Software nur im Notfall
  * Falls doch: Pakete für die Distribution bauen
  * Beispiel: Python 3.5.0

* reguläre Pakete für die favorisierte Distribution bauen

  * Abhängigkeiten definieren, auch auf selbst gebaute third-party-Pakete

* Der Anwendung muss egal sein wo sie läuft

  * Container, eigene VM oder Baremetal

* Monitoring Endpunkte für gängige Monitoringsysteme
* Anwendung sollte als nicht-root laufen

#### Bauen von Images

* Alles muss reproduzierbar sein
* Images klein halten

  * Aufräumen mit im build-RUN-Command
  * Kein eigenständiges RUN, da die Daten trotzdem im vorherigen Layer bleiben

* Host-Volumes nur wenn unbedingt nötig

  * UID/GID können Probleme machen

* EXPOSE und VOLUME für Dinge die potentiell geteilt werden sollen
* Applikation als eigener User starten

  * USER <user>

* Wenn mehr als ein Prozess benötigt wird: servicemanager wie s6, runit, supervisord verwenden
* run.sh/init.sh soll so klein wie möglich sein

  * So viel wie möglich beim Build erledigen

* Docker and the PID 1 zombie reaping problem

  * Konnte ich in docker 1.8 nicht nachstellen, trotzdem interessant
  * https://blog.phusion.nl/2015/01/20/docker-and-the-pid-1-zombie-reaping-problem/

#### Betrieb der Container

* Sende die Logs der Container an einen Logserver
* Halte Container aktuell

  * indem ein neues Image gebaut und deployed wird
  * Es ist also wichtig, dass Admins auch build und deployment anstoßen können

    *  Vielleicht sind Entwickler im Sprint gerade weniger am beseitigen eines OpenSSL-Bugs interessiert


* Betrachte den Container als unveränderlich

  * Vielleicht mit einer Ausnahme: Kritische Sicherheitslücke

* Monitore von außen

  * Betrachte den Container als ein Prozess

#### Containerize everything?

* Bringt mehr flexibilität

  * Selbes Setup auf OpenStack oder AWS oder … möglich

* Aber: fange klein an

  * Existierende Projekte aufzuspalten und einzeln in Container zu verpacken braucht Zeit

#### Ein Wort zu Container vs. VM

* Container sind die Applikation
* VM, neben Baremetal, eine mögliche Infrastruktur
* Es gibt also kein versus
