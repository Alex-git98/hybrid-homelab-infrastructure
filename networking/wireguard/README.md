# WireGuard

Le projet utilise plusieurs tunnels WireGuard indépendants.

## 1. VPN domestique

Ce WireGuard est utilisé pour l'accès distant au réseau domestique.

Réseau :

```text
10.13.13.0/24
```

Serveur :

```text
10.13.13.1
```

Le serveur tourne dans un conteneur LinuxServer WireGuard.

Configuration :

```text
/home/alex/docker/wireguard/config
```

Le conteneur expose :

```text
51820/udp
```

La box redirige un port public vers le port WireGuard du serveur domestique.

## 2. Tunnel Oracle

Un second WireGuard est installé directement sur le VPS Oracle.

Réseau :

```text
10.50.0.0/24
```

VPS :

```text
10.50.0.1
```

Serveur domestique :

```text
10.50.0.2
```

Le VPS écoute actuellement sur :

```text
51820/udp
```

## Vérification du VPS

```bash
sudo wg
```

Exemple d'informations utiles :

```text
interface: wg0
public key: ...
listening port: 51820

peer:
allowed ips: 10.50.0.2/32
latest handshake: ...
transfer: ...
```

Ne jamais publier les clés privées.

## Routage

Le tunnel Oracle doit permettre au VPS d'atteindre uniquement les services nécessaires du serveur domestique.

L'objectif final est de ne pas donner au VPS un accès complet au LAN si cela n'est pas nécessaire.

## Dépannage

```bash
sudo wg
sudo ss -lunp | grep 51820
ip route
sudo iptables -L -n -v
sudo iptables -t nat -S
sudo ufw status verbose
```

## Bonnes pratiques

- Une clé différente par peer.
- AllowedIPs aussi restrictifs que possible.
- Ne pas utiliser `0.0.0.0/0` côté serveur pour un simple tunnel de services.
- Vérifier le handshake.
- Vérifier ensuite le routage et le firewall.
