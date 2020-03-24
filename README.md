# STRICT
STRICT [simply trac infections] is a protocol and concept how to anonymously track infections without tracking people.

Die Idee zu STRICT entstannt beim #WirVsVirus Hackathon und soll bietet eine Lösung zum anonymen Tracking von Infectionen.
Der Fokus lag dabei auf Datensparsamkeit und einfache Implementierung, damit wollen wir eine möglichst große Verbreitung erreichen. STRICT lässt sich in Smartdevice mit Bluetooth integrieren, in Betriebssysteme oder in Apps. Durch die Standartisierung des Protokols arbeiten all diese Systeme zu sammen und wir erreichen eine möglichst große Verbreitung um ein Fläschendeckendes tracking von Infectionskrankheiten zu gewährleisten.


## DISCLAIMER

This protocol has not undergone thorough threatmodelling and review yet, but we are working on it. It is an open work in progress, since time is of the essence. Feedback is welcome.

## Problem Statement

We want to do privacy preserving contact tracing and notify users if they have come in contact with potentially infected people. This should happen in a way that is as privacy preserving as possible. We want to have the following properties:

- The users should be alerted if they got in touch with infected parties, ideally only that.
- The server should not learn anything besides who is infected, ideally not even that.u

## Acronyms

  BLE = Bluetooth Low Energy
  PID = Pseudonymous Identifier
  N = # of days of incubation period (+ some margin)
  DB = Database

## Protocol Description

- Every participant generates a new random PID per timeslot (e.g. every 30 minutes).
- Each phone is running a BLE (or similar) beacon, broadcasting the current hashed PID (SHA256 truncated by 6 bytes). If two devices come close to each other, they record each others hashed PIDs and save these together with a timestamp, calculated distance (based on signal strength) and meeting duration locally on their device.
- In case of a positive diagnosis for a participant, they submit the history of their PIDs from the last N days to a public DB. The PIDs of their contacts do not leave their device.
- Every participant regularly downloads the new infected PIDs from the DB and does a local hash (SHA256) and calculates the intersection with their recorded history and marks them in its own database as infected.
- The users device calculates the risk of the user being infectious in relation to time based on the duration and distance of all PIDs marked as infected.
- Recommend actions to the user based on the result of the risk calculation.
- For the times the user was likely to be infectious they publishes the respective PIDs. And hopefully follows the recommended actions.
- In case of a positive test outcome the user publishes their PID history and self quarantines. In case of a negative outcome, they continue running the above protocol.

## BLE

- connectionless approaches work with BLE 4, whereas connections require BLE 5 (higher compatibility: Nougat and older don't support 5.0)
- BLE can exchange 26 bytes without establishing a connection
- BLE ranging seems to be accurate up to 4 meters 
- On Android, Bluetooth MAC rotation on the OS level does not provide further de-correlation, because the MAC address is changed at the same time as the message sent
