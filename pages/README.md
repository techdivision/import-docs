# ToDo's

* [ ] Ergänzen um Mehrsprachigkeit einschließlich Bespielen für
  * Attribute
  * Kategorien
  * Produkte
* [ ] Noch nicht veröffentlichte Use Cases anonymisieren
* [ ] Beschreibung aller möglichen Parameter
* [x] Integration des Videos
* [ ] Überarbeitung aller Meta-Informationen hinsichtlich SEO
* [ ] Beschreiben für was die anderen Commands gut sind und wie sie funktionieren
  * import:clear:pid-file
  * import:create:configuration-file
  * import:create:ok-file
* [ ] Beschreibung wie Dateinamen aufgebaut sein müssen und wie ich das Schema anpassen kann mit Beispielen, siehe [Blog Post](https://pacemaker.techdivision.com/en/blog/sample-data-import.html)
* [ ] [Blog Post](https://pacemaker.techdivision.com/en/blog/sample-data-import.html) hinsichtlich neuen Default Dateinamen (product-import anstatt magento-import) überarbeiten
* [ ] Beschreibung der .ok File Funktionalität
  * Welche Möglichkeiten gibt es
  * Was muss rein
  * Hat die Reihenfolge der Dateien in der .ok Datei eine Auswirkung auf die Ausführungs-Reihenfolge?
    > Nein, die Dateien werden **IMMER** alphabetisch sortiert, siehe [AbstractFileResolver::loadFiles()](https://github.com/techdivision/import/blob/10.x/src/Subjects/FileResolver/AbstractFileResolver.php)
* [ ] Beschreibung der Funktionalität für den Bunch-Import
  * Welche Möglichkeiten tun sich damit auf (siehe Gabor)
  * Was heißt Bunch im Pacemaker Community Kontext
  * Wie müssen die Dateinamen + die .ok Datei aufgebaut sein
* [ ] Erstellen eines Bereichs Best Practices 
  * Pacemaker Community als Whitelabel Lösung
  * Wie kann man einen neuen Image Type anlegen
  * Wie kann Pacemaker Community als Basis für andere Extensions verwendet werden (siehe Brickfox)
  * Wie kann Pacemaker Community in eigenen Projekten eingesetzt werden
  * Wie baue, registriere und setze ich eine Extension in Pacemaker Community um
* Framework um "Callback" ergänzen
  * Wann brauche ich ein Modul
  * Wie kann ich ein Modul implementieren?
  * Wann brauche ich einen Callback?
  * Wie kann ich einen Callback implementieren?
