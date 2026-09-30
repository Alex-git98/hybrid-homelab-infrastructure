# Troubleshooting

Ce document conserve les problèmes réellement rencontrés pendant la construction de l'infrastructure.

## WireGuard : interface déjà existante

Commande :

```bash
sudo wg-quick up wg0
```

Message :

```text
wg-quick: `wg0' already exists
```

Diagnostic :

```bash
sudo wg
```

Si `wg0` est déjà présente et que le service fonctionne, il n'est pas nécessaire de lancer une seconde fois l'interface.

## Vérifier l'écoute WireGuard

```bash
sudo ss -lunp | grep 51820
```

Résultat attendu :

```text
0.0.0.0:51820
[::]:51820
```

## Vérifier le handshake

```bash
sudo wg
```

Chercher :

```text
latest handshake
```

Un handshake récent indique que le peer a réussi à établir le tunnel cryptographique.

Attention : un handshake réussi ne garantit pas que le routage applicatif fonctionne.

## Vérifier le trafic

```bash
sudo wg
```

Observer :

```text
transfer: ... received, ... sent
```

Les compteurs permettent de vérifier si du trafic circule réellement.

## Vérifier le firewall

```bash
sudo ufw status verbose
sudo ufw show added
sudo iptables -S
sudo iptables -L -n -v
```

## Vérifier le forwarding

```bash
sysctl net.ipv4.ip_forward
```

Valeur attendue pour le routage :

```text
net.ipv4.ip_forward = 1
```

## Vérifier le NAT

```bash
sudo iptables -t nat -S POSTROUTING
```

Le tunnel Oracle utilise actuellement une règle de masquerading pour le réseau :

```text
10.100.0.0/24
```

## Vérifier les routes

```bash
ip route
```

Pour un diagnostic plus précis :

```bash
ip route get <adresse-ip>
```

## Méthode de diagnostic

Toujours progresser dans cet ordre :

```text
1. Interface VPN
        |
2. Handshake
        |
3. Route
        |
4. Firewall
        |
5. NAT
        |
6. Service cible
```

Cela permet d'éviter de modifier plusieurs composants en même temps.
