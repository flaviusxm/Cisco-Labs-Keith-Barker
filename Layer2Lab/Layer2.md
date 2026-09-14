# 🌐 Packet Tracer Lab — Layer 2 Switching and VLANs

Un laborator practic realizat în **Cisco Packet Tracer** pentru înțelegerea conceptelor fundamentale de networking:

- VLAN
- Layer 2 Switching
- Access Ports
- Trunk Ports
- 802.1Q
- Router-on-a-Stick
- Sub-interfețe
- Default Gateway
- IP Static
- HTTP
- Ping / ICMP
- Rutare

Scopul laboratorului este ca **Laptop1 să poată accesa pagina web găzduită de Server1**, folosind adresa:

`http://10.67.83.35`

---

# 📋 Cuprins

1. [Obiectiv](#-obiectiv)
2. [Dispozitive utilizate](#-dispozitive-utilizate)
3. [Conexiuni fizice](#-conexiuni-fizice)
4. [Adresarea IP](#-adresarea-ip)
5. [Ce este un Router](#-ce-este-un-router)
6. [Ce este un Switch](#-ce-este-un-switch)
7. [Ce este un VLAN](#-ce-este-un-vlan)
8. [Access Port](#-access-port)
9. [Trunk Port](#-trunk-port)
10. [802.1Q](#-8021q)
11. [Router-on-a-Stick](#-router-on-a-stick)
12. [Sub-interfața](#-sub-interfaa)
13. [Default Gateway](#-default-gateway)
14. [Cum circulă pachetul](#-cum-circul-pachetul)
15. [Configurația Switch0](#-configuraia-switch0)
16. [Configurația Switch1](#-configuraia-switch1)
17. [Configurația Router1](#-configuraia-router1)
18. [Configurația Laptop1](#-configuraia-laptop1)
19. [Configurația Server1](#-configuraia-server1)
20. [Testarea](#-testarea)
21. [Comenzi de verificare](#-comenzi-de-verificare)
22. [Troubleshooting](#-troubleshooting)
23. [Concepte importante de reținut](#-concepte-importante-de-reinut)
24. [Structura proiectului](#-structura-proiectului)
25. [Cerințe](#-cerine)
26. [Status final](#-status-final)

---

# 🎯 Obiectiv

Obiectivul laboratorului este următorul:

> **Laptop1 trebuie să acceseze Server1 prin HTTP la `10.67.83.35`.**

Pentru ca acest lucru să funcționeze:

- VLAN 10 trebuie să existe pe ambele switch-uri.
- Laptop1 trebuie să fie în VLAN 10.
- Server1 trebuie să fie în VLAN 10.
- Legătura dintre Switch0 și Switch1 trebuie să fie trunk.
- Legătura dintre Switch0 și Router1 trebuie să fie trunk.
- Router1 trebuie să aibă o sub-interfață pentru VLAN 10.
- Laptop1 și Server1 trebuie să aibă IP-uri valide.
- Laptop1 și Server1 trebuie să folosească Router1 ca default gateway.
- Serviciul HTTP de pe Server1 trebuie să fie activ.

---

# 🖥️ Dispozitive utilizate

Laboratorul folosește următoarele dispozitive:

| Dispozitiv | Model     | Rol                      |
| ---------- | --------- | ------------------------ |
| Router1    | Router-PT | Rutare / Default Gateway |
| Switch0    | 2960-24TT | Layer 2 Switch           |
| Switch1    | 2960-24TT | Layer 2 Switch           |
| Laptop1    | Laptop-PT | Client                   |
| Server1    | Server-PT | Web Server               |

---

# 🔌 Conexiuni fizice

| De la   | Port               | La      | Port               | Cablu                   |
| ------- | ------------------ | ------- | ------------------ | ----------------------- |
| Router1 | FastEthernet0/0    | Switch0 | GigabitEthernet0/1 | Copper Straight-Through |
| Switch0 | GigabitEthernet0/2 | Switch1 | GigabitEthernet0/1 | Copper Straight-Through |
| Laptop1 | FastEthernet0      | Switch0 | FastEthernet0/1    | Copper Straight-Through |
| Server1 | FastEthernet0      | Switch1 | FastEthernet0/1    | Copper Straight-Through |

În acest laborator se folosește **Copper Straight-Through** pentru toate conexiunile.

---

# 🌐 Adresarea IP

Rețeaua utilizată este:

`10.67.83.0/27`

Subnet mask:

`255.255.255.224`

Adresele importante sunt:

| Dispozitiv | IP          | Mask            | Gateway     |
| ---------- | ----------- | --------------- | ----------- |
| Router1    | 10.67.83.30 | 255.255.255.224 | —           |
| Laptop1    | 10.67.83.33 | 255.255.255.224 | 10.67.83.30 |
| Server1    | 10.67.83.35 | 255.255.255.224 | 10.67.83.30 |

VLAN-ul folosit este:

`VLAN 10`

---

# 🧠 Ce este un Router?

Un **router** este un dispozitiv care operează în principal la **Layer 3 — Network Layer** al modelului OSI.

Rolul său principal este să conecteze **rețele diferite** și să decidă pe unde trebuie trimis un pachet IP.

Un router folosește o **tabelă de rutare** pentru a lua aceste decizii.

De exemplu:

```text
Network             Next Hop / Interface
10.67.83.0/27       Fa0/0.10
```

Routerul poate spune:

> „Destinația acestui pachet aparține rețelei 10.67.83.0/27, deci îl trimit prin interfața corespunzătoare.”

## Router1 în acest laborator

Router1 are următoarea interfață fizică:

```text
FastEthernet0/0
```

Pe aceasta este creată sub-interfața:

```text
FastEthernet0/0.10
```

Aceasta este asociată cu:

```text
VLAN 10
```

și are IP-ul:

```text
10.67.83.30/27
```

Acest IP este folosit de Laptop1 și Server1 ca:

```text
Default Gateway
```

---

# 🔀 Ce este un Switch?

Un **switch** este un dispozitiv care operează în principal la **Layer 2 — Data Link Layer**.

Switch-ul conectează dispozitive din aceeași rețea locală.

De exemplu:

```text
Laptop → Switch → Server
```

Switch-ul folosește în principal **MAC address-uri** pentru a decide unde să trimită cadrele Ethernet.

Switch-ul învață ce dispozitiv se află pe fiecare port prin intermediul MAC address-urilor.

Exemplu conceptual:

```text
MAC Address             Port

AA:AA:AA:AA:AA:AA       Fa0/1
BB:BB:BB:BB:BB:BB       Fa0/2
```

Dacă switch-ul primește un frame destinat MAC-ului:

```text
BB:BB:BB:BB:BB:BB
```

îl poate trimite direct pe:

```text
Fa0/2
```

---

# 🏷️ Ce este un VLAN?

**VLAN** înseamnă:

> Virtual Local Area Network

Un VLAN permite împărțirea logică a unui switch în mai multe rețele separate.

În acest laborator avem:

```text
VLAN 10
```

Laptop1 și Server1 sunt membri ai VLAN-ului 10.

Asta înseamnă că traficul lor aparține VLAN-ului 10.

Un VLAN creează un **broadcast domain** separat.

De exemplu, fără VLAN-uri:

```text
PC1
PC2
PC3
PC4
```

ar putea aparține aceluiași broadcast domain.

Cu VLAN-uri:

```text
VLAN 10
    PC1
    PC2

VLAN 20
    PC3
    PC4
```

VLAN 10 și VLAN 20 sunt separate logic.

---

# 🔌 Access Port

Un **Access Port** este un port de switch care aparține unui singur VLAN.

În laborator:

```text
Switch0 Fa0/1
```

este Access Port pentru:

```text
VLAN 10
```

Laptop1 este conectat aici.

Pe Switch1:

```text
Switch1 Fa0/1
```

este Access Port pentru:

```text
VLAN 10
```

Server1 este conectat aici.

Configurația este:

```text
switchport mode access
switchport access vlan 10
```

Un dispozitiv final, precum un PC, este în mod normal conectat la un access port.

---

# 🔗 Trunk Port

Un **Trunk Port** este un port care poate transporta trafic pentru mai multe VLAN-uri.

În acest laborator avem trunk-uri între:

```text
Switch0 ↔ Switch1
```

și:

```text
Switch0 ↔ Router1
```

Trunk-ul folosește **802.1Q VLAN tagging** pentru a identifica VLAN-ul din care face parte traficul.

Configurația este:

```text
switchport mode trunk
switchport trunk allowed vlan 10
```

Prin urmare, VLAN 10 poate traversa trunk-ul.

---

# 🏷️ 802.1Q

**IEEE 802.1Q** este standardul folosit pentru VLAN tagging pe Ethernet.

Atunci când un frame traversează un trunk, switch-ul poate adăuga un tag care indică VLAN-ul.

Conceptual:

```text
Ethernet Frame
      +
802.1Q Tag
      |
      ↓
VLAN ID = 10
```

Astfel, dispozitivul de la celălalt capăt al trunk-ului poate identifica faptul că frame-ul aparține:

```text
VLAN 10
```

În laborator, comanda:

```text
encapsulation dot1Q 10
```

spune routerului:

> „Această sub-interfață procesează traficul tagged pentru VLAN 10.”

---

# 🥢 Router-on-a-Stick

**Router-on-a-Stick** este o metodă prin care un singur port fizic al routerului este folosit pentru mai multe VLAN-uri.

În loc să avem:

```text
Router
 ├── Interface pentru VLAN 10
 ├── Interface pentru VLAN 20
 └── Interface pentru VLAN 30
```

putem avea:

```text
Router
 |
 | FastEthernet0/0
 |
 ├── Fa0/0.10 → VLAN 10
 ├── Fa0/0.20 → VLAN 20
 └── Fa0/0.30 → VLAN 30
```

Fiecare sub-interfață reprezintă un VLAN.

În laborator avem:

```text
FastEthernet0/0.10
```

pentru:

```text
VLAN 10
```

---

# 🧩 Sub-interfața

O **sub-interfață** este o interfață logică creată pe o interfață fizică.

În laborator:

```text
FastEthernet0/0
```

este interfața fizică.

Pe ea avem:

```text
FastEthernet0/0.10
```

Sub-interfața:

```text
Fa0/0.10
```

este asociată cu:

```text
VLAN 10
```

prin:

```text
encapsulation dot1Q 10
```

și primește:

```text
IP = 10.67.83.30
```

---

# 🚪 Default Gateway

**Default Gateway** este dispozitivul către care un host trimite traficul atunci când destinația nu se află în propria sa rețea locală.

În acest laborator:

```text
Laptop1
IP: 10.67.83.33
Gateway: 10.67.83.30
```

Routerul are:

```text
10.67.83.30
```

Prin urmare, pentru Laptop1:

```text
10.67.83.30
```

este default gateway.

Același lucru este valabil pentru Server1:

```text
Server1
IP: 10.67.83.35
Gateway: 10.67.83.30
```

---

# 📦 Cum circulă pachetul?

Aceasta este una dintre cele mai importante părți ale laboratorului.

Vrem ca:

```text
Laptop1
10.67.83.33
```

să acceseze:

```text
Server1
10.67.83.35
```

## Pasul 1 — Laptopul creează traficul

Laptop1 vrea să trimită un request către:

```text
10.67.83.35
```

De exemplu:

```text
ping 10.67.83.35
```

sau:

```text
http://10.67.83.35
```

---

## Pasul 2 — Laptopul verifică rețeaua

Laptop1 are:

```text
IP:      10.67.83.33
Mask:    255.255.255.224
Gateway: 10.67.83.30
```

Server1 are:

```text
10.67.83.35
```

Ambele adrese aparțin aceleiași rețele:

```text
10.67.83.0/27
```

Prin urmare, comunicarea poate fi făcută direct la Layer 2, fără ca routerul să fie necesar pentru acest trafic.

---

## Pasul 3 — Laptopul folosește ARP

Pentru a trimite un frame Ethernet către Server1, Laptop1 are nevoie de MAC address-ul serverului.

Laptopul poate trimite un:

```text
ARP Request
```

practic întrebând:

> „Cine are IP-ul 10.67.83.35?”

Serverul răspunde:

```text
ARP Reply
```

cu MAC address-ul său.

Laptopul poate apoi construi frame-ul Ethernet.

---

## Pasul 4 — Frame-ul intră în Switch0

Laptop1 este conectat la:

```text
Switch0 Fa0/1
```

Acest port este:

```text
Access VLAN 10
```

Prin urmare, frame-ul intră în:

```text
VLAN 10
```

---

## Pasul 5 — Switch0 trimite frame-ul pe trunk

Serverul se află pe Switch1.

Pentru ca VLAN 10 să ajungă la Switch1, traficul trebuie să treacă prin:

```text
Switch0 Gig0/2
        ↓
Switch1 Gig0/1
```

Aceste porturi sunt trunk.

VLAN 10 este permis pe trunk:

```text
switchport trunk allowed vlan 10
```

---

## Pasul 6 — Switch1 primește traficul

Switch1 primește frame-ul pe:

```text
Gig0/1
```

Trunk-ul identifică:

```text
VLAN 10
```

Switch1 știe că:

```text
Fa0/1
```

este în VLAN 10.

Prin urmare, frame-ul este trimis către Server1.

---

## Pasul 7 — Server1 primește frame-ul

Server1 are:

```text
IP: 10.67.83.35
```

și primește request-ul.

Dacă este un ping, serverul răspunde cu:

```text
ICMP Echo Reply
```

Dacă este un request HTTP, serverul răspunde cu pagina web.

---

# 🌍 Important: unde intră Routerul?

În configurația actuală, Laptop1 și Server1 sunt în aceeași subrețea:

```text
10.67.83.0/27
```

și același VLAN:

```text
VLAN 10
```

Prin urmare, pentru comunicarea directă:

```text
Laptop1 → Server1
```

routerul **nu este necesar pentru rutare**.

Routerul este configurat pentru a reprezenta gateway-ul VLAN-ului și pentru a permite ulterior comunicarea către alte rețele/VLAN-uri.

Dacă, de exemplu, am avea:

```text
VLAN 10 → 10.67.83.0/27

VLAN 20 → altă rețea
```

atunci routerul ar putea face:

```text
VLAN 10 → Router → VLAN 20
```

Aceasta este **inter-VLAN routing**.

---

# ⚙️ Configurația Switch0

## 1. Crearea VLAN-ului

Intrăm în CLI:

```text
enable
configure terminal
vlan 10
name VLAN10
exit
```

Acum Switch0 știe că există:

```text
VLAN 10
```

---

## 2. Configurarea portului pentru Laptop1

Laptop1 este conectat la:

```text
FastEthernet0/1
```

Configurăm:

```text
interface fastEthernet 0/1
switchport mode access
switchport access vlan 10
no shutdown
exit
```

Asta înseamnă:

```text
Fa0/1
    ↓
Access Port
    ↓
VLAN 10
    ↓
Laptop1
```

---

## 3. Configurarea trunk-ului către Router

Routerul este conectat la:

```text
Switch0 GigabitEthernet0/1
```

Configurăm:

```text
interface gigabitEthernet 0/1
switchport mode trunk
switchport trunk allowed vlan 10
no shutdown
exit
```

---

## 4. Configurarea trunk-ului către Switch1

Switch1 este conectat la:

```text
Switch0 GigabitEthernet0/2
```

Configurăm:

```text
interface gigabitEthernet 0/2
switchport mode trunk
switchport trunk allowed vlan 10
no shutdown
exit
```

---

# ⚙️ Configurația Switch1

## 1. Crearea VLAN-ului

```text
enable
configure terminal
vlan 10
name VLAN10
exit
```

---

## 2. Configurarea portului pentru Server1

Server1 este conectat la:

```text
FastEthernet0/1
```

Configurăm:

```text
interface fastEthernet 0/1
switchport mode access
switchport access vlan 10
no shutdown
exit
```

Server1 este astfel în:

```text
VLAN 10
```

---

## 3. Configurarea trunk-ului către Switch0

```text
interface gigabitEthernet 0/1
switchport mode trunk
switchport trunk allowed vlan 10
no shutdown
exit
```

Acum VLAN 10 poate traversa:

```text
Switch0 ↔ Switch1
```

---

# ⚙️ Configurația Router1

Router1 folosește:

```text
FastEthernet0/0
```

Mai întâi activăm interfața fizică:

```text
enable
configure terminal

interface fastEthernet 0/0
no shutdown
exit
```

Apoi creăm sub-interfața pentru VLAN 10:

```text
interface fastEthernet 0/0.10
encapsulation dot1Q 10
ip address 10.67.83.30 255.255.255.224
no shutdown
exit
```

## Explicația comenzilor

### `interface fastEthernet 0/0.10`

Creează sub-interfața logică:

```text
Fa0/0.10
```

---

### `encapsulation dot1Q 10`

Spune routerului:

> „Această sub-interfață procesează cadre tagged pentru VLAN 10.”

---

### `ip address 10.67.83.30 255.255.255.224`

Configurează IP-ul routerului:

```text
10.67.83.30/27
```

Acesta este gateway-ul VLAN-ului 10.

---

### `no shutdown`

Pornește interfața.

---

# 💻 Configurația Laptop1

Laptop1 trebuie configurat cu IP static.

Mergem la:

```text
Laptop1
→ Desktop
→ IP Configuration
```

Selectăm:

```text
Static
```

și introducem:

| Câmp            | Valoare           |
| --------------- | ----------------- |
| IPv4 Address    | `10.67.83.33`     |
| Subnet Mask     | `255.255.255.224` |
| Default Gateway | `10.67.83.30`     |
| DNS Server      | `8.8.8.8`         |

Laptop1 este astfel:

```text
IP:       10.67.83.33
Mask:     255.255.255.224
Gateway:  10.67.83.30
VLAN:     10
```

---

# 🖥️ Configurația Server1

Server1 trebuie configurat cu IP static.

Mergem la:

```text
Server1
→ Desktop
→ IP Configuration
```

Selectăm:

```text
Static
```

și introducem:

| Câmp            | Valoare           |
| --------------- | ----------------- |
| IPv4 Address    | `10.67.83.35`     |
| Subnet Mask     | `255.255.255.224` |
| Default Gateway | `10.67.83.30`     |
| DNS Server      | `8.8.8.8`         |

Server1 va avea:

```text
IP:       10.67.83.35
Mask:     255.255.255.224
Gateway:  10.67.83.30
VLAN:     10
```

---

# 🌐 Activarea HTTP

Server1 trebuie să ruleze un Web Server.

Mergem la:

```text
Server1
→ Services
→ HTTP
```

și verificăm:

```text
HTTP = ON
```

Serverul poate răspunde astfel la:

```text
http://10.67.83.35
```

Pagina implicită poate fi modificată în:

```text
Index.html
```

---

# 🧪 Testarea laboratorului

## Testul 1 — Ping

Pe Laptop1:

```text
Desktop
→ Command Prompt
```

Executăm:

```text
ping 10.67.83.35
```

Dacă totul este configurat corect, trebuie să primim:

```text
Reply from 10.67.83.35
```

de mai multe ori.

Un rezultat de tipul:

```text
Packets: Sent = 4
Received = 4
Lost = 0
```

înseamnă că ping-ul a avut succes.

---

# 📡 Ce este Ping?

`ping` este folosit pentru testarea conectivității.

Folosește:

```text
ICMP
```

Mai exact:

```text
ICMP Echo Request
```

și:

```text
ICMP Echo Reply
```

Laptop1 spune practic:

> „Server1, ești acolo?”

Server1 răspunde:

> „Da.”

Dacă ping-ul funcționează, avem o indicație puternică faptul că conectivitatea IP este funcțională.

---

# 🌍 Testul 2 — HTTP

Pe Laptop1:

```text
Desktop
→ Web Browser
```

În bara de adrese introducem:

```text
http://10.67.83.35
```

Dacă totul este configurat corect, pagina web găzduită de Server1 va fi afișată.

---

# 🔍 Ce se întâmplă când accesăm HTTP?

Browserul Laptopului trimite un request către:

```text
10.67.83.35
```

Serverul rulează:

```text
HTTP
```

pe:

```text
TCP port 80
```

Conceptual:

```text
Laptop1
   |
   | TCP → Port 80
   |
   ↓
Server1
   |
   | HTTP Response
   |
   ↓
Laptop1
```

Browserul afișează apoi pagina primită de la server.

---

# 🔎 Comenzi de verificare

## Switch0

### Verificarea VLAN-urilor

```text
show vlan brief
```

Ar trebui să vedem VLAN 10 și portul Fa0/1 asociat acestuia.

---

### Verificarea trunk-urilor

```text
show interfaces trunk
```

Această comandă arată:

- porturile trunk
- encapsularea
- VLAN-urile permise
- statusul trunk-ului

---

### Verificarea statusului porturilor

```text
show interfaces status
```

---

### Configurația completă

```text
show running-config
```

---

# 🔎 Switch1

Putem folosi:

```text
show vlan brief
```

```text
show interfaces trunk
```

```text
show interfaces status
```

```text
show running-config
```

---

# 🔎 Router1

## Verificarea interfețelor

```text
show ip interface brief
```

Trebuie să vedem ceva asemănător:

```text
Interface              IP-Address      Status    Protocol

FastEthernet0/0        unassigned      up        up
FastEthernet0/0.10     10.67.83.30     up        up
```

---

## Verificarea sub-interfeței

```text
show running-config interface fastEthernet 0/0.10
```

---

## Verificarea tabelei de rutare

```text
show ip route
```

---

# 🧪 Exemplu `show vlan brief`

Un output relevant poate arăta astfel:

```text
VLAN Name                             Status    Ports

1    default                          active    Fa0/2, Fa0/3, Fa0/4
10   VLAN10                           active    Fa0/1
```

Important este să verificăm că:

```text
VLAN 10
```

există și:

```text
Fa0/1
```

este asociat cu VLAN 10.

---

# 🔗 Exemplu `show interfaces trunk`

Un output relevant:

```text
Port        Mode         Encapsulation  Status        Native vlan
Gi0/1       on           802.1q         trunking      1
Gi0/2       on           802.1q         trunking      1

Port        Vlans allowed on trunk
Gi0/1       10
Gi0/2       10
```

Important:

```text
Status = trunking
```

și:

```text
VLAN 10
```

trebuie să fie permis.

---

# 🐛 Troubleshooting

## ❌ Laptopul nu are IP-ul corect

Verifică:

```text
Desktop
→ IP Configuration
```

Trebuie să ai:

```text
IP:       10.67.83.33
Mask:     255.255.255.224
Gateway:  10.67.83.30
```

---

# ❌ Ping-ul nu funcționează

Verifică în ordine:

### 1. VLAN-ul există

```text
show vlan brief
```

Trebuie să existe:

```text
VLAN 10
```

---

### 2. Portul Laptopului este în VLAN 10

Pe Switch0:

```text
show vlan brief
```

Fa0/1 trebuie să fie în:

```text
VLAN 10
```

---

### 3. Portul Serverului este în VLAN 10

Pe Switch1:

```text
show vlan brief
```

Fa0/1 trebuie să fie în:

```text
VLAN 10
```

---

### 4. Trunk-ul funcționează

Rulează:

```text
show interfaces trunk
```

Verifică dacă VLAN 10 este permis.

---

### 5. Routerul este pornit

Pe Router1:

```text
show ip interface brief
```

Trebuie ca:

```text
Fa0/0
```

și:

```text
Fa0/0.10
```

să fie:

```text
up
```

---

# ❌ Browserul nu deschide pagina

Verifică:

```text
Server1
→ Services
→ HTTP
```

HTTP trebuie să fie:

```text
ON
```

Verifică și IP-ul:

```text
10.67.83.35
```

În browser trebuie să folosești:

```text
http://10.67.83.35
```

---

# ❌ Trunk-ul nu funcționează

Verifică dacă ai:

```text
switchport mode trunk
```

și:

```text
switchport trunk allowed vlan 10
```

Pe ambele capete ale legăturii trebuie să existe configurația corectă.

---

# ❌ Portul este down

Poți folosi:

```text
no shutdown
```

pe interfața relevantă.

De asemenea, verifică:

- cablul
- portul
- dispozitivul
- conexiunea fizică

---

# 🧠 Concepte importante de reținut

## Layer 2

Layer 2 se ocupă în principal de:

- Ethernet frames
- MAC addresses
- Switch-uri
- VLAN-uri
- Access Ports
- Trunk Ports
- 802.1Q

---

## Layer 3

Layer 3 se ocupă în principal de:

- IP addresses
- Routing
- Routers
- Subnetting
- Routing table
- Default Gateway

---

## MAC Address

MAC address-ul identifică o interfață de rețea la nivel Layer 2.

Exemplu:

```text
AA:BB:CC:DD:EE:FF
```

Switch-ul folosește MAC address-ul pentru forwarding-ul Ethernet.

---

## IP Address

IP address-ul identifică logic un dispozitiv/interfață într-o rețea Layer 3.

Exemplu:

```text
10.67.83.35
```

Server1 folosește această adresă.

---

## VLAN

VLAN-ul separă logic traficul la Layer 2.

În laborator:

```text
VLAN 10
```

---

## Access

Access înseamnă:

```text
Un singur VLAN
```

Exemplu:

```text
Laptop
   |
   ↓
Access Port
   |
VLAN 10
```

---

## Trunk

Trunk înseamnă:

```text
Mai multe VLAN-uri pot traversa legătura
```

În laborator este permis:

```text
VLAN 10
```

---

## 802.1Q

802.1Q este standardul folosit pentru identificarea VLAN-ului prin tagging pe trunk.

---

## Router

Routerul conectează rețele Layer 3 diferite și poate face routing.

---

## Switch

Switch-ul conectează dispozitive la Layer 2 și folosește MAC addresses pentru forwarding.

---

## Gateway

Gateway-ul este punctul către care hostul trimite traficul destinat altor rețele.

În laborator:

```text
10.67.83.30
```

---

## Router-on-a-Stick

Un singur port fizic al routerului poate avea mai multe sub-interfețe:

```text
Fa0/0
 ├── Fa0/0.10 → VLAN 10
 ├── Fa0/0.20 → VLAN 20
 └── Fa0/0.30 → VLAN 30
```

---

# 🧭 Imaginea mentală a laboratorului

Cel mai simplu mod de a înțelege laboratorul este să îl privești astfel:

```text
Laptop1
   |
   | Access VLAN 10
   |
Switch0
   |
   | Trunk
   |
Switch1
   |
   | Access VLAN 10
   |
Server1
```

În paralel, Switch0 are și legătura:

```text
Switch0
   |
   | Trunk
   |
Router1
```

Routerul are:

```text
Fa0/0.10
    |
    | VLAN 10
    |
10.67.83.30
```

---

# 🧩 De ce avem nevoie de VLAN?

Imaginează-ți că ai un switch cu 20 de calculatoare.

Fără VLAN-uri, toate pot aparține aceluiași broadcast domain.

Cu VLAN-uri poți avea:

```text
VLAN 10 → Accounting
VLAN 20 → IT
VLAN 30 → HR
```

Chiar dacă toate dispozitivele sunt conectate fizic la același switch, ele pot fi separate logic.

---

# 🧩 De ce avem nevoie de trunk?

Imaginează-ți că:

```text
Switch0
```

are:

```text
VLAN 10
VLAN 20
VLAN 30
```

și trebuie să trimită toate aceste VLAN-uri către:

```text
Switch1
```

Nu vrei neapărat câte un cablu pentru fiecare VLAN.

Folosești un:

```text
TRUNK
```

care poate transporta traficul mai multor VLAN-uri.

---

# 🧩 De ce avem nevoie de Router-on-a-Stick?

Dacă avem:

```text
VLAN 10
```

și:

```text
VLAN 20
```

acestea sunt separate la Layer 2.

Pentru ca dispozitivele din VLAN 10 să comunice cu cele din VLAN 20, este nevoie de Layer 3 routing.

Router-on-a-Stick poate face:

```text
VLAN 10
   ↓
Router
   ↓
VLAN 20
```

---

# 📚 Rezumat rapid

| Concept           | Ce face                                          |
| ----------------- | ------------------------------------------------ |
| Router            | Face routing între rețele                        |
| Switch            | Conectează dispozitive la Layer 2                |
| VLAN              | Separă logic rețeaua                             |
| Access Port       | Transportă un singur VLAN                        |
| Trunk Port        | Transportă mai multe VLAN-uri                    |
| 802.1Q            | Identifică VLAN-ul prin tagging                  |
| Sub-interfață     | Interfață logică pentru un VLAN                  |
| Router-on-a-Stick | Routing între VLAN-uri printr-o interfață fizică |
| IP Address        | Adresă Layer 3                                   |
| MAC Address       | Adresă Layer 2                                   |
| Default Gateway   | Ieșirea către alte rețele                        |
| ICMP              | Protocol folosit de ping                         |
| HTTP              | Protocol pentru pagini web                       |
| Port 80           | Portul standard pentru HTTP                      |

---

# 📂 Structura proiectului

Proiectul poate fi organizat astfel:

```text
Packet-Tracer-VLAN-Lab/
│
├── Layer2-VLAN-Lab.pkt
│
└── README.md
```

Fișierul:

```text
Layer2-VLAN-Lab.pkt
```

conține topologia și configurațiile Packet Tracer.

Fișierul:

```text
README.md
```

conține documentația laboratorului.

---

# 🛠️ Cerințe

Este necesar:

```text
Cisco Packet Tracer 8.0 sau mai nou
```

---

# 🔗 Resurse

- Cisco Packet Tracer / NetAcad
- CCNA 200-301 learning resources
- TheKeithBarker — networking tutorials

---

# 📊 Status final

| Test                          | Status |
| ----------------------------- | ------ |
| VLAN 10 creat pe Switch0      | ✅      |
| VLAN 10 creat pe Switch1      | ✅      |
| Laptop1 în VLAN 10            | ✅      |
| Server1 în VLAN 10            | ✅      |
| Trunk Switch0 ↔ Switch1       | ✅      |
| Trunk Switch0 ↔ Router1       | ✅      |
| Router sub-interface Fa0/0.10 | ✅      |
| Laptop1 IP static             | ✅      |
| Server1 IP static             | ✅      |
| HTTP activ pe Server1         | ✅      |
| Ping Laptop1 → Server1        | ✅      |
| HTTP Laptop1 → Server1        | ✅      |

---

# 🎉 Concluzie

Laboratorul demonstrează cum pot fi conectate dispozitive folosind **VLAN-uri, switch-uri Layer 2, trunk-uri și un router configurat cu Router-on-a-Stick**.

Configurația finală este:

```text
VLAN 10
```

Laptop1:

```text
10.67.83.33/27
```

Server1:

```text
10.67.83.35/27
```

Router1 / Gateway:

```text
10.67.83.30/27
```

Serverul rulează:

```text
HTTP
```

iar Laptop1 poate accesa:

```text
http://10.67.83.35
```

Prin urmare, laboratorul este configurat cu succes. ✅

---

## 📌 Ideea principală de ținut minte

Dacă trebuie să reții doar fluxul de bază:

```text
DEVICE
   ↓
ACCESS PORT
   ↓
VLAN
   ↓
TRUNK
   ↓
ALT SWITCH
   ↓
ACCESS PORT
   ↓
DESTINAȚIE
```

iar atunci când trebuie să comunicăm între rețele/VLAN-uri:

```text
VLAN
   ↓
TRUNK
   ↓
ROUTER
   ↓
SUB-INTERFACE
   ↓
ROUTING
   ↓
ALTĂ REȚEA / ALT VLAN
```

Acestea sunt conceptele fundamentale pe care laboratorul este construit.