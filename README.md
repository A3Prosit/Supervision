
# Prosit 5 - Supervision

Animateur : Etienne

Secrétaire : PapyJohn

Gestionnaire : Happo

Scribe : Emi

## Mots clés :

- Supervision* : Surveillance du bon fonctionnement d'un système ou d'une activité.
- Centreon* : Logiciel de supervision informatique édité par Centron
- OID* : Object Identifier, standard pour nommer un objet, correspond à un noeud dans le MIB
- Environnement SAAS / ON-PREMISE
- Métrologie* : Sceince des mesure, principe et méthodes permettant de garantir et maintenir la la confiance envers les mesures.
- SLA*: Document qui définit la QoS
- Monitoring* : Surveillance 
- Netflow* : Caractéristique introduite par les CISCO routers, qui permet de récupérer du trafic IP dans entrées/sorties d'une interface
- Compilateur MIBS* : Quand une application utilise SNMP ou NMS? le loigiciel qui fait le parsing avec le MIB s'appelle le compilateur MIBS
- Concaténer
- WMI* : Système de gestion interne de windows qui prend en charge la surveillance et le contrôle de ressource systèmes via un ensemble d'interface
- Fork* : Process qui crée une copie de sois-même via un appel système
- ICMP* : Internet Control Message Protocole , utilisé pour véhiculer des messages de contrôle d'erreur
- SNMP* : Simple Network Management Protocol
- pro-actif* : Anticipation de comportements sur situations.
- POC* : Proof Of Concept - Réalisation expérimentale, concrète et préliminaire, courte ou incomplète, pour démontrer la faisabilité.

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
- Définition des mots clés
- Supervision
	- Différents monitoring / Métrologie
	- SNMP / SNMP Trap
	- MIB
	- Historique des outils existants
	- WMI
	- SMI
	- Enjeux supervision

Réalisation
- Le Workshop
- Le challenge


## Supervision :

### Monitoring & Métrologie

- **Monitoring** : Surveiller à un instant t
	-	Revient à connaître son état actuel 
	-	Revient à connaître son historique passé
	-	Utilise les valeurs (UP/DOWN) et de données chiffrées (%)
	-	Vérifier si disponible
	-	Alerte si pas bon
- **Métrologie** : Étudier
	- Revient à garder et tracer la valeur numérique d'une charge (ex : % CPU, Nb personnes connectés)
	- Permet de tracer des graphique entrant / sortant 
	- Permet de tracer l'évolution dans le temps grâce à des métriques

