<img src="https://github.com/sensebox/OER/blob/master/senseBox_edu/images/sensebox_logo_neu.png" width="200"/>

# Sensebox "Wetterstation Nienberge"
Dies ist die Dokumentation zur Sensebox Wetterstation Nienberge. Bei Rückfragen stehen wir gerne via E-Mail zur Verfügung

## Ziel
Diese SenseBox als Instrument der Umweltdatenerfassung soll verschiedene Messdaten als OpenData bereitgestellen und so als Kartengrundlage dienen für den Kurs DigiKarto_SoSe16 am Ifgi der WWU.
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
Der Software-Sketch basiert auf der von der Open-Sense-Map bereitgestellten Grundlage welche bspw. das hochladen und runden der Daten bereits beinhaltet hat. 
Es mussten also die einzelnen Sketche welche die Messwerte und Sensoren getrennt betrachtet hatten in einen vereinigt werden.
Im Sketch müssen neben den direkt zu messenden Daten aber auch einige Daten errechnet werden. Das Schalensternanemometer beispielsweise gibt ein analoges Stromsignal aus (mehr Strom = mehr Wind) und dieses muss in Windgeschwindigkeits in [m/s] umgerechnet werden [SI-Einheit]. Ebenso muss eine Umrechnung bei dem UV-Sensor vorgenommen werden in [µW/cm²]. Die genauen Umrechnungswerte wurden jeweils den DataSheets/BeispielSketchen entnommen und entsprechend den Anforderungen angepasst. 
 
