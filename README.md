# STRICT
STRICT [simply trac infections] is a protocol and concept how to anonymously track infections without tracking people.

Die Idee zu STRICT entstannt beim #WirVsVirus Hackathon und soll bietet eine Lösung zum anonymen Tracking von Infectionen.
Der Fokus lag dabei auf Datensparsamkeit und einfache Implementierung, damit wollen wir eine möglichst große Verbreitung erreichen. STRICT lässt sich in Smartdevice mit Bluetooth integrieren, in Betriebssysteme oder in Apps. Durch die Standartisierung des Protokols arbeiten all diese Systeme zu sammen und wir erreichen eine möglichst große Verbreitung um ein Fläschendeckendes tracking von Infectionskrankheiten zu gewährleisten.

## How it works

Das Konzept ist in 3 Komponenten eingeteilt welche im folgenden näher erläutert werden.

### Buetooth Device
Für das Tracking kommen bluetoothfähige Geräte zum einsatz welche als Beacon fungieren und eine, sich alle 30 Minuten ändernde ID aussenden und von anderen Geräten empfangen. Mithilfe der Signalstärke und eines Counters lässt sich später die Kontaktdauer und die Entfernung zu einer Infizierten Person ermitteln. Sowohl die temporären IDs als auch die empfangenen IDs werden in einer Datenbank auf dem Endgerät abgelegt. 

### Smartphone oder Computer
Zur langfristigen Speicherung (aktuell sind 2 Monate angedacht) und zum abgleich mit dem Server wird ein Smartphone oder Computer benötigt. Hier lassen sich die Daten auch von verschiedenen Devices zusammen führen. Zum Abgleich wird eine Datenbank mit infizierten IDs vom Server geladen und mit den eigenen Daten abgeglichen. Sollten sich eine oder mehrere IDs in der Kontaktdatenbank befinden ermittelt das Gerät ein Risikoprofil welches dann bestimmt welche weiteren Maßnahmen getroffen werden. Durch das Risikoprofil wird eine passende Handlungsanweisung ausgegeben und wenn gegeben auch eine weiter Meldekette ausgelöst.

### Server
Das System soll nicht von einer Stelle abhängig sein, daher sieht unser System eine möglichst dezentrale Lösung vor. Jedoch ist eine Infectionskette auch nichts womit man leichtsinnig umgehen sollte daher streben wir an das offizielle Stellen wie das Robert-Koch-Institut oder das Gesundheitsministerium die Meldeserver betreiben. Durch das Datensparsame konzept ist ein Caching der Daten gewährleistet und die Serverlast sehr gering was den Betrieb eines solchen Systems sehr einfach macht.
