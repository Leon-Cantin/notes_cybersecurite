## Énumération
* détecter si dans un docker `cat /proc/1/cgroup`
* si `/opt/backup/docker-compose.yml` (je suis pas sûr du chemin) `privileged: true` alors possible de sortir si root

## UID montre un nombre
Si le UID ou GID est un chiffre, peut-être que le dossier est monté directement de l'hôte.
* Tout changement effectué à l'intérieur du docker se reflète dans l'hôte. Possible de mettre SUID root

## Enum port de l'hôte
le conteneur peut avoir son IP et voir l'IP de l'hôte.

## Deepce
https://github.com/stealthcopter/deepce

## /dev/sda
Peut monter les disques dur de l'hôte
```shell
# Trouver le disque à monter
lsblk
mkdir /mnt/sda
mount /dev/sda /mnt/sda
```