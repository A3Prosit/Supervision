
# Prosit 5 - Supervision


Animateur : Etienne

Secrétaire : PapyJohn

Gestionnaire : Happo

Scribe : Emi

## Mots clés :

- Supervision: technique de suivi et de pilotage. La supervision concerne l'acquisition de données(mesures, alarmes, retour d'état de fonctionnement) et des paramètres de commande des processus , elle est généralement confiée à des automates programmables
- Centreon: soft de supervision
- OID: 
- Environnement SAAS / ON-PREMISE: soft as a service: sur le cloud
- Métrologie: science de la mesure:  définit les principes et les méthodes permettant de garantir et maintenir la confiance envers les mesures résultant des processus de mesure
- SLA : Service Level Agreement: accords sur la qualité de service
- Monitoring: métrologie+ supervision
- Netflow: protocole cisco pour monitorer le trafic IP
- Compilateur MIBS: gui de snmp
- Concaténer
- WMI: partie dessus
- Fork: nouveau logiciel créé a partir du code source d'un logiiel existant
- ICMP: ping
- SNMP: cf partie
- pro-actif
- POC*

\'*' ⇒ A définir

## Contexte :

Quoi ?
- Mettre en place des solutions de supervision et de métrologie
Comment ?
- Utilisation de logiciels open source / propriétaires
Pourquoi ?
- Mesurer la disponibilité / perf. infrastructure

## Contraintes :

- Petit Pierre dit que non

## Problématique :

- Comment marche et mets-on en place une solution de supervision à travers plusieurs outils ?

## Généralisation :

- ~~Gestion~~
- ~~MCO~~ 
- Monitoring
- ~~Supervision~~
## Hypothèses :

- Il existe déjà de nombreux outils de monitoring
- Centreon est un outil de supervision qui marche par template
- On peut découper la supervision en cinq étapes : 
	- Av-vente
	- conseil
	- POC
	- Intégration
	- Out-sourcing
- Les outils de supervision ne sont pas compatibles sur tous les OS
- Chaque outil de supervision à son domaine
- Il faut combiner plusieurs outils pour obtenir 1 SI
- Il existe plusieurs niveaux de MIBs


## Plan d’action :

Études
### Définition des mots clés
### Supervision

#### Différents monitoring / Métrologie

supervision: consiste à indiquer et à commander l'état d'un appel, d'un système ou d'un réseau. Les solutions de supervision permettent de remonter des informations tchniques et foncionnelles du système d'information.

la supervision inclut plusieurs activités:
	- suveiller
	- visualiser
	- analyser
	- piloter
	- agir
	- alterer
En info on peut superviser :
- le rseau et ses équipements
- les serveurs
- les périphériques
- les applications
- le workflow
- les systèmes d'information
- assurer la disponibilité des services
- prévenir les défaillances
- détecter les anomalies
- regrouper les infos d'équipement hétérogènes
- automatiserles tâches
- aleter en cas d'interuption d'un service
- relancer les services interrompus

ce qu'on peut superviser:

réseau et matériel:
- switch et routeurs: disponibilité, interrogation des sondes (indicateurs), alertes
- serveurs: dispo, sondes matérielles, alertes
- onduleur: dispo, charge, état
- imprimantes: dispo, état, état des consommables
systèmes:
- switch: utilisation des ressources, métrologie
- serveur: ressources
applications et services:
- dispo
- cohérence des réponses aux interrogations
- perf

on distingue 2 types de supervision : 

