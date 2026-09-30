# Oracle Cloud VPS

## Instance

Le projet utilise une instance Oracle Cloud Infrastructure.

Configuration actuelle :

- Région : `eu-paris-1`
- OS : Ubuntu
- Shape : `VM.Standard.A1.Flex`
- CPU : 2 OCPU
- RAM : 12 GB
- VCN : `vps-lab-vcn-01`
- Réseau VCN : `10.0.0.0/24`
- Instance : `vps-lab`

L'adresse publique est réservée dans OCI afin de conserver une IP stable.

## Réseau

Adresse privée de l'instance :

```text
10.0.0.12
```

Le VPS utilise également une interface WireGuard :

```text
10.100.0.1/24
```

## Ports publics

Ports actuellement prévus :

| Port | Protocole | Utilisation |
|---|---|---|
| 22 | TCP | SSH |
| 80 | TCP | HTTP / reverse proxy |
| 443 | TCP | HTTPS / reverse proxy |
| 51820 | UDP | WireGuard |

## Firewall OCI

Les règles OCI doivent être cohérentes avec le firewall UFW du système.

Le principe est de ne pas ouvrir un port dans OCI si le service n'en a pas besoin.

## UFW

Configuration :

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw default allow routed
```

Vérification :

```bash
sudo ufw status verbose
```

## Monitoring

Les ressources à surveiller :

- CPU
- mémoire
- stockage
- trafic réseau
- volume de trafic WireGuard
- éventuels coûts OCI

Le suivi OCI est préférable pour surveiller les métriques et la consommation de la plateforme.
