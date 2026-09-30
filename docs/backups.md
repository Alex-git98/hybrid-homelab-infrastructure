# Sauvegardes

## Objectif

Les sauvegardes doivent permettre de restaurer les services après :

- panne matérielle ;
- erreur humaine ;
- corruption ;
- suppression accidentelle ;
- problème Docker ;
- problème système.

## Sauvegarde locale

Le serveur dispose de mécanismes de sauvegarde des données et configurations.

Les éléments importants comprennent notamment :

- configurations Docker ;
- configurations applicatives ;
- données Nextcloud ;
- données Immich ;
- données multimédias selon les besoins ;
- bases de données lorsque nécessaire.

## Nextcloud

Le projet utilise notamment des sauvegardes de :

```text
configuration
data
base de données
```

Les sauvegardes doivent être vérifiées régulièrement.

Une sauvegarde non testée n'est pas considérée comme une restauration validée.

## NAS distant

Un NAS distant est utilisé pour une synchronisation ponctuelle.

Le serveur démarre OpenVPN avec :

```bash
sudo openvpn --config /etc/openvpn/client/VPN_save.ovpn --daemon
```

Puis le script vérifie l'accès au NAS :

```text
192.168.50.250
```

Les données sont synchronisées avec `rsync` via SSH.

Exemple :

```bash
rsync -av --update ...
```

À la fin de la synchronisation, OpenVPN est arrêté.

## Script

Script utilisé :

```text
sync_dossiers.sh
```

Il réalise notamment :

1. vérification des dossiers ;
2. démarrage du VPN ;
3. attente de connexion ;
4. test du NAS ;
5. synchronisation des films ;
6. synchronisation des séries ;
7. arrêt du VPN ;
8. journalisation.

## Amélioration prévue

Le script utilise actuellement :

```bash
sudo pkill openvpn
```

Cette commande peut arrêter tous les processus OpenVPN.

Une amélioration future consiste à utiliser un PID ou un service systemd dédié afin de ne stopper que l'instance utilisée par la sauvegarde.

## Règle importante

Les sauvegardes ne doivent pas être considérées comme fiables uniquement parce que la commande s'est terminée avec succès.

À prévoir :

- vérification des logs ;
- test de restauration ;
- contrôle de l'espace disponible ;
- vérification périodique de l'intégrité.
