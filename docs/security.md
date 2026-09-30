# Sécurité

## Objectif

Le principe général est de réduire au maximum la surface d'exposition Internet.

Le serveur domestique ne doit pas devenir directement accessible depuis Internet simplement parce qu'un service Docker est installé.

## VPS

Le VPS utilise UFW.

Configuration actuelle :

```text
Default: deny incoming
Default: allow outgoing
Default: allow routed
```

Règles actuellement configurées :

```text
SSH : 22/tcp depuis une adresse IP autorisée
WireGuard : 51820/udp
HTTP : 80/tcp
HTTPS : 443/tcp
WireGuard interface : trafic autorisé selon les règles UFW
SSH depuis le réseau WireGuard : 10.100.0.0/24
```

Vérification :

```bash
sudo ufw status verbose
sudo ufw show added
```

## SSH

L'accès SSH doit rester limité.

Le serveur utilise l'authentification par clé et non par mot de passe.

Vérification :

```bash
sudo sshd -T | grep -E '^(port|permitrootlogin|pubkeyauthentication|passwordauthentication)'
```

Ne jamais publier de clé privée SSH.

## WireGuard

Le tunnel utilise des clés cryptographiques.

Vérification :

```bash
sudo wg
```

Informations utiles :

- clé publique du serveur
- peers
- AllowedIPs
- endpoint
- dernier handshake
- volume de données transférées

Les clés privées ne doivent jamais être publiées.

## Oracle Cloud

Le firewall Linux n'est pas la seule couche de sécurité.

Oracle Cloud possède également des règles réseau au niveau de la VCN / subnet / VNIC.

Le modèle retenu est donc :

```text
Internet
   |
   v
OCI Network Security
   |
   v
UFW
   |
   v
Services
```

## Principe de moindre privilège

Lors de la publication des services, les règles doivent être limitées autant que possible :

- seuls les ports nécessaires sont ouverts ;
- seuls les réseaux nécessaires sont routés ;
- les services internes ne sont pas exposés directement ;
- l'administration passe par SSH / VPN.

## Secrets

Ne jamais versionner :

```text
*.key
*.pem
*.ovpn
.env
PrivateKey
PresharedKey
passwords
tokens
API keys
certificates privés
```

Utiliser des exemples anonymisés dans la documentation.
