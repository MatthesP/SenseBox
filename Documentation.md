<img src="https://github.com/sensebox/OER/blob/master/senseBox_edu/images/sensebox_logo_neu.png" width="200"/>

# Sensebox "Wetterstation Nienberge"
Dies ist die Dokumentation zur Sensebox Wetterstation Nienberge. Bei Rückfragen stehen wir gerne via E-Mail zur Verfügung

## Ziel
Diese SenseBox als Instrument der Umweltdatenerfassung soll verschiedene Messdaten als OpenData bereitgestellee und so als Kartengrundlage dienen für den Kurs DigiKarto_SoSe16.
Neben den in der Sense-Box enthaltenen Sensoren wurde bei dieser Sensebox-Wetterstation besonders Wert auf das Schalensternanemometer gelegt. 
Die Messdaten der Station werden in einem vorgegebenen Intervall an die [OpenSenseMap](http://opensensemap.org/) übertragen und dort veröffentlicht unter dem Namen ["Wetterstation Nienberge"](http://opensensemap.org/explore/573d736d566b8d3c11114aaf)

## Materialien
#### Aus der senseBox:edu
* Genuino UNO
* [HDC1000](http://www.watterott.com/de/HDC1000-Breakout) (Temperatur- & Luftfeuchtigkeitssensor) - [technische Spezifikationen](https://github.com/watterott/HDC100X-Breakout)
* [VEML6070](http://www.watterott.com/de/VEML6070-Breakout-UV-Lichtsensor) (UV-Intensitäts-sensor) - [technische Spezifikationen](https://github.com/watterott/VEML6070-Breakout)
* [TSL45315](http://www.watterott.com/de/TSL45315-Breakout) (Lichtsensor) - [technische Spezifikationen](https://github.com/watterott/TSL45315-Breakout)
* [Sensebox-Shield](https://github.com/watterott/SenseBox-Shield)
* [W5500 Ethernet-Shield](http://www.seeedstudio.com/wiki/W5500_Ethernet_Shield_v1.0)
* Batteriehalter
* Breadbord
* Diverse Jumper Wires

#### Zusätzliche Hardware
* [Schalensternanemometer](https://www.adafruit.com/product/1733)
* diverse Kabel (Jumper Wires)
* Ethernetkabel
* Power-over-Ethernet (PoE) Adapter

## Setup Beschreibung
#### Hardwarekonfiguration
Auf den Genuino UNO ist das W5500 Ethernet-Shield gesteckt und hierauf wiederum das Sensebox-Shield.
Die Stromversorgung des Systems wird über das Ethenet-Shiled mittels PoE-Adapter sichergestellt.
Nebst der Stromversorgung dient das Ethernet-Kabel selbstversändlich auch der Datenübermittlung an die OpenSenseMap wie oben beschrieben. Für diese Lösung haben wir uns entschieden da es mittels eines PoE-Adapters möglich ist die Sensebox mit nur einem Kabel zu versorgen. 

Das Sensebox-Shield wäre für den bisherigen bzw. bisher geplanten Aufbau nicht nötig gewesen, um jedoch noch I2C bzw. weitere analoge Anschlussmöglichkeiten über Kompaktkabel zu schaffen haben wir uns entschieden dieses trotzdem zu verbauen. So halten wir uns einige Möglichkeiten offen im nachhinein noch weitere Sensoren nachzurüsten.

![Aufbau des Genuino-Sandwiches](https://raw.githubusercontent.com/MatthesP/SenseBox/master/pictures/FullSizeRender.jpg)

Zudem haben wir uns entschieden das BreadBoard auf der Halterung zusammen mit dem Genuino zu belassen, um kein seperates Breadbord in der wasserdichten Hülle verstauen zu müssen. 
Der  VEML6070 (UV-Intensitäts-sensor) und der TSL45315 (Lichtsensor) sind auf diesem BreadBoard angebracht und mittels Jumper Wires an die für I2C-Sensoren benötigten Pins angeschlossen (GND,VCC,SCL,SDA). Hierbei sind die Sensoren beide nach außen gerichtet um Verfälschungen durch Schatten (hervorgerufen bspw. durch Kabel) zu vermeiden. 

Der Temperatur und Luftfeuchtigkeits-Sensor (HDC1000) konnte entgegen den beiden Lichtsensoren nicht im inneren der wasserdichten Box befestigt werden da das Innere der Box ein eigenes Klima aufweisen könnte. Dementsprechend wurde eine kleine Elektroverteilerdose aussen an das Hauptgehäuse geklebt. Diese ist nach untenhin geöffnet, ermöglicht so eine Luftzirkulation, schützt jedoch gleichzeitig vor Spritzwasser und ermöglicht so die optimalsten Messwerte.

![Gesamtaufbau](https://raw.githubusercontent.com/MatthesP/SenseBox/master/pictures/IMG_0844.JPG)

Das größte Problem der Realisation der Station hat das Schalensternanemometer dargestellt. Dieses liefert ein analoges Stromsignal zwischen 0,4V und 2V je nach Windstärke. Es benötigt jedoch laut Datenblatt eine eigene Stromversorgung von 9-25V. Diese haben wir mittels dem der Sensebox beiliegenden Batteriepack gewährleistet. Dieses fasst 6 AA-Batteriene und liefert somit 9V. An das Batteriepack haben wir Jumper Wires angelötet um für die weitere Schaltung das BreadBoard verwenden zu können.
  
  ![BatteriePack](https://raw.githubusercontent.com/MatthesP/SenseBox/master/pictures/IMG_0850.JPG)

**Wichtig:** Der Minuspol (GND) der zusätzlichen Stromversorgung muss neben dem Anschluss an das Schalensternanemometer auch mit dem GND des Genuino verbunden sein. Sonst ist es des Genuino nicht möglich Spannungsschwankungen auf dem Signalkabel zu erkennen und somit die Windgeschwindigkeit zu berechnen. 

Um diese Verbindung herzustellen haben wir an die offenen Kabelenden des Schalensternanemometers ebenfalls Jump Wires angelötet und konnten so die Schaltung auf dem BreadBoard realisieren. Da im Chinesischen Datenblatt des Herstellers keine Belegung angegeben war mussten wir diese mittels Multimeter selber herausfinden und kamen zu folgendem Ergebniß: Schwarz = GND, Braun=VCC, Blau=Signal. Da, wie oben beschrieben, das Anemometer einen analogen Signalwert wiedergibt haben wir dieses an den Analogen Port A0 des Genuino angeschlossen.

Schaltplan:





<image src=""/>

#### Softwaresketch
Der Software-Sketch basiert auf der von der Open-Sense-Map bereitgestellten Grundlage welche bspw. das hochladen der Daten bereits beinhaltet hat.
 
Links zu den zusätzlichen Bibliotheken:

 - [HDC100X](https://github.com/RFgermany/HDC100X_Arduino_Library)
 - [TSL45315](https://github.com/adidax/Makerblog_TSL45315)
 - [Ethernet-Bibliothek](https://github.com/Wiznet/WIZ_Ethernet_Library)
 - ...



Links zu zusätzlichen Bibliotheken

``` c
  hier ist euer code

  OSM IDs unkenntlich machen
```

## OpenSenseMap Registrierung
Die Registrierung bei der OpenSenseMap erforderte lediglich die Eingabe von Name, E-mail-Adresse Aufstellungsort und die verwendeten Sensoren. Nach Absenden des Formulares konnten wir in das per E-mail zugeschickte Arduino-Sketch die Sensor-Auslese-Sketche einpflegen, da wir diese bisher getrennt voneinander in unterschiedlichen Sketchen ausgelesen hatten. Diese mussten also in einem Sketch inklusive der Schablone vereint werden.

Die Station ist nun auf der OpenSenseMap unter dem Namen "Wetterstation Nienberge" registriert. Es wurden insgesamt 4 Sensoren angebracht die folgende 5 Messwerte ergeben:

 - Temperatur (in °C)
 - Luftfeuchtigkeit (%)
 - Lichtintensität (Lux)
 - UV-Intensität (µW/cm²)
 - Windgeschwindigkeit (m/s)

Die aktuellen Daten der Station können unter folgendem Link erreicht werden:
http://opensensemap.org/explore/573d736d566b8d3c11114aaf

## Stationsaufbau
Die Station ist auf einem Balkon in der Jacobistrasse, 48161 in Münster aufgestellt und in Richtung Süd-SüdWest ausgerichtet. Dieser Standpunkt eignet sich besonders für das Sternschalenanemometer da der Wind hier nicht durch andere Gebäude beeinflusst wird, sondern frei über den angrenzenden Garagenhof wehen kann. 

## Kontakt
Matthias Podlaha Email: m_podl01@wwu.de
Timo Isbilir: timoisbilir@web.de
