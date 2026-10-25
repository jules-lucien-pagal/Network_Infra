### Network_Infrastructure ###

🔧 -- Infrastructure Réseaux - Sécurité - Systèmes --- | Cisco | Palo Alto | Fortigate | Ubuntu Server |
- Niveau : Intermédiaire/Avancé

Dans ce lab, il est question de déployer une infrastructure LAN & WAN 
- Site A & Site B communiquent via le Backbone MPLS [Flux Master]
- Site A & Site B communiquent via un tunnel VPN IPSec [Flux Secondaire] en cas de souci au sein du Backbone.  Le traffic est ainsi redirigé vers le tunnel VPN établit entre Palo Alto & Fortinet.


🧩 Contexte technique

- OSPF utilisé comme IGP sur la partie Lan avec PBR
- MP-BGP , iBGP unicast et VPNv4 utilisés sur la partie Wan pour le peering entre routeurs PEs du Backbone [Peering sur les Loopbacks] + [LDP , LSP , Traffic Engineering , ..]
- OSPF & BGP Gracefull-restart/NSR
- ISIS utilisé comme IGP inter-PEs au coeur du Backbone pour garantir la connectivité Inter-PEs
- Redistribution bidirectionnelle entre OSPF ↔ BGP pour assumer l'échanger des routes
- eBGP utilisé sur la partie Wan pour le peering CEs↔PEs afin d'éviter les redistributions supplémentaires
- VRFs , RT , RD pour instancier et labeliser le trafic venant des différents Clients CEs

Les Users ont accès à Internet via Palo Alto et la matrice de flux est définie comme suit : 
Users -> SW_Coeur -> Proxy -> Palo -> ISP -> Internet
La communicatio entre un User du Site A et Site B via le BACKBONE MPLS est définit comme suit :
Site A : Users -> SW_Coeur -> CE -> PEs [Backbone]   <---> Site B : CE -> SW_Coeur -> Users ; Et inversement

Sur le Site A :
- Les 2 pare-feux en aval sont en cluster Active/Passive pour une redondance HA et le filtrage in-out
- Les routeurs ISP simulent les interco vers les ISP via les IP publiques
- Les Switchs WAN segmentent le flux in-out entre l'intérier et l'extérieur en ne laissant passer que les Vlans autorisées à sortir 
- Le SW_Core fait de l'inter-vlan routing
- Le Switch DMZ porte les Serveurs DMZ
- 2 PCs [Windows & Linux] sont intégrés au Domaine AD
-Le Pare-feu Palo Alto [PA-FW-DMZ] sert à protéger les Serveurs dans la DMZ,  le protocole ICMP a été bloqué vers la DMZ pour éviter le mappage sur ce tronçon.
- Les Serveurs dans la DMZ ont accès à Internet et le filtrage est plus strict !
Seuls les services autorisés (Web , Proxy , Tacacs+ , NTP) sont autorisés à passer le Firewall DMZ
- Les DNAT sont configurés pour joindre les Serveurs Internes à partir de l'extérieur
- Le Serveur Tacacs+ sur Ubuntu Server via une solution Open Source gère l'authentification des Network devices
- Le Serveur Proxy reposera sur une solution Open Source
- Le Serveur NTP repose sur la solution Open Source Chrony
- Le Serveur Web repose sur Apache 
- Tous les Serveurs DMZ tournent sur Linux et seul le Serveur AD tourne sur Windows avec (DNS , DHCP , Redirecteurs DNS pour la sortie Internet)

Sur le Site B :
- Le pare-feu Fortinet pour le filtrage in-out
- Les routeurs ISP simule l'interco vers l'ISP via l'IP publique
- Les Switchs WAN segmentent le flux in-out entre l'intérier et l'extérieur en ne laissant passer que les Vlans autorisées à sortir 
- Le SW_Core fait de l'inter-vlan routing
