
Dieses Projektchen ergänzt einen [Artikel zu Nextcloud aus c’t 11/2020](https://www.heise.de/select/ct/2020/11/2002714292424531451). Für die Inbetriebnahme der Nextcloud-Umgebung 
benötigen Sie:

* ein System mit Docker und docker-compose

* eine Domain nebst Host wie nextcloud.example.com

* eine E-Mail-Adresse ich@example.com

* nextcloud.example.com muss auf die IP-Adresse des Systems zeigen

Um diesen Ausgangszustand herzustellen, laden Sie das [tar-Archiv mit den Dateien herunter](https://github.com/psct/ctnextcloud/releases/download/v1/ctnextcloud.tar), entpacken Sie es und ersetzen Sie in der Datei nextcloud.yml und static.yml jeweils nextcloud.example.com (kommt mehrfach vor) durch Ihren 
Host(-namen) und die E-Mail-Adresse ich@example.com in static.yml durch Ihre Adresse.

Ändern Sie unbedingt auch das Passwort für den Nextcloud-Admin-Benutzer (ncadmin). Es ist vorbelegt als "12345678x!". Ändern Sie besser auch gleich noch den 
Namen dieses Benutzers auf einen für Sie passenden.

Die Datei acme.json braucht exakt die im tar-Archiv enthaltenen Zugriffsrechte (chmod 600), sonst verarbeitet Traefik sie nicht. Hierin laden schließlich die 
Zertifikatsinformationen.

Starten Sie anschließend mit `docker-compose -f ./nextcloud.yml up -d ` Traefik, Nextcloud und die zugehörige Datenbank.

Wenn Docker alle Images heruntergeladen und gestartet hat, sollten Sie nach einiger Zeit in eine Web-Browser unter Ihrem Host- und Domain-Namen die frische 
Nextcloud-Installation erreichen können.

Die Besonderheiten für die Nextcloud-Einrichtung:

Nextcloud wird gezwungen, alle Zugriffe auf das https-Protokoll umzuleiten; das geschieht über Nextcloud-eigene Mechanismen (das für das Apache-Image 
implementierte OVERWRITEPROTOCOL) und über ein Redirect im vorgespannten Traefik, das als Proxy Anfragen an den Docker-Host für den virtuellen Host 
nextcloud.example.com an den Nextcloud-Container weiterzuleiten.

Traefik beschafft automatisch passende Zertifikate bei Let’s Encrypt. Einige weitere Anweisungen (unter labels: für den Nextcloud-Container) sorgen dafür, dass 
Nextcloud erfährt, dass es hinter einem Proxy arbeitet. Die Variable TRUSTED_DOMAINS hilft ebenfalls dabei. Weitere Labels formen die URLs für die DAV-Protokolle 
so um, dass diese auch über Kurz-URLs erreichbar sind.
