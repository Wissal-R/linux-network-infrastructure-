# Infrastructure Réseau Multi-Services Linux

> **Projet académique** - Module Administration Réseaux  
> ENSA Khouribga | Janvier 2025  
> **Équipe de 4 personnes**

---

## Objectif

Déploiement d'une infrastructure réseau complète sous Ubuntu avec 8 services critiques :
- Services réseau : DHCP, DNS BIND9, LDAP OpenLDAP
- Services applicatifs : SSH, HTTP/Apache, FTP vsftpd, SMTP Postfix
- Supervision : Nagios 4.4.6

---

## Technologies

- **OS** : Ubuntu Server / Linux
- **Virtualisation** : VirtualBox
- **Réseaux** : 192.168.56.0/24, 192.168.33.0/24

**Services déployés :**
- **DHCP** : isc-dhcp-server (allocation IP automatique)
- **DNS** : BIND9 (résolution de noms - domaine iric1.com)
- **LDAP** : OpenLDAP/slapd (authentification centralisée - dc=ensakh,dc=com)
- **SSH** : OpenSSH (accès distant sécurisé)
- **HTTP** : Apache2 (serveur web)
- **FTP** : vsftpd (transfert de fichiers)
- **SMTP** : Postfix (relais mail)
- **Supervision** : Nagios 4.4.6 + Plugins 2.3.3

---

## Équipe & Répartition

**Équipe de 4 personnes**

| Membre | Services configurés |
|--------|---------------------|
| **Wissal RHANDI** | **DHCP + DNS BIND9 + LDAP OpenLDAP** |
| Farah Ressati | HTTP Apache + FTP vsftpd |
| Chaymae Rachid | SMTP Postfix + SSH |
| Ikram Mihfad | Nagios + Sécurité (UFW, ACLs) + Documentation |

---

## Ma Contribution Spécifique (Wissal RHANDI)

### Configuration DHCP (isc-dhcp-server)

**Mise en place du service d'allocation IP automatique :**

- Installation et configuration `isc-dhcp-server`
- **Réseau** : 192.168.56.0/24
- **Plage DHCP** : 192.168.56.40 - 192.168.56.60
- **Serveur DNS** : 8.8.8.8 (Google DNS)
- **Passerelle** : Configuration routeur par défaut
- Tests validation : clients obtiennent IP automatiquement

**Fichier configuré :** `/etc/dhcp/dhcpd.conf`

### Configuration DNS BIND9

**Déploiement serveur de noms pour le domaine iric1.com :**

- Installation et configuration BIND9
- **Domaine** : iric1.com
- **Zone directe** : Résolution nom → IP
- **Zone inverse** : Résolution IP → nom
- **Enregistrements DNS** :
  - A (Address) : noms d'hôtes → adresses IP
  - PTR (Pointer) : adresses IP → noms d'hôtes
  - NS (Name Server) : serveurs de noms autoritaires

**Tests réalisés :**
- `nslookup` : validation résolution de noms
- `dig` : requêtes DNS détaillées
- Tests zones directe et inverse

**Fichiers configurés :**
- `/etc/bind/named.conf.local` (déclaration zones)
- `/etc/bind/db.iric1.com` (zone directe)
- `/etc/bind/db.192.168.56` (zone inverse)

### Configuration LDAP OpenLDAP

**Déploiement annuaire centralisé pour authentification :**

- Installation `slapd` (serveur LDAP) + `ldap-utils`
- **Organisation** : dc=ensakh,dc=com
- **Arborescence LDAP** :
```
  dc=ensakh,dc=com
  ├── ou=users (utilisateurs)
  └── ou=groups (groupes)
```

- **Fonctionnalités** :
  - Authentification centralisée pour tous les services
  - Gestion utilisateurs/groupes unifiée
  - Intégration avec SSH pour auth LDAP