Links zu den zusätzlichen benötigten Bibliotheken für Sensoren/Shields:

 - [HDC100X Bibliothek](https://github.com/RFgermany/HDC100X_Arduino_Library)
 - [TSL45315 Bibliothek](https://github.com/adidax/Makerblog_TSL45315)
 - [VEML6070 Bibliothek](https://github.com/adafruit/Adafruit_VEML6070)
 - [Ethernet-Bibliothek](https://github.com/Wiznet/WIZ_Ethernet_Library)

**Programmcode**
Der ProgrammCode ist für diese Dokumentation in Abschnitte unterteilt worden.
Sollte nach der Überschrift nicht klar sein was in dem Abschnitt passiert bitte die Inline-Kommentare beachten.

Einbinden der SPI, Ethernet, Wire, HDC und VEML-Bibliothek, Initialisierung der Sensor-Objekte und definieren einiger Variablen:
``` c
/*
SenseBox von Matthias Podlaha und Timo Isbilir
Version 1.1
Upload-Datum: 20.05.2016
*/


#include <SPI.h>
#include <Ethernet.h>

/*
 * Zusätzliche Sensorbibliotheken, -Variablen etc im Folgenden einfügen.
 */

//TSL45315-Defines (Light in LUX); I2C-Variablen; Variablen für interne Umrechnungen
#include <Wire.h>
#define I2C_ADDR        (0x29) //Adresse des Sensors festlegen
#define REG_CONTROL     0x00
#define REG_CONFIG      0x01
#define REG_DATALOW     0x04
#define REG_DATAHIGH    0x05
#define REG_ID          0x0A

//HDC100-Temp/Humi Initialisierung
#include <HDC100X.h>   //SensorBibliothek für den HDC1000 laden
HDC100X HDC(0x43); //Adresse des Sensors festlegen
double temp; //double temp definieren, hier wird später die Temperatur gespeichert
double humi; //double humi definieren, hier wird später die Luftfeuchte gespeichert

//VEML6070
#include "Adafruit_VEML6070.h" //VEML Bibliothek laden
double uvint; //double der den UV-Wert abspeichern wird
Adafruit_VEML6070 uv = Adafruit_VEML6070(); 

//Anemometer
int analogSensorPin = 0; //Int welcher den analogen Pin festlegt an dem das Anemometer angeschlossen ist

```
Die SenseBox-ID, SensorID's, Ethernetparameter und Messparameter waren durch die Sense-Box Schablone vorgegeben. Die Sensor-ID's sind folgend unkenntlich gemacht
``` c
//SenseBox ID
#define SENSEBOX_ID "------------------------"

//Sensor IDs
#define TEMPSENSOR_ID "------------------------" //Temperatur
#define SENSOR1_ID "------------------------" // Luftfeuchtigkeit 
#define UVSENSOR_ID "------------------------" //UV-Intensität
#define SENSOR2_ID "------------------------" // Licht 
#define ANEMOMETER "------------------------" //Anemometer
```
Netzwerkeinstellungen
``` c
//Ethernet-Parameter
char server[] = "www.opensensemap.org";
byte mac[] = { ----, ----, ----, ----, ----, ---- };
// Diese IP Adresse nutzen falls DHCP nicht möglich
IPAddress myIP(---, ---, ---, ---);
EthernetClient client;
```
Einstellungen für das Intervall der Messungen. Dies bestimmt gleichzeitig die Upload-rate der SenseBox
``` c
//Messparameter
int postInterval = 10000; //Uploadintervall in Millisekunden
long oldTime = 0;

```
Setup:
``` c
void setup()
{
  Serial.begin(9600); //Baudrate festlegen
```
Netzwerksetup wurde aus der Vorlage der OSeM (OpenSenseMap) übernommen, der analaoge Pin an dem das Anemometer angeschlossen ist muss als Input deklariert werden, HDC1000 und UV-Sensor werden gestartet:
``` c
  //Netzwerksetup
  Serial.print("Starting network...");
  //Ethernet Verbindung mit DHCP ausführen..
  if (Ethernet.begin(mac) == 0) 
  {
    Serial.println("DHCP failed!");
    //Falls DHCP fehltschlägt, mit manueller IP versuchen
    Ethernet.begin(mac, myIP);
  }
  Serial.println("done!");
  delay(1000);
  Serial.println("Starting loop.");
  
  // Analoger Windsensor Pin als Input deklarieren
  pinMode(analogSensorPin , INPUT);

  //HDC1000
  HDC.begin(HDC100X_TEMP_HUMI, HDC100X_14BIT, DISABLE); //startet den Sensor

  //VEML6070
   uv.begin(VEML6070_1_T); // pass in the integration time constant
}
```
Loop beginnt mit der aus der Vorlage übernommenen Abfrage bezüglich der Uploadfrequenz und sorgt dafür dass diese konstant ist: 
``` c
void loop()
{
  //Upload der Daten mit konstanter Frequenz
  if (millis() - oldTime >= postInterval)
  {
    oldTime = millis();
```
Ab nun werden die Messwerte der einzelnen Sensoren ermittelt und mittels der aus der Vorlage bereitgestelltne Methode postFloatValue an den OSeM Server gesendet.
``` c
    //Sensor1: Temperatur und Luftfeuchte
    //HDC100
    
    humi = HDC.getHumi(); //Luftfeuchtigkeitswert in humi abspeichern
    temp = HDC.getTemp(); //Temperatur in temp abspeichern
    postFloatValue(humi, 1, SENSOR1_ID); //Luftfeuchtigkeit runden und hochladen
    postFloatValue(temp, 1, TEMPSENSOR_ID); //Temperatur runden und hochladen
    //Sensor1 Ende

    //Sensor2: UV-Light-Sensor
    //VEML6070
    uvint = uv.readUV(); //UV-Wert in uvint speichern
    postFloatValue(uvint, 1, UVSENSOR_ID); //UV-Wert runden und hochladen
    //Sensor2 Ende

    //Sensor3: Light-Sensor
    //TSL45315-Lux
    //Starten des Lichtsensors
    Wire.begin();
    Wire.beginTransmission(I2C_ADDR);
    Wire.write(0x80 | REG_CONTROL);
    Wire.write(0x03); //Power on
    Wire.endTransmission();
    // Festlegen der Belichtungszeit
    Wire.beginTransmission(I2C_ADDR);
    Wire.write(0x80 | REG_CONFIG);
    Wire.write(0x01); //400 ms
    Wire.endTransmission();
    
    //Umrechnungen
    Wire.beginTransmission(I2C_ADDR);
    Wire.write(0x80 | REG_DATALOW); 
    Wire.endTransmission();
    Wire.requestFrom(I2C_ADDR, 2); //2 Bytes anfordern
    uint16_t low = Wire.read();
    uint16_t high = Wire.read();
    while(Wire.available()){ 
    Wire.read(); 
    }
    uint32_t lux;  //Umrechnung in Lux aus dem SenseboxWiki
    lux = (high << 8) | (low << 0);
    lux = lux * 1; //Multiplikator für 400mm
    postFloatValue (lux,1,SENSOR2_ID); 
    //Ende Sensor 3
```
Der Code für das Anemometer konnte inklusive der benötigten Umrechnungen im Internet gefunden werden. Diesen haben wir noch leicht für unsere Zwecke modifiziert und das hochladen des errechneten Windgeschwindigkeitswertes eingefügt. Die Umrechnung von der 0-1023 Skala der analogRead() Funktion des Arduino zu m/s Windgeschwindigkeit wurde übernommen. Genauere Erklärung zu der Umrechnung in den Inline-Kommentaren.
Die Inline-Kommentare sind von dem Ursprungscode übernommen.
``` c
    // Sensor4: Anemometer
    //adafruitAnemometer
    /*
     *  Code taken from : http://www.hackerscapes.com/2014/11/anemometer/
     *
     *  All credits belong to their respectful owners.
     *  Edited on 20.05.2016 by Matthias
     */

    int sensorValue = 0; //Variable stores the value direct from the analog pin
    float sensorVoltage = 0; //Variable that stores the voltage (in Volts) from the anemometer being sent to the analog pin
    float windSpeed = 0; // Wind speed in meters per second (m/s)

    float voltageConversionConstant = .004882814; //This constant maps the value provided from the analog read function, which ranges from 0 to 1023, to actual voltage, which ranges from 0V to 5V
    int sensorDelay = 1000; //Delay between sensor readings, measured in milliseconds (ms)

    //Anemometer Technical Variables
    //The following variables correspond to the anemometer sold by Adafruit, but could be modified to fit other anemometers.

    float voltageMin = .4; // Mininum output voltage from anemometer in mV.
    float windSpeedMin = 0; // Wind speed in meters/sec corresponding to minimum voltage

    float voltageMax = 2.0; // Maximum output voltage from anemometer in mV.
    float windSpeedMax = 32; // Wind speed in meters/sec corresponding to maximum voltage

    sensorValue = analogRead(analogSensorPin); //Get a value between 0 and 1023 from the analog pin connected to the anemometer

    sensorVoltage = sensorValue * voltageConversionConstant; //Convert sensor value to actual voltage

    //Convert voltage value to wind speed using range of max and min voltages and wind speed for the anemometer
    if (sensorVoltage <= voltageMin) {
      windSpeed = 0; //Check if voltage is below minimum value. If so, set wind speed to zero.
    } else {
      windSpeed = (sensorVoltage - voltageMin) * windSpeedMax / (voltageMax - voltageMin); //For voltages above minimum value, use the linear relationship to calculate wind speed.
    }

    postFloatValue(windSpeed, 1 , ANEMOMETER); //Windgeschwindigkeit runden und hochladen
    // Ende von Sensor4
``` 
Um zu überprüfen ob die gemessenen/errechneten Werte Sinn ergeben lassen wir sie uns im folgenden im Seriellen Monitor zur Gegenkontrolle ausgeben. 

``` c
    // Kontrollwerte:
    // Ausgabe der Messwerte für den Seriellen Monitor
    Serial.println();
    Serial.println("Kontrollwerte");
    Serial.println();
    Serial.print("Temp: ");
    Serial.println(temp);
    Serial.print("Luftfeuchte: ");
    Serial.println(humi);
    Serial.print("UV-Intensitaet: ");
    Serial.println(uvint);
    Serial.print("Lux:");
    Serial.println(lux); 
    Serial.print("Windgeschwindigkeit: ");
    Serial.println(windSpeed);
    Serial.println();
    Serial.println("Kontrollwerte Ende");
    Serial.println();
        Serial.print("----------");
  }
}
```
Bereitgestellte Methoden der OSeM zum Hochladen und Runden der gemessenen Daten:
``` c
void postFloatValue(float measurement, int digits, String sensorId)
{ 
  //Float zu String konvertieren
  char obs[10]; 
  dtostrf(measurement, 5, digits, obs);
  //Json erstellen
  String jsonValue = "{\"value\":"; 
  jsonValue += obs; 
  jsonValue += "}";  
  //Mit OSeM Server verbinden und POST Operation durchführen
  Serial.println("-------------------------------------"); 
  Serial.print("Connectingto OSeM Server..."); 
  if (client.connect(server, 8000)) 
  {
    Serial.println("connected!");
    Serial.println("-------------------------------------");     
    //HTTP Header aufbauen
    client.print("POST /boxes/");client.print(SENSEBOX_ID);client.print("/");client.print(sensorId);client.println(" HTTP/1.1");
    client.println("Host: www.opensensemap.org"); 
    client.println("Content-Type: application/json"); 
    client.println("Connection: close");  
    client.print("Content-Length: ");client.println(jsonValue.length()); 
    client.println(); 
    //Daten senden
    client.println(jsonValue);
  }else 
  {
    Serial.println("failed!");
    Serial.println("-------------------------------------"); 
  }
  //Antwort von Server im seriellen Monitor anzeigen
  waitForServerResponse();
}

void waitForServerResponse()
{ 
  //Ankommende Bytes ausgeben
  boolean repeat = true; 
  do{ 
    if (client.available()) 
    { 
      char c = client.read();
      Serial.print(c); 
    } 
    //Verbindung beenden 
    if (!client.connected()) 
    {
      Serial.println();
      Serial.println("--------------"); 
      Serial.println("Disconnecting.");
      Serial.println("--------------"); 
      client.stop(); 
      repeat = false; 
    } 
  }while (repeat);
}
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

![Station_montiert](https://raw.githubusercontent.com/MatthesP/SenseBox/master/pictures/IMG_0847.JPG)
![Station_montiert_2](https://raw.githubusercontent.com/MatthesP/SenseBox/master/pictures/IMG_0848.JPG)

## Kontakt
Matthias Podlaha: m_podl01@wwu.de
Timo Isbilir: timoisbilir@web.de
Münster, Mai 2016