La surveillance:
- Matérielle ( activité d'un équipement, charge, ...)
- Réseau (débit, latence, taux d'erreur, QoS, protocoles, sécurité)
- Système ( logs, perf, intégrité)
- Applicative (perf, modif de la config, analyse)

Les actions liées aux événements:
- enregistrement dans un journal
- tracé graphique
- alerte (mail, SMS,...)
- exécution de script pour automatiser un tâche

#### SNMP / SNMP Trap

Simple network managment protocol standard défini par l'IETF , RFC 1157

protocole de niveau applicatif. Il sait analyser les infos à tous les niveaux (physiques, réseaux, services, systèmes). Toutes les plateformes peuvent installer le service SNMP (windows, linux, cisco,...) mais personne ne l'active par défaut pour des raisons de sécu.

Il permet principalement de :

-   visualiser une quantité d'informations concernant le matériel, les connexions réseaux, leur état de charge ;
    
-   modifier le paramétrage de certains composants ;
    
-   alerter l'administrateur en cas d'évènements graves ;
    
-   et d'autres choses encore…

protocole: les agents de supervision communiquent avec le serv de 3 façon:
- requêtes SNMP (serv vers agent)
- réponses SNMP (agent vers serv)
- alarme (trap) agent vers serv quand un problème arrive port 162

![](pics/SNMP.jpg)

5 op:
get request
get next request
set request
get response
trap


snmpget permet de récupérer la valeur d’un OID feuille alors que snmpwalk permet de récupérer toutes les valeurs d’un OID « noeud »

installer SNMP sous Windows : "Ajout/Suppression de composants Windows" dans "Ajout/Suppression de programmes" dans le panneau de config. Cocher "SNMP(protocole simplifié de gestion réseau) puis "ok"

installer SNMP sous linux: déjà installé sur les serv Linux 
```
yum install net-snmp
```
modif ```/etc/snmp/snmpd.conf``` et mettre les bons volumes de disque

redémarrer le service
```
/etc/init.d/snmpd restart
```
verif que snmpd est lancé au démarrage
```
chkconfig --list | grep snmpd
```
si il ne l'est pas on l'y met
```
chkconfig snmpd on
```


install sur debian:

```
apt-get install snmpd snmp
```
CentOS:
```
yum install net-snmp net-snmpd-utils
```
sur CentOS il faut l'activer au démarage 
```
systemctl enable snmpd
```
on l'active sur les 2 avec
```
systemctl start snmpd
``` 
on verif qu'il est actif
```
systemctl status snmpd
```

on verif qu'il écoute avec "ss" qui liste les ports en écoute
```
ss -ulnp
```
u: liste les ports UDP uniquement
l: liste les ports 'listening'
n: affiche les numéros plutôt qe les noms
p: affiche le processus correspondant

on modif ensuite /etc/snmp/snmpd.conf

on commente :
```
agentAddress  udp:127.0.0.1:161
```
Puis ajouter :
```
agentAdrress udp:161
```
on met snmpd en écoute sur le port 161 en UDP pour l'IPv4

enfin on redémarre SNMP

```
systemctl restart snmpd
```

**config snmp sous CentOS 7**

/etc/snmp/snmpd.conf ligne 41 on change la community

les lignes 55 et 56 

```
view   systemview   included   .1.3.6.1.2.1
view   systemview   included   .1.3.6.1.2.1.25.1
```

décommenter les lignes 85,122,147,151

on redémarre snmpd 

**config sous debian**

/etc/snmp/snmpdconf

modif la community ligne 52
redémarrer snmpd

**tester**
iresoning pour visualiser graphiquement et getif
```
snmpwalk -v1 127.0.0.1 -c macommuity
```
```
snmpwalk -v <laversion> -c <lacommunaute> <adresseip> <oid>
```
la commande centreon
0 ok
1 warning
2 critical

sur windows
```
 snmpwalk.exe -r :@ip -os [oid] -op [autre oid] 
```

**Communautés**
SNMP fonctione par communauté, cad un groupe d'agents

Côté agents: 
- on créé une communauté publique (généralement nomée public) accessible à tous en lecture seule
- on créé une commu privée accessible en lecture/écriture protégée par mdp
Côté serveur:
on ajoute les hôtes et on indique les éléments de la MIB à surveiller


#### **MIB**
Management Information Base. 
Le protocole SNMB spécifie un bdd qui stocke des attributs classés dans un arbre. La MIB peut être normalisée ou spécifique
les OID sont numériques et globaux, on peut les comparer à un IPv6 les fabriquants ont des préfixes, chaque fabriquant a un gamme de produit (autre préfixe) etc.
Les OID peuvent vite être long et complexe pour un humain. Il existe donc une méthode pour traduire un OID numérique en format lisible, la carte de traduction est stockée dans un fichier texte appelé Managment Information Base (MIB)

Une MIB n'est pas nécssaire pour utiliser SNMP on effectuer des requêtes sur des périph SNMP mais sans la MIB  in sera difficile de comprendre ce que les donnés veulent dire

**OID** object identifier paire clé valeur unique remplies par l'agent SNMP pour que le manager puisse effectuer des requêtes


#### Historique des outils existants

IPMI protocole Inntel permettant de gérer la partie matérielle
- Nagios 
- Centreon 
- Shinken 
- Zabbix 
- Cacti (graphes) 
- Eyes of Network

solutions propriétaires pour grands entreprises:
- HP Open View
- IBM Tivoli monitoring
- Microsoft System Center

Solutions propriétaires pour PME:
- PRTG
- Netcrunch

Solutions propriétaires SaaS (cloud):
- Netvigie
- RG Vision
- Satelliz

solutions de monitoring :
- Nagios : logiciel libre sous licence GPL permettant la surveillance système et réseau
- Zabbix: open source sous licence GPL v2 permet de surveiller le statut de divers services réseau, serveurs et autre matériel réseau
- OpenNMS: outil open source de supervision/hypervision de réseau, dev en java et utilise des bdd postgreSQL pour fonctionner
- Shinken: libre sou licence GNU AGPL, compatible avec Nagios. Permet la surveillance système et réseau
- Centreon: progiciel open source, permet de surveiller l'ensemble des infrastructures et applications du système d'information

Monitoring système :
https://en.wikipedia.org/wiki/Comparison_of_network_monitoring_systems

surveillance applicative: 

on utilise des indicateurs généralement regroupés en trois grandes familles:
- disponibilité du service
- perf de l'appli
- intégrité des données

à part l'intégrité des données, la supervision applicative mesure donc la QoS en terme de perf et disponibilité

objectif: identifier et résoudre un problème de fourniture de service pour pouvoir agir plus rapidement



#### WMI

wmiSysInfos logiciel de récupération d'infos hardware et software de machine locale ou distante

windows manager instrumentation : implémentation microsoft de Web-Based Entreprise Managment(WBEM), le standard du Distributed Manasgment Task Force (DMTF), il prends en charge le moèdle Commin Information Model(CMI)qui décrit les objets d'un environement de gestion
Il permet à des scripts WSH de gérer Windows localement ou à distance, il est pré installé sur quasi tous les windows depuis 2000 (sais pas pour 2016)

WBEM: ensemble de techniques et de standards internet de gestion servant à unifier la gestion d'environement informatique distribués
#### SMI

Structure of management information: 

  
1                   iso
1.3                 org
1.3.6               dod
1.3.6.1             internet
1.3.6.1.1           directory
1.3.6.1.2           mgmt
1.3.6.1.2.1              mib-2
1.3.6.1.2.1.2.2.1.3 ifType
1.3.6.1.2.1.10        transmission
1.3.6.1.2.1.10.23   transmissionppp
1.3.6.1.2.1.27      application
1.3.6.1.2.1.28      mta
1.3.6.1.2.2         pib
1.3.6.1.3           experimental
1.3.6.1.4           private
1.3.6.1.4.1         enterprise
1.3.6.1.5           security
1.3.6.1.6           SNMPv2
1.3.6.1.6.1         snmpDomains
1.3.6.1.6.2         snmpProxys
1.3.6.1.6.3         snmpModules
1.3.6.1.7           mail
1.3.6.1.8           features

entreprises sous 1.3.6.1.4.1
https://www.iana.org/assignments/enterprise-numbers/enterprise-numbers

#### Enjeux supervision

Réalisation
- Le Workshop
- Le challenge

1er partie
snmpget -v2c -c public 127.0.0.2 1.3.6.1.4.1.2021.9.1.2.1
"/"
2eme 
sur l'interface centreon, dans poller, ressources 

http://192.168.74.135/centreon/main.php?p=60904
  
$CENTREONPLUGINS =  "/usr/lib/centreon/plugins"

3eme

on enlève un / pour mettre un / ....
/usr/lib/centreon/plugins
