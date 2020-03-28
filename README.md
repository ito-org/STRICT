# STRICT

STRICT [simply trac infections] is a protocol and concept of how to anonymously track infections without tracking people.

The idea for STRICT emerged as part of a grassroots movement during the German government's #WirVsVirus hackathon in March 2020. Its goal is to offer a simple solution for combatting the spread of infective diseases. Our focus lied on data minimization and an easy-to-audit implementation to achieve maximum acceptance.

STRICT can be integrated into smart devices with Bluetooth capability, either directly into their operating system or into applications. Through the standardization of this protocol all participants can work together in a cross-border network enabling infection tracking for many geographical locations at once.

## DISCLAIMER

This protocol has not undergone thorough threat modelling and review yet, but we are working on it. It is an open work in progress, since time is of the essence. Any type of constructive feedback is welcome. We are developing a very similar protocol at https://github.com/degregat/ppdt. After the hackathon we copied and altered that protocol draft to match our  current design.

## Problem Statement

We want to do privacy preserving contact tracing and notify users if they have come in contact with potentially infected people. This should happen in a way that is as privacy preserving as possible. We want to have the following properties:

- The users should be alerted if they got in touch with infected parties, ideally only that.
- The server should not learn anything besides who is infected, ideally not even that.

## Acronyms

| Short | Long |
| ------------- | ------------- |
| BLE  | Bluetooth Low Energy  |
| PID  | Pseudonymous Identifier  |
| N  | # of days of incubation period (+ some margin)  |
| DB  | Database  |

## Protocol Description

- Every participant generates a new random PID per timeslot (e.g. every 30 minutes), these PIDs will be saved with a timestamp and a region tag.
- Each phone is running a BLE (or similar) beacon, broadcasting the current hashed PID (SHA256 truncated by 6 bytes). If two devices come close to each other, they record each others hashed PIDs and save these together with a timestamp, calculated distance (based on signal strength) and meeting duration locally on their device.
- In case of a positive diagnosis for a participant, they submit the randomized history of their PIDs from the last N days to a public DB. The PIDs of their contacts do not leave their device.
- On the Server, the PIDs will be saved with a timestamp for the upload date and a region tag.
- Every participant regularly downloads the new infected PIDs from the DB and does a local hash (SHA256) and calculates the intersection with their recorded history and marks them in its own database as infected.
- The users device calculates the risk of the user being infectious in relation to time based on the duration and distance of all PIDs marked as infected.
- Recommend actions to the user based on the result of the risk calculation.
- For the times the user was likely to be infectious they publishes the respective PIDs. And hopefully follows the recommended actions.
- In case of a positive test outcome the user publishes their PID history and self quarantines. In case of a negative outcome, they continue running the above protocol.

## Possible Extensions

- To exchange bandwidth for post-computation, a ratchet with pre- and post-generation capabilities could be used.
- During contact, if the BLE constraints permit a connection, a key exchange can be performed. Messages encrypted with the resulting key can be appended to the published IDs.
- Saving ICD-10 Codes with uploaded PIDs to calculate the risk of infection separately for every one of them and give specific advice.

## Risk Assessment

- Users log distance and duration for each PID they see, to calculate risk on device after notification.

## Threat Model

- Clients are assumed to be individually malicious, but not colluding at scale.
- The DB is assumed to be semi-honest.
- We provide only application layer de-correlation. The OS is assumed to be trustworthy, e.g. not recording the Bluetooth MACs and we do not deal with transport for submission and download.

## Malicious Clients

Possible ways for a malicious client to misbehave would be to forge/omit submissions to produce false positives and false negatives. If the PIDs are sufficiently long, the collision rate should be low enough to produce few false positives in case of forged submission. Since this is an opt-in protocol, false negatives are identical to non-participation.

A client could correlate PIDs to other users on sidechannels, to later look up which people are positive. This might be mitigated with something like Private Join and Compute, but with malicious security.

## BLE

- BLE4 allows subsequent connections via GATT servers since Android API version 21 (85% of devices according to android studio)
- BLE can exchange 26 bytes without establishing a connection
- BLE ranging seems to be accurate up to 4 meters 
- On Android, Bluetooth MAC rotation on the OS level does not provide further de-correlation, because the MAC address is changed at the same time as the message sent changes.

## Other Layers

- Anonymous submission and anonymous download can further increase user privacy
- Health authorities could give out anonymous credentials for submission with test results if it seems feasible and necessary

## Privacy and Incentives

- Only the history of PIDs of participants who were tested positive is published. Since this history is only correlated to an region, and correlation to contact history happens only on users devices, only regions, but not the contact history is leaked to the server or non-contacts. This, together with voluntary participation, can increase buy-in from the population, leading to faster response time for testing larger groups. Since the health authorities will administer the tests, local statistics will also become more accurate.
- Since people get incentivized to get tested before they become symptomatic, spread can be reduced.
- Since the PIDs get rotated, local tracking/correlation by other potentially malicious participants gets impeded.
- This kind of soft, opt-in intervention is probably most useful for the long tail to monitor resurgences. To improve monitoring, the app could walk users through self diagnosis.

## Open Questions

- Does a (weighted) intersection method exist which hides the elements of the intersection from a malicious attacker?
- Which potential malicious user behavior did we miss?
- Can we achieve robustness against colluding clients, for example neighbors or coworkers? Linkage attacks that use location data obtained by stalking Bluetooth emitters are possible.
- Do we need rate limiting to prevent spam on the DB? Can we reduce false positives from forged submissions further this way?
  * Only accept as many PIDs as someone could have generated while being infectious. This is probably only possible if an authorization by the health system or a similar party is implemented.
- Do we gain anything from anonymous submission of PIDs? (all at once, subsets, individual PIDs per circuit or on a mixnet)
  * Solution to the question before would be made ineffective.
- Further analysis of privacy leakage from plaintext DB
- BLE has a range of up to 10 meters. Can we get useful distance information and log it for each PID of a contact?
  * Yes, if the transmit power and antenna impedance are known. Our sources say its possible to send messages up to 10m indoors, or more if there are no obstructions. Outdoors signals can travel up to 50m and in rare cases even farther [citation needed]. However we have information that the values received via Bluetooth ranging vary a lot based on indoors/outdoors and the amount of water between sender and receiver, e.g. in the human body. Further tests are necessary.
- How long should the PID be?
- What type and size of regions should be used? For example, would GPS coordinates with 0 or 1 digit precision be sufficient for obscuring users' location?
