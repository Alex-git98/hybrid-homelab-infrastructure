# Réseau

## Réseau domestique

LAN :

```text
192.168.50.0/24
```

Serveur Ubuntu :

```text
192.168.50.210
```

Passerelle domestique :

```text
192.168.50.254
```

## Réseau WireGuard Oracle

Le tunnel WireGuard du VPS utilise actuellement :

```text
10.100.0.0/24
```

VPS :

```text
10.100.0.1
```

Client / serveur domestique :

```text
10.100.0.2
```

Ces valeurs correspondent au tunnel VPS et ne doivent pas être confondues avec le VPN domestique existant.

## VPN domestique existant

Le serveur Ubuntu possède également un serveur WireGuard utilisé pour permettre aux appareils personnels d'accéder au LAN à distance.

Réseau :

```text
10.30.30.0/24
```

Adresse du serveur WireGuard :

```text
10.30.30.1
```

Ce VPN est indépendant du tunnel WireGuard vers Oracle.

## OpenVPN vers le NAS distant

Un second VPN, basé sur OpenVPN, est utilisé ponctuellement pour atteindre un NAS distant.

NAS distant :

```text
192.168.50.250
```

Le fichier OpenVPN contient une route spécifique vers cette adresse :

```text
route 192.168.50.250 255.255.255.255
```

Il ne s'agit donc pas d'un VPN Internet global.

## Routage

Le serveur possède une route par défaut vers la box :

```text
default via 192.168.50.254
```

Le tunnel Oracle ajoute ses propres routes pour le réseau WireGuard.

## Objectif de routage

Les trois usages doivent rester indépendants :

```text
WireGuard maison
10.30.30.0/24
        |
        +--> accès distant au LAN


WireGuard Oracle
10.100.0.0/24
        |
        +--> VPS <-> serveur maison


OpenVPN NAS
        |
        +--> 192.168.50.250
```

Le routage devra être vérifié après chaque modification avec :

```bash
ip route
ip rule
sudo wg show
```
