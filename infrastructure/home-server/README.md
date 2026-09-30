# Home Server

## Système

Serveur :

```text
Ubuntu Server
```

Adresse LAN :

```text
192.168.50.210
```

Passerelle :

```text
192.168.50.254
```

## Docker

Docker est utilisé pour héberger les différents services.

Le serveur contient plusieurs réseaux Docker.

Exemple :

```bash
docker network ls
```

Les configurations sont stockées sous :

```text
/home/alex/docker/
```

## Services

Services principaux :

- Nextcloud
- Immich
- Jellyfin
- Homarr
- Portainer
- AdGuard
- WireGuard
- Dashdot
- File Browser

## WireGuard domestique

Le serveur possède un conteneur WireGuard LinuxServer.

Configuration principale :

```text
/home/alex/docker/wireguard/config
```

Le réseau VPN domestique est :

```text
10.30.30.0/24
```

Le serveur WireGuard utilise :

```text
10.30.30.1
```

Ce VPN permet l'accès distant au LAN.

## WireGuard Oracle

Le serveur devra également participer au tunnel WireGuard avec le VPS Oracle.

Ce tunnel est indépendant du VPN domestique.

Réseau :

```text
10.100.0.0/24
```

## OpenVPN

Le serveur utilise également OpenVPN ponctuellement pour accéder à un NAS distant.

Configuration :

```text
/etc/openvpn/client/VPN_save.ovpn
```

L'objectif est uniquement d'atteindre :

```text
192.168.50.250
```

## Important

Les fichiers contenant des clés privées ou des secrets ne doivent jamais être copiés dans GitHub.

La documentation doit utiliser des exemples anonymisés.
