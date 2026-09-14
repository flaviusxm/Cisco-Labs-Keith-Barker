# 🌐 Packet Tracer Lab — Layer 2 Switching & VLANs

Laborator realizat în **Cisco Packet Tracer** pentru a înțelege practic VLAN-urile, switch-urile Layer 2, access/trunk ports și configurarea unui router folosind **Router-on-a-Stick**.

Scopul este ca **Laptop1** să poată comunica cu **Server1** și să acceseze pagina web:

```text

http://10.67.83.35

```

Topologia folosește un singur VLAN:

```text

VLAN 10

```

Laptop1 și Server1 sunt în același VLAN și în aceeași subrețea:

```text

10.67.83.0/27

```

Pentru a transporta VLAN 10 între dispozitive folosim trunk-uri, iar Router1 este configurat cu o sub-interfață pentru VLAN 10.

# Topologie

```text

Laptop1

|

| Access VLAN 10

|

Switch0

|\

| \ Trunk

| \

| Router1

|

| Trunk

|

Switch1

|

| Access VLAN 10

|

Server1

```

# Configurare

1. Configurarea Switch0

Mai întâi am creat VLAN-ul 10:

```text

enable

configure terminal

vlan 10

name VLAN10

exit

```

Laptop1 este conectat la `Fa0/1`, așa că portul este configurat ca **access port**:

```text

interface fastEthernet 0/1

switchport mode access

switchport access vlan 10

no shutdown

exit

```

Legătura către Router1 este `Gi0/1`, iar cea către Switch1 este `Gi0/2`. Ambele trebuie să transporte VLAN 10, deci sunt configurate ca **trunk**:

```text

interface gigabitEthernet 0/1

switchport mode trunk

switchport trunk allowed vlan 10

no shutdown

exit

interface gigabitEthernet 0/2

switchport mode trunk

switchport trunk allowed vlan 10

no shutdown

exit

```

Astfel, Switch0 primește traficul de la Laptop1 în VLAN 10 și îl poate transmite mai departe prin trunk.

2. Configurarea Switch1

Și pe Switch1 trebuie să existe VLAN 10:

```text

enable

configure terminal

vlan 10

name VLAN10

exit

```

Server1 este conectat la `Fa0/1`, deci îl punem în VLAN 10:

```text

interface fastEthernet 0/1

switchport mode access

switchport access vlan 10

no shutdown

exit

```

Portul `Gi0/1`, conectat la Switch0, este trunk:

```text

interface gigabitEthernet 0/1

switchport mode trunk

switchport trunk allowed vlan 10

no shutdown

exit

```

Acum VLAN 10 poate traversa legătura:

```text

Switch0 ↔ Switch1

```

3. Configurarea Router1

Router1 este conectat la Switch0 prin `FastEthernet0/0`.

Mai întâi activăm interfața:

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

`encapsulation dot1Q 10` îi spune routerului că sub-interfața este asociată cu:

```text

VLAN 10

```

IP-ul `10.67.83.30` devine **default gateway-ul** dispozitivelor din această rețea.

Aceasta este configurația de **Router-on-a-Stick**: un singur port fizic al routerului poate avea sub-interfețe pentru VLAN-uri diferite.

## 4. Configurarea Laptop1

Pe:

```text

Laptop1 → Desktop → IP Configuration

```

am setat IP static:

```text

IP Address: 10.67.83.33

Subnet Mask: 255.255.255.224

Default Gateway: 10.67.83.30

```

Laptop1 este conectat la un access port din VLAN 10.

## 5. Configurarea Server1

Pe:

```text

Server1 → Desktop → IP Configuration

```

am setat:

```text

IP Address: 10.67.83.35

Subnet Mask: 255.255.255.224

Default Gateway: 10.67.83.30

```

Apoi am activat serverul web:

```text

Server1 → Services → HTTP → ON

```

Serverul poate fi accesat la:

```text

http://10.67.83.35

```

# Cum circulă traficul?

Când Laptop1 trimite un request către Server1:

```text

Laptop1

10.67.83.33

↓

Access Port / VLAN 10

↓

Switch0

↓

Trunk / VLAN 10

↓

Switch1

↓

Access Port / VLAN 10

↓

Server1

10.67.83.35

```

Ambele dispozitive sunt în aceeași rețea:

```text

10.67.83.0/27

```

și același VLAN, deci pentru comunicarea directă dintre ele **routerul nu este necesar pentru rutare**.

Routerul este prezent pentru a demonstra configurarea de **default gateway și Router-on-a-Stick**, iar într-o topologie cu mai multe VLAN-uri ar putea face routing între acestea.

# Testarea

După configurare, am verificat conectivitatea de pe Laptop1:

```text

ping 10.67.83.35

```

```text

Reply from 10.67.83.35

```

înseamnă că dispozitivele pot comunica.

Apoi, în:

```text

Desktop → Web Browser

```

am accesat:

```text

http://10.67.83.35

```

Dacă pagina apare, înseamnă că și serviciul HTTP funcționează.

# Comenzi de verificare

Pe switch-uri:

```text

show vlan brief

```

verifică VLAN-urile și porturile access.

```text

show interfaces trunk

```

verifică trunk-urile și VLAN-urile permise.

```text

show interfaces status

```

verifică starea porturilor.

Pe router:

```text

show ip interface brief

```

verifică starea interfețelor și sub-interfețelor.

```text

show ip route

```

afișează tabela de rutare.

```text

show running-config

```

afișează configurația curentă.

