# OpenVPN

OpenVPN est utilisé sur le serveur domestique pour accéder ponctuellement à un NAS distant.

## Configuration

Fichier :

```text
/etc/openvpn/client/VPN_save.ovpn
```

Lancement manuel :

```bash
sudo openvpn --config /etc/openvpn/client/VPN_save.ovpn --daemon
```

## Route

Le fichier de configuration contient :

```text
remote vpn-save.example 1194
route 192.168.50.250 255.255.255.255
```

Le nom réel du domaine n'est pas publié dans ce dépôt.

## Fonctionnement

Le VPN est lancé par un script de sauvegarde.

Le script :

1. vérifie les dossiers locaux ;
2. lance OpenVPN ;
3. attend la connexion ;
4. vérifie le NAS ;
5. utilise rsync via SSH ;
6. arrête OpenVPN.

## NAS

Adresse :

```text
192.168.50.250
```

Les synchronisations concernent notamment :

```text
Dossier A
Dossier B
```

## Coexistence avec WireGuard

OpenVPN et WireGuard peuvent fonctionner simultanément car ils servent des objectifs différents.

Le point important est le routage :

```text
192.168.50.250
    |
    +--> OpenVPN


10.100.0.0/24
    |
    +--> WireGuard Oracle


10.30.30.0/24
    |
    +--> WireGuard accès distant maison
```

Le système doit conserver des routes cohérentes pour éviter les conflits.

## Attention

Le script actuel utilise :

```bash
sudo pkill openvpn
```

Cela peut arrêter toute instance OpenVPN.

Une amélioration future est de gérer OpenVPN avec une unité systemd dédiée ou un PID spécifique.
