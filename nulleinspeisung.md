# Nulleinspeisung

## Voraussetzung

* AhoyDTU ist mit virtuellem Dimmer in CCU eingebunden
* Energiemessung fürs ganze Haus ist möglich (z. B. Shelly 3EM)
* Energiemessung für Wechselrichter ist möglich, also die Energiemessung für den AC Ausgang ist eingebunden

## Idee

* der Gesamtverbrauch ist die Summe über alle Phasen plus die Einspeiseleistung des WR,
* Bsp.
  * "Hausmessung" L1=300W L2=200W L3=-100 macht saldiert 400W
  * WR Leistung 300W 
  * Haus "verbraucht" 400W+300W=700W --> es wird also nicht eingespeist
    * auf L3 werden 200W verbraucht, 300W kommen vom WR, -100W als Einspeisung 
* Bsp.
  * "Hausmessung" L1=100W L2=100W L3=-400 macht saldiert -200W
  * WR Leistung 500W 
  * Haus "verbraucht" -200W+500W=300W --> es werden nur 300W verbraucht und 200W gehen ins Netz
    * auf L3 werden 100W verbraucht und 500W vom WR dazu, macht -400W (Einspeisung)
 
## Umsetzung

__tbd__
