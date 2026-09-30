# 🏠 Hybrid Homelab Infrastructure

Infrastructure personnelle hybride **Cloud / On-Premise** conçue pour expérimenter l'administration systèmes, les réseaux, la cybersécurité, la conteneurisation et les services cloud.

Le projet combine un serveur Ubuntu domestique avec une infrastructure publique hébergée sur **Oracle Cloud Infrastructure (OCI)**.

L'objectif principal est de pouvoir rendre certains services accessibles depuis Internet **sans exposer directement le réseau domestique**.

---

## 🎯 Objectifs

* Concevoir une infrastructure hybride Cloud / On-Premise
* Héberger des services conteneurisés avec Docker
* Sécuriser l'accès aux services exposés sur Internet
* Mettre en place un tunnel VPN chiffré entre le Cloud et le réseau domestique
* Centraliser le point d'entrée Internet sur un VPS
* Mettre en place un reverse proxy
* Maintenir les services internes hors d'accès direct depuis Internet
* Automatiser les sauvegardes
* Superviser les ressources et les flux réseau
* Documenter l'architecture et les procédures de dépannage

---

# 🏗️ Architecture

```text
                         INTERNET
                             │
                             ▼
                  ┌─────────────────────┐
                  │    Oracle Cloud     │
                  │        VPS          │
                  │                     │
                  │  Public IP          │
                  │  WireGuard          │
                  │  Reverse Proxy      │
                  │  UFW                │
                  └──────────┬──────────┘
                             │
                    WireGuard tunnel
                       encrypted
                             │
                             ▼
                  ┌─────────────────────┐
                  │    Home Server      │
                  │      Ubuntu         │
                  │                     │
                  │      Docker         │
                  │                     │
                  │  ┌───────────────┐  │
                  │  │   Nextcloud   │  │
                  │  ├───────────────┤  │
                  │  │    Immich     │  │
                  │  ├───────────────┤  │
                  │  │    Jellyfin   │  │
                  │  └───────────────┘  │
                  └──────────┬──────────┘
                             │
                       LAN 192.168.1.0/24
                             │
                    ┌────────┴────────┐
                    │                 │
                 NAS local        Other devices
```

---

# 🖥️ Infrastructure

## Cloud

**Oracle Cloud Infrastructure**

* Ubuntu Server
* VM.Standard.A1.Flex
* 2 OCPU
* 12 GB RAM
* Public IPv4 réservée
* VCN dédiée
* WireGuard
* UFW
* Reverse proxy

## On-Premise

**Home Server**

* Ubuntu Server
* Docker
* Docker Compose / Stacks
* LAN : `192.168.50.0/24`
* Serveur : `192.168.50.210`

---

# 🌐 Réseau

Le réseau domestique reste derrière la box Internet.

Aucun service applicatif n'est directement exposé depuis la box.

Le VPS Oracle constitue le point d'entrée public.

```text
Internet
   │
   ▼
Oracle VPS
   │
   │ WireGuard
   ▼
Home Server
   │
   └── Docker services
```

---

# 🔐 Sécurité

Les principales mesures de sécurité mises en place sont :

* Firewall UFW sur le VPS
* Politique par défaut `deny incoming`
* SSH limité aux sources autorisées
* WireGuard utilisé pour le tunnel Cloud ↔ Home
* Services internes non directement exposés
* Reverse proxy utilisé comme point d'entrée applicatif
* Authentification SSH par clé
* Désactivation de l'authentification SSH par mot de passe
* Sauvegardes automatisées
* Séparation des flux VPN
* Fail2ban sur Oracle pour limite le brute force
* IP bloqué hors france

Les clés privées, mots de passe, tokens et certificats sensibles ne sont jamais stockés dans ce dépôt.

---

# 🔑 VPN

Deux architectures VPN sont utilisées.

## WireGuard

Utilisé pour :

**Oracle Cloud ↔ Home Server**

Le tunnel permet au VPS d'accéder aux services autorisés du réseau domestique sans ouvrir de nouveaux ports applicatifs sur la box.

## OpenVPN

Utilisé ponctuellement pour accéder à un NAS distant lors des sauvegardes.

Le VPN NAS n'est pas utilisé comme tunnel Internet général.

