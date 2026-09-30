# Déploiement

## Principe

Le déploiement est réalisé progressivement afin de pouvoir identifier facilement les problèmes.

## Étape 1 — VPS

Vérifier :

```bash
hostname
ip addr
ip route
sudo ss -lntup
```

## Étape 2 — SSH

Vérifier l'authentification par clé :

```bash
ssh ubuntu@<IP_VPS>
```

Puis vérifier la configuration SSH :

```bash
sudo sshd -T
```

## Étape 3 — WireGuard

Vérifier :

```bash
sudo wg
sudo ss -lunp | grep 51820
```

Tester le tunnel depuis le client.

## Étape 4 — Firewall

Activer UFW seulement après avoir vérifié qu'une session SSH de secours est disponible.

Configuration de base :

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw default allow routed
```

Puis ajouter uniquement les ports nécessaires.

Vérifier :

```bash
sudo ufw status verbose
```

## Étape 5 — Routage

Vérifier :

```bash
ip route
sysctl net.ipv4.ip_forward
```

## Étape 6 — Reverse proxy

Le reverse proxy sera installé sur le VPS.

Objectif :

```text
https://nextcloud.example
        |
        v
Reverse Proxy
        |
        | WireGuard
        v
192.168.50.210:<port>
```

Le port réel du service sera documenté après déploiement.

## Étape 7 — DNS

Les noms de domaine devront pointer vers l'adresse publique du VPS.

Exemple :

```text
nextcloud.example.com -> VPS public IP
immich.example.com    -> VPS public IP
```

## Étape 8 — HTTPS

Le reverse proxy devra fournir les certificats TLS et rediriger HTTP vers HTTPS lorsque cela sera configuré.

## Étape 9 — Tests

Pour chaque service :

```text
DNS
  |
HTTPS
  |
Reverse Proxy
  |
WireGuard
  |
Home Server
  |
Docker
  |
Application
```

Chaque niveau doit être testé séparément.

## Règle de déploiement

Ne jamais modifier plusieurs couches simultanément sans possibilité de retour arrière.

Après chaque modification importante :

```text
configuration
    |
test
    |
validation
    |
documentation
```
