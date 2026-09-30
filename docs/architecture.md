# Architecture

## Vue d'ensemble

Le projet est conçu comme une petite infrastructure hybride :

```text
Internet
   |
   v
Oracle Cloud VPS
   |
   | WireGuard
   |
   v
Home Server - Ubuntu
   |
   +-- Docker
       +-- Nextcloud
       +-- Immich
       +-- Jellyfin
       +-- autres services
```

## Principe

Le VPS possède une adresse IPv4 publique et constitue le point d'entrée Internet.

Le serveur domestique reste derrière la box. Le trafic applicatif destiné aux services publiés doit traverser le tunnel WireGuard.

L'objectif est donc de ne pas créer une redirection de port séparée pour Nextcloud, Immich, Jellyfin, etc. sur la box.

## Séparation des rôles

### VPS

- Point d'entrée public.
- Reverse proxy.
- Terminaison HTTPS.
- Firewall.
- Tunnel WireGuard.
- Éventuellement mécanismes de blocage et de journalisation.

### Serveur domestique

- Hébergement des applications.
- Docker.
- Stockage des données.
- Sauvegardes.
- Accès LAN.
- Client WireGuard vers le VPS.

## Flux cible

Exemple pour Nextcloud :

```text
Client Internet
      |
      | HTTPS
      v
VPS Oracle
      |
      | WireGuard
      v
Serveur Ubuntu
      |
      | réseau Docker / port local
      v
Nextcloud
```

Le VPS ne doit pas avoir un accès inutile à l'ensemble du LAN. Les flux seront progressivement limités aux services nécessaires.

## Principes de sécurité

1. Minimiser les ports exposés publiquement.
2. Utiliser WireGuard pour le lien Cloud / Home.
3. Filtrer les flux avec un firewall.
4. Ne jamais publier de secrets dans Git.
5. Documenter les règles réseau.
6. Garder les services non nécessaires inaccessibles depuis Internet.