Une route spécifique est utilisée vers le NAS distant :

```text
192.168.50.250/32
```

---

# 📦 Services

Les principaux services hébergés sur le serveur domestique sont :

| Service      | Fonction            |
| ------------ | ------------------- |
| Nextcloud    | Cloud personnel     |
| Immich       | Gestion de photos   |
| Jellyfin     | Serveur multimédia  |
| Homarr       | Dashboard           |
| Portainer    | Gestion Docker      |
| AdGuard      | DNS / filtrage      |
| WireGuard    | VPN                 |
| Dashdot      | Monitoring système  |


---

# 💾 Sauvegardes

Les données importantes sont sauvegardées vers différents supports.

Le projet comprend notamment :

* sauvegarde des configurations Docker
* sauvegarde des données applicatives
* sauvegarde vers NAS
* synchronisation automatisée vers un NAS distant
* utilisation de scripts Bash pour automatiser certaines opérations

Exemple :

```text
Home Server
     │
     ├── Dossier-A
     └── Dossier-B
           │
           ▼
       OpenVPN
           │
           ▼
      NAS distant
           │
          rsync
```

---

# 🛠️ Administration

Les opérations d'administration sont principalement réalisées en SSH.

Les outils utilisés comprennent notamment :

```text
Linux
Bash
SSH
Docker
Docker Compose
Git
WireGuard
OpenVPN
iptables
UFW
systemd
```

---

# 🔎 Troubleshooting

Une partie importante du projet consiste à documenter les problèmes rencontrés et leur résolution.

Exemple :

### WireGuard — handshake absent

Diagnostic :

```bash
sudo wg
sudo ss -lunp | grep 51820
ip route
sudo iptables -L FORWARD -n -v
```

Vérifications effectuées :

* port UDP ouvert côté Cloud
* port WireGuard à l'écoute
* IP forwarding activé
* règles NAT
* règles FORWARD
* configuration du peer
* routage

Cette documentation permet de conserver une trace des choix techniques et des problèmes réellement rencontrés.

---

```# 📈 Roadmap

### Infrastructure

* [x] Serveur Ubuntu
* [x] Docker
* [x] Services auto-hébergés
* [x] VPN WireGuard domestique
* [x] VPS Oracle Cloud
* [x] IP publique réservée
* [x] WireGuard sur le VPS
* [x] UFW sur le VPS
* [ ] Tunnel VPS → Home Server
* [ ] Reverse Proxy
* [ ] Publication de Nextcloud
* [ ] Publication d'Immich

### Sécurité

* [x] SSH par clé
* [x] Firewall VPS
* [x] Réduction de la surface d'exposition
* [ ] Protection du reverse proxy
* [ ] Fail2ban / mécanisme de blocage
* [ ] Centralisation des logs
* [ ] Monitoring
* [ ] Alerting

### Automatisation

* [x] Scripts Bash
* [x] Sauvegardes automatisées
* [ ] Automatisation du déploiement
* [ ] Infrastructure as Code
* [ ] CI/CD pour certaines configurations
```
---

# 📚 Compétences mises en pratique

Ce projet permet de mettre en pratique :

### Systèmes

* Administration Linux
* Gestion des services
* SSH
* Bash
* systemd
* Gestion des permissions

### Réseaux

* TCP/IP
* Routage
* NAT
* VPN
* WireGuard
* OpenVPN
* DNS
* Firewalling

### Cloud

* Oracle Cloud Infrastructure
* VCN
* VNIC
* Instances Compute
* IP publique
* Security Lists
* Monitoring

### Conteneurisation

* Docker
* Docker Compose
* Docker Networks
* Volumes
* Gestion des services

### Cybersécurité

* Réduction de la surface d'attaque
* Segmentation réseau
* Firewall
* VPN
* Authentification SSH par clé
* Gestion des accès
* Sauvegardes
* Journalisation

---

# 👨‍💻 Projet personnel

Ce projet est développé et documenté progressivement dans le cadre de mon apprentissage de l'administration systèmes, réseaux et cybersécurité.

L'objectif n'est pas uniquement d'héberger des services, mais de comprendre et documenter leur fonctionnement, leur sécurisation, leur supervision et leur maintenance.
