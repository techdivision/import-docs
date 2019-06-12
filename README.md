# ToDo's

* [ ] Ergänzen um Mehrsprachigkeit einschließlich Bespielen für
  * Attribute
  * Kategorien
  * Produkte
  
* [ ] Cases von Hallhuber + Högl anonymisieren
* [ ] Beschreibung aller möglichen Parameter
* [ ] Integration des Videos
* [ ] Überarbeitung aller Meta-Informationen hinsichtlich SEO

* [ ] Beschreiben für was die anderen Commands gut sind und wie sie funktionieren
  * import:clear:pid-file
  * import:create:configuration-file
  * import:create:ok-file

* [ ] Beschreibung wie Dateinamen aufgebaut sein müssen und wie ich das Schema anpassen kann mit Beispielen, siehe [Blog Post](https://m2if.com/blog/sample-data-import)

* [ ] [Blog Post](https://m2if.com/blog/sample-data-import) hinsichtlich neuen Default Dateinamen (product-import anstatt magento-import) überarbeiten

* [ ] Beschreibung der .ok File Funktionalität
  * Welche Möglichkeiten gibt es
  * Was muss rein
  * Hat die Reihenfolge der Dateien in der .ok Datei eine Auswirkung auf die Ausführungs-Reihenfolge?
    > Nein, die Dateien werden **IMMER** alphabetisch sortiert, siehe TechDivision\Import\Subjects\FileResolver\AbstractFileResolver::loadFiles()

* [ ] Beschreibung der Funktionalität für den Bunch-Import
  * Welche Möglichkeiten tun sich damit auf (siehe Gabor)
  * Was heißt Bunch im M2IF Kontext
  * Wie müssen die Dateinamen + die .ok Datei aufgebaut sein

* [ ] [Best Practices] M2IF als Whitelabel Lösung
  * Wie kann M2IF als Basis für andere Extensions verwendet werden (siehe Brickfox)
  * Wie kann M2IF in eigenen Projekten eingesetzt werden
  * Wie baue, registriere und setze ich eine Extension in M2IF um
