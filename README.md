<div align="center">

# 🛡️ Conception et Sécurisation d'une Infrastructure Réseau avec Supervision SIEM

### De l'architecture « flat » à un réseau segmenté, filtré et supervisé en temps réel

![Statut](https://img.shields.io/badge/Statut-Soutenu%20le%2018%2F05%2F2026-success?style=for-the-badge)
![Type](https://img.shields.io/badge/Type-PFE%20%2F%20DUT-blue?style=for-the-badge)
![Domaine](https://img.shields.io/badge/Domaine-R%C3%A9seaux%20%26%20Cybers%C3%A9curit%C3%A9-critical?style=for-the-badge)

![Sophos](https://img.shields.io/badge/Firewall-Sophos%20XG-0072C6?style=flat-square&logo=sophos&logoColor=white)
![Wazuh](https://img.shields.io/badge/SIEM-Wazuh%204.9.2-3595F0?style=flat-square&logoColor=white)
![EVE-NG](https://img.shields.io/badge/Simulation-EVE--NG-orange?style=flat-square)
![VMware](https://img.shields.io/badge/Virtualisation-VMware%20Workstation-607078?style=flat-square&logo=vmware&logoColor=white)
![Ubuntu](https://img.shields.io/badge/Serveur-Ubuntu%2024.04%20LTS-E95420?style=flat-square&logo=ubuntu&logoColor=white)
![Kali](https://img.shields.io/badge/Tests-Kali%20Linux-557C94?style=flat-square&logo=kalilinux&logoColor=white)
![Cisco](https://img.shields.io/badge/R%C3%A9seau-VLAN%20%7C%20NAT%20%7C%20Port%20Security-1BA0D7?style=flat-square&logo=cisco&logoColor=white)
![LaTeX](https://img.shields.io/badge/Rapport-LaTeX-008080?style=flat-square&logo=latex&logoColor=white)

**[📄 Consulter le rapport complet (PDF)](./SFE.pdf)**

</div>

---

## 📑 Sommaire

1. [Contexte et problématique](#-contexte-et-problématique)
2. [Objectifs principaux](#-objectifs-principaux)
3. [Architecture et technologies utilisées](#️-architecture-et-technologies-utilisées)
4. [Méthodologie et réalisations](#-méthodologie-et-réalisations)
5. [Résultats et démonstrations](#-résultats-et-démonstrations)
6. [Limites et perspectives](#-limites-et-perspectives)
7. [Rapport de stage](#-rapport-de-stage)
8. [Auteurs et remerciements](#-auteurs-et-remerciements)

---

## 📌 Contexte et problématique

Ce projet de fin d'études a été réalisé à l'**École Supérieure de Technologie de Dakhla** (Université Ibnou Zohr), filière **Gestion des Réseaux et Sécurité**, au sein d'une **annexe de la Région Dakhla-Oued Eddahab** (service Transport et Logistique), durant l'année universitaire 2025/2026.

Face à la montée des cybermenaces visant les institutions publiques, l'infrastructure réseau de l'annexe présentait des faiblesses structurelles :

- **Architecture « flat »** : les différentes divisions (division sociale, division personnel et logistique) partageaient un seul réseau local, sans segmentation ni contrôle d'accès ;
- **Propagation latérale** : un poste compromis pouvait atteindre l'ensemble du réseau ;
- **Aucune supervision** : pas de collecte ni d'analyse des événements de sécurité, donc aucune capacité de détection d'anomalies ou de tentatives d'intrusion.

> **Problématique** : comment concevoir et mettre en place une architecture réseau sécurisée permettant d'isoler efficacement les différentes divisions, de contrôler les flux de communication et d'améliorer la détection des menaces au sein des annexes de la région ?

---

## 🎯 Objectifs principaux

- 🔎 **Analyser** l'architecture existante et identifier ses principales faiblesses ;
- 🧱 **Segmenter** le réseau en VLAN pour isoler les divisions et les serveurs critiques ;
- 🔥 **Filtrer** les flux avec un pare-feu de nouvelle génération (NGFW) et des règles ACL fondées sur le principe du moindre privilège ;
- 📡 **Superviser** l'infrastructure grâce à un SIEM centralisant les journaux des équipements et des postes ;
- 🧪 **Valider** l'efficacité du dispositif par des scénarios d'attaque simulés (reconnaissance, accès non autorisé) ;
- ⚖️ **Préserver** les performances et la disponibilité du réseau malgré le durcissement.

---

## 🛠️ Architecture et technologies utilisées

### Vue d'ensemble

L'architecture adopte une **topologie hiérarchique** et une stratégie de **défense en profondeur** autour de trois axes :

| Axe | Rôle | Moyen mis en œuvre |
|---|---|---|
| **Isolation structurelle** | Cloisonner les flux et réduire les domaines de broadcast | Segmentation en VLAN (802.1Q), plan d'adressage VLSM |
| **Contrôle périmétrique et granulaire** | Filtrer le trafic entrant, sortant et inter-VLAN | Pare-feu Sophos XG, règles ACL, NAT, DHCP centralisé |
| **Intelligence de sécurité** | Détecter, corréler et alerter | SIEM Wazuh (agents + Syslog), HIDS, FIM, MITRE ATT&CK |

Le pare-feu constitue le **point de contrôle central** : il assure le routage inter-VLAN, le filtrage, le NAT vers Internet et la distribution DHCP. Il est relié à un routeur simulant le fournisseur d'accès (ISP). Un switch central distribue le trafic vers des switches d'accès par VLAN.

### Segmentation réseau

| VLAN | Rôle | Sous-réseau | Particularités |
|---|---|---|---|
| **VLAN 10** | Division Sociale | `192.168.17.0/26` | Accès Internet web (HTTP/HTTPS), blocage vers les autres VLAN |
| **VLAN 20** | Division Personnel & Logistique | `192.168.17.64/26` | Même logique que le VLAN 10, isolation étendue |
| **VLAN 30** | Serveurs critiques (SIEM) | `192.168.17.136/29` | Réception des logs Syslog, protection renforcée |
| **VLAN 100** | Réseau d'attaque (simulation) | — | Existe uniquement dans la maquette, pour les tests de sécurité |

Le plan d'adressage a été construit en **VLSM** : sous-réseaux `/26` pour les divisions (50 hôtes chacune), `/29` pour les serveurs, `/30` pour la liaison pare-feu ↔ routeur, et `/24` pour le réseau d'administration et le réseau externe simulé.

### Pile technologique

| Catégorie | Technologie | Usage dans le projet |
|---|---|---|
| Pare-feu NGFW | **Sophos Firewall (XG)** | Segmentation, ACL, NAT, DHCP, journalisation |
| SIEM | **Wazuh 4.9.2** (Manager, Indexer, Dashboard) | Collecte, corrélation, détection d'intrusion, intégrité des fichiers |
| Simulation réseau | **EVE-NG** | Émulation de la topologie complète |
| Virtualisation | **VMware Workstation** | Hébergement de la plateforme et des machines virtuelles |
| Systèmes | **Ubuntu 24.04 LTS**, **Kali Linux** | Serveur SIEM, agent supervisé et poste d'attaque |
| Réseau | Équipements **Cisco** émulés | VLAN, NAT/PAT, routage statique, Port Security |
| Outils de test | **Nmap**, **Telnet**, **Netcat** | Scans de reconnaissance et tentatives d'accès |
| Documentation | **draw.io**, **TeXstudio (LaTeX)** | Schémas, topologie et rédaction du rapport |

---

## 🔍 Méthodologie et réalisations

Le projet s'est déroulé du **1ᵉʳ avril au 14 mai 2026** (environ six semaines), selon un planning en phases :

| Phase | Période | Contenu |
|---|---|---|
| 1. Analyse des besoins | 1 – 5 avril | Objectifs, exigences fonctionnelles et non fonctionnelles, contraintes |
| 2. Conception | 6 – 10 avril | Architecture, segmentation VLAN, choix du firewall et du SIEM |
| 3. Déploiement virtuel | 11 – 20 avril | Maquette EVE-NG, configuration des équipements, premiers tests de connectivité |
| 4. Configuration et sécurisation | 21 avril – 2 mai | Firewall, règles de filtrage, mécanismes de protection |
| 5. Intégration des services | 2 – 9 mai | Déploiement du SIEM et collecte des logs |
| 6. Tests et validation | 9 – 12 mai | Scénarios d'attaque et vérification des politiques |
| Rédaction du rapport | 1ᵉʳ avril – 14 mai | En parallèle de toutes les phases |

### Étapes clés

**1. Conception et choix techniques**
Analyse fonctionnelle (acteurs : administrateur, utilisateurs, serveurs, ISP, attaquant simulé), définition des flux normaux et des flux d'attaque, puis rédaction de la politique de sécurité (segmentation, contrôle d'accès, filtrage, journalisation).
Un pare-feu **Fortinet** avait d'abord été retenu, mais les restrictions de licence en environnement de simulation ont conduit à une **réorientation vers Sophos XG**, plus adapté à EVE-NG.

**2. Sécurisation des équipements réseau**
- Création des VLAN, affectation des ports en mode *access* et *trunk* ;
- **Port Security** (adresse MAC limitée, apprentissage « sticky », désactivation du port en cas de violation) pour contrer les accès non autorisés et le MAC flooding ;
- Protection des accès au switch (console, mode privilégié) et **chiffrement des mots de passe** dans la configuration ;
- Routeur ISP : NAT/PAT et route statique par défaut.

**3. Configuration du pare-feu Sophos XG**
- Interfaces VLAN rattachées à un port en trunk, avec un serveur **DHCP dédié** par segment ;
- Création d'**objets réseau** par VLAN pour des règles lisibles et réutilisables ;
- Restriction de l'accès à la console d'administration (*Device Access*) par zone ;
- **Règles ACL** par VLAN : accès Internet limité au web, DNS, DHCP, flux Wazuh autorisés, **blocage inter-VLAN** avec journalisation activée.

**4. Déploiement du SIEM Wazuh**
- Installation sur un serveur Ubuntu 24.04 placé dans le VLAN 30 (indexeur, manager et tableau de bord) ;
- **Double stratégie de collecte** : agents Wazuh sur les postes (ports 1514 et 1515 ouverts via le firewall) et **Syslog sans agent** pour le routeur, les switches et le pare-feu, avec liste blanche des adresses sources ;
- Résolution d'un **incident de compatibilité de versions** entre l'agent et le manager (alignement de l'agent sur la version du serveur), puis enregistrement réussi de l'agent.

**5. Validation par simulation d'attaques**
Depuis une machine attaquante interne et un point externe, tests de reconnaissance et d'accès sur le serveur SIEM et sur les services d'administration.

---

## 📊 Résultats et démonstrations

### Livrables

- ✅ Architecture réseau **segmentée en quatre VLAN**, avec plan d'adressage VLSM documenté ;
- ✅ **Pare-feu Sophos XG** opérationnel : interfaces VLAN, DHCP par segment, NAT, politique ACL par VLAN ;
- ✅ **SIEM Wazuh** fonctionnel dans le VLAN des serveurs, avec tableau de bord centralisé ;
- ✅ **Collecte de logs** via agent (poste Kali, version 4.9.2) et via Syslog (équipements réseau) ;
- ✅ **Maquette complète sous EVE-NG** reproduisant la topologie cible ;
- ✅ Rapport technique de **112 pages** détaillant la conception, la configuration et la validation.

### Tests de sécurité réalisés

| Scénario | Outil | Résultat observé | Enseignement |
|---|---|---|---|
| Scan du réseau interne depuis la machine attaquante | Nmap | Aucun hôte protégé détecté | Cloisonnement inter-VLAN efficace |
| Scan depuis un attaquant externe (WAN) | Nmap | Aucun hôte ni service exposé | Filtrage WAN correct |
| Scan ciblé du serveur SIEM | Nmap | Hôte vu comme inactif | Serveur invisible aux attaques de reconnaissance |
| Tentative SSH non autorisée vers le SIEM | SSH | Connexion bloquée | ACL de protection du VLAN 30 validées |
| Accès aux ports sensibles (SSH, HTTPS) depuis l'extérieur | Telnet, Netcat | Connexions refusées | Console d'administration du firewall non exposée |

### Vue d'ensemble du SIEM

Le tableau de bord Wazuh affiche l'agent actif (Kali Linux, version 4.9.2), la répartition des alertes par gravité (aucune alerte critique ou élevée sur la période observée), ainsi que les modules **Configuration Assessment**, **Malware Detection**, **Vulnerability Detection**, **MITRE ATT&CK** et **conformité** (PCI DSS, HIPAA, NIST). L'outil *Discover* permet d'explorer les événements bruts, par exemple la remontée de l'arrêt d'un agent.

> 💡 **Captures d'écran** : la topologie, les tableaux de bord Wazuh et les configurations du pare-feu sont illustrés dans le [rapport complet](./SFE.pdf).

---

## 🚧 Limites et perspectives

Compte tenu de la durée du stage (six semaines), les tests ont porté sur la **validation des configurations de base et de la connectivité sécurisée**. Une analyse de vulnérabilités approfondie et des simulations d'attaques complexes n'ont pas pu être menées.

Pistes d'amélioration identifiées dans le rapport :

- 🛑 Intégration d'un **système de prévention d'intrusion (IPS)** ;
- 🔐 Mise en place d'une **authentification forte** ;
- 💾 Ajout de solutions de **sauvegarde** et de **haute disponibilité** ;
- 🤖 Automatisation de la réponse aux incidents (**SOAR**) ;
- 🔬 Poursuite de l'analyse de sécurité avancée et optimisation des politiques du SIEM et du pare-feu, sous réserve de l'accord de l'organisme d'accueil.

> ℹ️ Le VLAN 100 et les machines d'attaque n'existent que dans l'environnement virtuel de test, et les adresses IP citées sont celles de la maquette.

---

## 📄 Rapport de stage

Le mémoire complet (112 pages) détaille l'étude fonctionnelle, les choix techniques, les configurations pas à pas, les captures de validation et la bibliographie.

<div align="center">

### 👉 [📥 Consulter / Télécharger le rapport complet (PDF)](./SFE.pdf)

*Mémoire de Stage de Fin d'Études, DUT Gestion des Réseaux et Sécurité, année universitaire 2025/2026*

</div>

---

## 👥 Auteurs et remerciements

### Auteurs

| Nom | Rôle |
|---|---|
| **FARAH Loubna** | Étudiante, DUT Gestion des Réseaux et Sécurité, EST Dakhla |
| **SAHIB Moad** | Étudiant, DUT Gestion des Réseaux et Sécurité, EST Dakhla |

Soutenance le **18 mai 2026**, devant le jury composé de **Pr. Samya Bouhaddour** et **Pr. Asmae Yassine** (MC, Université Ibnou Zohr, EST Dakhla).

### Encadrement et remerciements

- **Pr. Youssef EL MARZAK**, tuteur de stage, pour sa disponibilité, ses conseils et son suivi rigoureux ;
- **M. Ayoub JIBOUNI**, coordinateur de filière, pour le suivi de la formation ;
- **M. Zouhir MAHANI**, directeur de l'École Supérieure de Technologie de Dakhla, pour son soutien et les moyens mis à disposition ;
- **La Région Dakhla-Oued Eddahab**, organisme d'accueil, pour le cadre de travail offert ;
- L'ensemble des **enseignants**, des **membres du jury**, ainsi que nos **familles et proches** pour leur soutien tout au long du parcours.

---

<div align="center">

⭐ *Si ce projet vous est utile ou vous inspire, n'hésitez pas à lui laisser une étoile.*

</div>