![](http://www.it-connect.fr/wp-content-itc/uploads/2015/09/S%C3%A9lection_005.png)

- **On peut surveiller :**
	- Matériel (activité d'un équipement, charge...)
	- Réseau (Débit, latence, taux d'erreur, QoS, protocole, sécurité...)
	- Système (logs, performances, intégrité)
	- Applicative (performance, modifications de configurations, analyse)

- **Quality Of Service QoS :**
	- Disponiblité du service
	- Performance de l'application
	- Intégrité des données

### SNMP - Simple Network Management

- Protocole qui permet aux admin réseaux de gérer les équipements et diagnostiquer les problèmes
- Standard définit par l'IETF - RFC 1157
- Utilisé par de nombreuses applications de management

**Fonctionnement** :
- Périphérique fait tourner un **agent SNMP** (processus daemon) qui réponds aux requêtes réseau
	- Fournit des OID (clé/valeur) en les remplissant
- Un **Manager SNMP** effectue des requêtes aux agents sur les OID
- 'Community Name' = Mot de passe en clair pour autoriser lecture / écriture OID
	- Par défaut 'public' (non sécurisé)
- Ports 161 [Agent] et 162[Manager]
- Fonctionne avec le MIB (**Management Information Base**)
- Polling : Envoyer une requête à invervalles régulières pour obtenir des valeurs - vérification "Active"
- Traps : L'agent SNMP contacte un autre SNMP (ex : routeur) en cas de problème pour qu'il envoi une trap vers le trap host. On éffectue alors un traitement via un **trap handler**

![](https://upload.wikimedia.org/wikipedia/commons/thumb/2/26/SNMP_communication_principles_diagram.PNG/500px-SNMP_communication_principles_diagram.PNG)

**Versions :**
- V1 - 1990
- V2c (classic) - 1993
- V2 et V3 - On évolués, sont maintenant les standards - 2002

**Utilisations :** 
- snmpwalk : Obtenir des informations par lots
- snmpget : Obtenir des infos sur un OID précis

**Le protocole en lui même :**

![](http://irp.nain-t.net/lib/exe/fetch.php/215snmp:snmp2.png)

- get-request : Le Manager SNMP demande une information à un agent SNMP
- get-next-request : Le Manager SNMP demande l'information suivante à l'agent SNMP
- set-request : Le Manager SNMP met à jour une information sur un agent SNMP
- get-reponse : L'agent SNMP répond à un get-request ou a un set-request
- trap : L'agent SNMP envoie une alarme au Manager

### MIB - Management Information Base 

- MIB est un fichier qui va être un arbre
- La racine n'est pas de nom
- Chaque branche est un OID
- Sert de carte de traduction pour lire lisiblement
- On retrouve deux types d'objets :
	- Scalaires (Unique)
	- Tableau :  Quand plusieurs objets qui sont liés

### Historiques outils existants :

Outils :
- Nagios
	- Surveillance système & réseau
	- Surveille les hôtes et services
	- Peut alerter
	- Logiciel libre sous license GPL
- Zabbix
	- Logiciel Open source
	- Surveiller les status des services réseau / serveur {...}
	- License GPL v2
	- Fait parti des solutions majeures de supervision libres
- OpenNms
	- Outils de supervision / hypervision
	- Open source
	- Java - S'appuie sur moteur applicatif Jetty + BDD PostgreSQL
- Shinken 
	- Comme Nagios (compatible avec)
	- Logiciel libre sous license GNU AGPL
- Centron : 
	- Open Source de la suite Centreon
	- Superviser ensemble infrastructures & applications composant du SI

### WMI - Windows Management Instrumentation

- Implémentation de Microsoft du WBEM (Web-Based Entreprise Management)
- standard du DMTF (Distributed Management Task Force)
- Système de gestion interne de windows qui prend en charge la surveillance et le contrôle de ressource systèmes via un ensemble d'interface
- Fournit un modèle cohérant et organisé des états de Window
- Permet à des scripts WSH de gérer Windows localement ou à distnace
- Grâce à lui que fonctionne 'Propriétés systèmes' de Windows


### SMI - Structure of Management Information
- Strucutre standardisé
	- Définit les types de données (Integer, Integer32...)
	- Mappage de la structure binaire
- Permet à SNMP de communiquer avec tous les équipements

### Enjeux et ouverture :

- S'assurer que tout fonctionne avec de bonnes **performances** (ressenti utilisateur)
	- Lancer des scénarios, et si temps de lancement anormal ⇒ Alerte
- Outils permettant de remplir la fonction de supervision de ressenti utilisateur :
![](https://wiki.monitoring-fr.org/_media/supervision/eue/stack3.png?w=600&tok=b7e037)
- Cucumber : Permet aux responsables fonctionnels d'écrire comment le logiciel doit se comporter (langage naturel)
- Watir : Librairire - Automatisation exécution actions dans un navigateur 
- Webdriver : Permet de piloter virtuellement des navigateur
- Sikulli : Permet exécuter des scénarios de tests basés sur des pattern graphique à l'aide librairie openCV
- Cucumber-Nagios : Plugin Nagios qui permet d'exécuter les tests de Cucumber


### Annexes :
- Paquets : snmp snmpd
- Centreon ⇒ /usr/lib/nagios/plugins
- loadkeys fr ⇒ Mettre en FR

### Challenge

Etape 1 : 
'/' <==> 1.3.6.1.4.1.2021.9.1.2.1

Etape 2 : 
/usr/lib/centreon/plugins <==> $CENTREONPLUGINS$

Etape 3 : 
usr/lib/centreon/plugins/



### Self-Check

⇒ V3 SNMP : Identification des parties qui communiquent + assurance conversation privé
Cette sécurité est basée sur 2 concepts :
-   USM (User-based Security Model)
-   VACM (View- based Access Control Model)

⇒ Fork Nagios : Shinken / Icinga / Naeman
⇒ Outil qui ne correspond pas à un outil de monitoring : OCS Inventory NG
⇒ Mauvaise pratique à ne pas appliquer lors d'un projet de supervision : Attendre la fin d'un projet pour sensibiliser et former les utilisateur de la solution de supervision.
⇒ Rien à voir avec la supervision GLPI
⇒ SNMP : Simple Network Management Protocol 
⇒ Elements directement impactant sur les performances d'un serveur de supervision : Poller + Scheduler
==> snmpwal : 
	- Parcourir une MIB
	- Utilise la communauté et la v. du protocole SNMP pour fonctionner
	- Parcourir une BDD normalis
==> NSClient++ == Windows + Linux
=> /etc/snmp/snmpd.conf