**Tests réalisés :**
- `ldapsearch` : requêtes sur l'annuaire
- Connexion SSH avec comptes LDAP
- Validation authentification centralisée

**Fichiers configurés :**
- `/etc/ldap/slapd.conf` (configuration serveur)
- Schémas LDAP personnalisés

### Intégration & Tests

- **Intégration DHCP ↔ DNS** : Clients DHCP enregistrés automatiquement dans DNS
- **Intégration LDAP ↔ Services** : Auth centralisée pour SSH, FTP, etc.
- **Tests bout-en-bout** : Validation stack complète
- Documentation des procédures de configuration

---

## Architecture Réseau
```
┌─────────────────────────────────────┐
│      Réseau 192.168.56.0/24         │
├─────────────────────────────────────┤
│                                     │
│  ┌──────────┐  ┌──────────┐        │
│  │   DHCP   │  │   DNS    │        │
│  │  Server  │  │  BIND9   │        │
│  │          │  │ iric1.com│ ← Wissal
│  │ Wissal   │  │  Wissal  │        │
│  └────┬─────┘  └────┬─────┘        │
│       │             │              │
│       └──────┬──────┘              │
│              │                     │
│      ┌───────▼────────┐            │
│      │  LDAP OpenLDAP │            │
│      │ dc=ensakh,dc=com│ ← Wissal  │
│      │                │            │
│      └───────┬────────┘            │
│              │ Auth                │
│    ┌─────────┴──────────┐          │
│    │                    │          │
│ ┌──▼───┐  ┌────▼────┐  ┌▼────┐   │
│ │ SSH  │  │ HTTP/   │  │ FTP │   │
│ │      │  │ Apache  │  │     │   │
│ └──────┘  └─────────┘  └─────┘   │
│                                    │
│         ┌──────────┐              │
│         │  Nagios  │              │
│         │  Monitor │              │
│         └──────────┘              │
└────────────────────────────────────┘
```

---

## Services Déployés

**Ma partie (Wissal) :**
- ✅ DHCP : Allocation automatique IP (plage .40-.60)
- ✅ DNS BIND9 : Résolution domaine iric1.com (zones directe/inverse)
- ✅ LDAP OpenLDAP : Authentification centralisée (dc=ensakh,dc=com)

**Partie équipe :**
- HTTP/Apache (serveur web, Virtual Hosts)
- FTP vsftpd (transfert fichiers)
- SMTP Postfix (relais mail)
- SSH OpenSSH (accès distant)
- Nagios 4.4.6 (supervision proactive)

**Sécurité (équipe) :**
- Pare-feu UFW configuré
- ACLs sur services sensibles
- Authentification centralisée LDAP

---

## Documentation

**[Rapport de projet complet (PDF)](docs/rapport-projet.pdf)**

*Le rapport (26 pages) détaille toutes les configurations, commandes utilisées, fichiers modifiés, et tests de validation.*

---

## Compétences Acquises

**Services réseau :**
- DHCP (isc-dhcp-server, allocation IP dynamique)
- DNS BIND9 (zones, enregistrements A/PTR/NS)
- LDAP OpenLDAP (annuaire, authentification centralisée)

**Administration Linux :**
- Ubuntu Server (installation, configuration)
- Services daemon (systemctl, configuration)
- Fichiers de configuration (/etc/)
- Debugging (logs, journalctl)

**Sécurité :**
- Authentification centralisée (LDAP)
- Intégration PAM (Pluggable Authentication Modules)
- Sécurisation services réseau

**Réseaux :**
- Architecture IP (sous-réseaux, plages)
- Résolution DNS (forward/reverse)
- Intégration multi-services

**Méthodologie :**
- Travail en équipe (4 personnes)
- Documentation technique
- Tests et validation

---

## Contact

**Wissal RHANDI**
- 📧 wissalrhandi685@gmail.com
- 💼 [LinkedIn](https://linkedin.com/in/wissalrhandi)
- 📍 Khouribga, Maroc
