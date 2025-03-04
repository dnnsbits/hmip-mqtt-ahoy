# Nulleinspeisung

Disclaimer

    Nachmachen auf eigene Gefahr und Verantwortung! Keine Haftung für Schäden an Mensch und Geräten!

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
* dann Sollleistung für den WR ermitteln und dann Wert als Limit im WR setzen
* wenn im Haus insgesamt 300W verbraucht werden, dann den WR auf 300W stellen
* wenn im Haus insgesamt 4000W verbraucht werden, dann den WR auf die max. 600W (800W) stellen 
 
## Umsetzung

Proof-Of-Concept - Script für CCU "Programm"

    ! Nulleinspeisung V1
    ! 1. Bezug/Einspeisung vom Haus
    ! JACK000012 ist ein Shelly 3EM über ccu-jack als Messgerät eingebunden
    ! Leistung der Phasen addiert und auf ganze Zahl gerundet
    var l1 =  dom.GetObject("CCU-Jack.JACK000012:2.POWER").Value();
    var l2 =  dom.GetObject("CCU-Jack.JACK000012:3.POWER").Value();
    var l3 =  dom.GetObject("CCU-Jack.JACK000012:4.POWER").Value();
    var energie_abc_w = (l1+l2+l3).Round(0);
    ! 2. Leistung der PV
    ! hier über ein HMIP FSM gemessen und auf ganze Zahl gerundet
    var energie_mpv_w = dom.GetObject("HmIP-RF.00012345678901:5.POWER").Value();
    energie_mpv_w = energie_mpv_w.Round(0);
    ! 3. Gesamt
    ! Energie_Haus
    var energie_haus_w = energie_abc_w + energie_mpv_w;
    ! 4. Sollleistung 
    var pvsollw = energie_haus_w;
    ! obere Grenze festlegen (wäre für einen HM600 mit 600W nicht notwendig, aber vielleicht bei einem HM1200)
    if (pvsollw > 600) {
      pvsollw = 600;
    }
    ! Sollwert etwas anheben, lieber etwas einspeisen, als teuer einkaufen 
    if (pvsollw < 500) {
      pvsollw = pvsollw + 20;
    }
    ! untere Grenze festlegen, um den WR nicht auf Null zu fahren (Grundlast ist ja immer da)
    if (pvsollw < 150) {
      pvsollw = 150;
    }
    ! Sollwert in Prozent berechnen
    var pvsollp = ((pvsollw*100)/600).Round(0);
    ! 5. Sollwert (Limit) im WR setzen 
    ! JACK000013 ist der HM600 über ccu-jack und den Limiter als "Dimmer"
    dom.GetObject("CCU-Jack.JACK000013:1.LEVEL").State(pvsollp);
    

Das Programm mit dem Skript sollte möglichst häufig laufen. Das Zeitmodul der CCU ist hier ungeeignet. Daher habe ich mich für den Systemtakt an https://www.christian-luetgens.de/homematic/programmierung/allgemein/systemtakt/Systemtakt.htm orientiert und lasse das Skript jede Minute laufen.

## Erfahrungen

  * die Steuerung ist träge - eine Minute ist doch recht lang - kürzeren Takt z. B. 30 Sekunden habe ich nicht ausprobiert
  * die Übermittlung des Sollwerts via AhoyDTU an den WR und die Reaktion (in AhoyDTU UI oder MQTT Broker)  dauert schon mal zwei oder drei Minuten - keine Ahnung ob es an der Anzeige liegt oder ob es wirklich so lahm ist
  * bei "niedrigen" Leistungswerten (<500W) hat die Umrechnung in Prozent den Sollwert zu niedrig eingestellt - entweder einen Aufschlag, wie im Script oder das ganze über die absolute Leistung steuern, statt über Prozent (müsste mal ausprobiert werden)
  * und die Zeit der Ausführung könnte/sollte/braucht nur mit etwas Nachlauf und Vorlauf von Sonnenaufgang bis -untergang sein, nachts macht es wenig Sinn der AhoyDTU dauernd neue Werte zu senden

## Fazit

  * es geht, aber es wird aufgrund der Trägheit doch mehr als notwendig Strom bezogen bzw. auch eingespeist, quasi eine Fast-Nulleinspeisung bzw. auch bewusst in Kauf genommen (Anhebung im Skript)





