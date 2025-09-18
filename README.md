                                         |||||||||||||||||||||                 ||||||||||||||||||||||              ||||||||||||||||||||||                    

                                                           
                                                          Este, README, foi desenvolvido para todos aqueles amantes de redes, 
                                                          este tem como objectivo incentivar a metodologia IAC/Netdvops, 
                                                          Autor: ELBER PEDRO  
               
                                         |||||||||||||||||||||                 ||||||||||||||||||||||              ||||||||||||||||||||||                  


# Ansible_TCC

# Ansible_automations

Plano de endereçamento: 

Links ponto-a-ponto (/30)

___________________________________________________________________________

    Link	            Rede	             Lado A	                Lado B                               
ASA ↔ CORE1	      10.255.0.0/30	    ASA 10.255.0.1	       CORE1 10.255.0.2
ASA ↔ CORE2	      10.255.0.4/30	    ASA 10.255.0.5	       CORE2 10.255.0.6
CORE1 ↔ DIST1	    10.255.0.8/30	    CORE1 10.255.0.9	     DIST1 10.255.0.10
CORE2 ↔ DIST2	    10.255.0.12/30	  CORE2 10.255.0.13	     DIST2 10.255.0.14
CORE1 ↔ DIST4	    10.255.0.16/30	  CORE1 10.255.0.17	     DIST4 10.255.0.18
CORE2 ↔ DIST3	    10.255.0.20/30	  CORE2 10.255.0.21	     DIST3 10.255.0.22

Nota: 1. Links ponto-a-ponto (/30)

Usei /30 (255.255.255.252) porque:

Dá 2 IPs válidos (um para cada lado do link).

É perfeito para links entre roteadores/switches de camada 3.

____________________________________________________________________________   ___________________________________________________________________________                                                                                                                                                                                                                      
Loopbacks /32

ASA router-id: 10.255.255.1

CORE1 Lo0 10.255.255.2,  CORE2 Lo0 10.255.255.3

DIST1 Lo0 10.255.255.11, DIST2 Lo0 10.255.255.12

DIST3 Lo0 10.255.255.13, DIST4 Lo0 10.255.255.14

VLANs /24 (SVIs onde indicado)

VLAN10 → DIST1: 10.10.10.1/24

VLAN20 → DIST4: 10.10.20.1/24

VLAN30 → DIST2: 10.10.30.1/24

VLAN40 → DIST3: 10.10.40.1/24

VLAN50 → DIST3: 10.10.50.1/24

Nota: Loopbacks (/32)

Foram utilizados como Switches core em OSPF para monitoramento.

São /32 porque representam 1 IP apenas.

Nunca mudam de estado (diferente das interfaces físicas que podem cair).

Exemplo: 2. Loopbacks (/32)

Servidores (VLAN10):

Ubuntu DNS/DHCP/Ansible: 10.10.10.2/24

Zabbix: 10.10.10.3/24

WebServer (opcional): 10.10.10.4/24

DHCP reserva/gateway: 10.10.10.1 (SVI DIST1)

____________________________________________________________________________   ___________________________________________________________________________    

enable
conf t
hostname ciscoasa

! --------- Interfaces ---------
interface Ethernet1
 nameif Internet
 security-level 0
 ip address dhcp setroute
 no shut

interface Ethernet0
 nameif IsptecSwCore1
 security-level 100
 ip address 10.255.0.1 255.255.255.252
 no shut

interface Ethernet2
 nameif IsptecSwCore2
 security-level 100
 ip address 10.255.0.5 255.255.255.252
 no shut

! --------- OSPF (ASA 9.1.x) ---------
router ospf 1
 router-id 10.255.255.1
 default-information originate always
 ! ativa OSPF pelas redes das interfaces internas
 network 10.255.0.0 255.255.255.252 area 0   ! Eth0/CORE1
 network 10.255.0.4 255.255.255.252 area 0   ! Eth2/CORE2
exit

! --------- NAT inside -> Internet ---------
object network ANY-IN-CORE1
 subnet 0.0.0.0 0.0.0.0
 nat (IsptecSwCore1,Internet) dynamic interface
!
object network ANY-IN-CORE2
 subnet 0.0.0.0 0.0.0.0
 nat (IsptecSwCore2,Internet) dynamic interface

end
copy run start

____________________________________________________________________________   ___________________________________________________________________________    

Teste Para ver as rotas do OSSPF 

show ospf neighbor
show ospf interface
show route | inc 0.0.0.0|10.10.
show nat
show xlate

packet-tracer input IsptecSwCore1 tcp 10.10.10.10 12345 8.8.8.8 80

CORE1 (IOSv L3)

Interfaces (ajusta os Gi conforme o teu EVE):

Eth0/0 ↔ ASA (10.255.0.2/30)
Eth0/1 ↔ DIST1 (10.255.0.9/30)
Eth1/0 ↔ DIST4 (10.255.0.17/30)

____________________________________________________________________________   ___________________________________________________________________________    

enable
conf t
hostname CORE1

interface Loopback0
 ip address 10.255.255.2 255.255.255.255

interface GigabitEthernet0/0
 desc to-ASA
 ip address 10.255.0.2 255.255.255.252
 no shut
interface GigabitEthernet0/1
 desc to-DIST1
 ip address 10.255.0.9 255.255.255.252
 no shut
interface GigabitEthernet1/0
 desc to-DIST4
 ip address 10.255.0.17 255.255.255.252
 no shut

router ospf 1
 router-id 10.255.255.2
 passive-interface default
 no passive-interface GigabitEthernet0/0
 no passive-interface GigabitEthernet0/1
 no passive-interface GigabitEthernet1/0
 network 10.255.255.2 0.0.0.0 area 0
 network 10.255.0.0 0.0.0.3 area 0
 network 10.255.0.8 0.0.0.3 area 0
 network 10.255.0.16 0.0.0.3 area 0
end
wr



                                                                                               


