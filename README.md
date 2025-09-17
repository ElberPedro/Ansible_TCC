                                                     |||||||||||||||||||||                 ||||||||||||||||||||||              ||||||||||||||||||||||           ||||||||||||||||||||||          ||||||||||||||||||||||
                                                           
                                                        Este, README, foi desenvolvido para todos aqueles amantes de redes, este tem como objectivo incentivar a metodologia IAC/Netdvops, 
                                                        Autor: ELBER PEDRO  
               
                                                     |||||||||||||||||||||                 ||||||||||||||||||||||              ||||||||||||||||||||||           ||||||||||||||||||||||          ||||||||||||||||||||||



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

___________________________________________________________________________                                                                                                                       ___________________________________________________________________________
                                                                                                   ___________________________________________________________________________

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

___________________________________________________________________________                                                                                                                       ___________________________________________________________________________
                                                                                               ___________________________________________________________________________






                                                                                               


