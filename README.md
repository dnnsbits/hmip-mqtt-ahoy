# hmip-mqtt-ahoy

Homematic-IP Anbindung zu einer ahoy-dtu für einen Hoymiles Wechselrichter (z. B. HM600) via MQTT

folgendes mit der CCU empfangen oder steuern

* Messwerte der AC-Seite vom Wechselrichter
* Messwerte der angeschlossenen Panels (beim HM600 max. zwei Stück)
* Limitierung der Ausgangsleistung (in Prozent)


## Voraussetzungen

* CCU3 mit ccu-jack https://github.com/mdzio/ccu-jack
* AhoyDTU https://github.com/lumapu/ahoy/
* MQTT Explorer - immer hilfreich, aber optional https://github.com/thomasnordquist/MQTT-Explorer

## Einstellungen

### AhoyDTU

* Settings/MQTT
  * Broker/Server IP, z. B. __192.168.1.100__
  * Port, i. d. R. __1883__
  * Username und Passwort vom Broker
  * Topic, z. B. __inverter__
* Settings/Inverter
  *  Inverter hinzufügen, Name z. B. __hm600__
 
im MQTT Explorer

* z. B. Yield Total/Gesamt-kWh vom HM600 AC-seitig __inverter/hm600/ch0/YieldTotal__ Value __669.094__

### CCU3

#### ccu-jack

es werden drei virtuelle Messwertegeräte benötigt sowie ein Dimmer für die Limitierung der Ausgangsleistung

* virtuelles Gerät mit Symbol  __HM-ES-PMSw1-Pl__ oder auch __HM-LC-Dim1T-FM__
  * Kanal 1 für AC: __MQTT Energiemessung__
  * Kanal 2 für Panel 1/DC: __MQTT Energiemessung__
  * Kanal 3 für Panel 2/DC: __MQTT Energiemessung__
  * Kanal 4 für Limiierung: __MQTT Dimmer__

#### CCU Gerät

Topics konfigurieren

* AC Daten 
  * POWERMETER|ENERGY_COUNTER_TOPIC	__inverter/hm600/ch0/YieldTotal__
  * POWERMETER|ENERGY_COUNTER_EXTRACTOR	__ALL__
  * POWERMETER|POWER_TOPIC	__inverter/hm600/ch0/P_AC__
  * POWERMETER|POWER_EXTRACTOR	__ALL__
  * POWERMETER|CURRENT_TOPIC	__inverter/hm600/ch0/I_AC__
  * POWERMETER|CURRENT_EXTRACTOR	__ALL__
  * POWERMETER|VOLTAGE_TOPIC	__inverter/hm600/ch0/U_AC__
 	* POWERMETER|VOLTAGE_EXTRACTOR	__ALL__
  * POWERMETER|FREQUENCY_TOPIC	__inverter/hm600/ch0/F_AC__
 	* POWERMETER|FREQUENCY_EXTRACTOR	__ALL__
 
* Panel 1
  * POWERMETER|ENERGY_COUNTER_TOPIC	__inverter/hm600/ch1/YieldTotal__
  * POWERMETER|ENERGY_COUNTER_EXTRACTOR	__ALL__
  * POWERMETER|POWER_TOPIC	__inverter/hm600/ch1/P_DC__
  * POWERMETER|POWER_EXTRACTOR	__ALL__
  * POWERMETER|CURRENT_TOPIC	__inverter/hm600/ch1/I_DC__
  * POWERMETER|CURRENT_EXTRACTOR	__ALL__
  * POWERMETER|VOLTAGE_TOPIC	__inverter/hm600/ch1/U_DC__
  * POWERMETER|VOLTAGE_EXTRACTOR	__ALL__

bei Panel 2 ist der __ch1__ im Topic durch __ch2__ zu ersetzen

* Leistungsregler (Dimmer)
  * DIMMER|RANGE_MIN	__0.0__
  * DIMMER|RANGE_MAX	__100.0__
  * DIMMER|COMMAND_TOPIC	__inverter/ctrl/limit/0__
  * DIMMER|FEEDBACK_TOPIC	__inverter/hm600/ch0/active_PowerLimit__
  * DIMMER|EXTRACTOR	__ALL__
 

 	 	 




