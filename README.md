# 🐧 Guide Complet d'Installation de Linux Mint à la Société Tchadienne des Eaux

> **À destination des équipes informatiques** — Déploiement, configuration et maintenance en environnement professionnel

[![Linux Mint](https://img.shields.io/badge/Linux%20Mint-21.x%20LTS-87CF3E?style=flat-square&logo=linux-mint&logoColor=white)](https://www.linuxmint.com)
[![License](https://img.shields.io/badge/License-MIT-blue?style=flat-square)](LICENSE)
[![Maintenance](https://img.shields.io/badge/Maintained-Yes-green?style=flat-square)]()
[![PRs Welcome](https://img.shields.io/badge/PRs-Welcome-brightgreen?style=flat-square)]()

---

## 📋 Table des matières

- [Prérequis et préparation](#1-prérequis-et-préparation)
- [Téléchargement et création du support](#2-téléchargement-et-création-du-support)
- [Installation de base](#3-installation-de-base)
- [Configuration post-installation](#4-configuration-post-installation)
- [Sécurisation du poste](#5-sécurisation-du-poste)
- [Installation des logiciels métier](#6-installation-des-logiciels-métier)
- [Intégration réseau d'entreprise](#7-intégration-réseau-dentreprise)
- [Création d'une image maître](#8-création-dune-image-maître)
- [Déploiement en masse](#9-déploiement-en-masse)
- [Maintenance et mises à jour](#10-maintenance-et-mises-à-jour)
- [Dépannage courant](#11-dépannage-courant)
- [Annexes](#-annexes)

---

## 1. Prérequis et préparation

### 1.1 Configuration matérielle minimale recommandée

| Composant | Minimum | Recommandé |
|-----------|---------|------------|
| Processeur | 64 bits dual-core | Quad-core récent |
| RAM | 4 Go | 8 Go ou plus |
| Stockage | 50 Go | 100 Go SSD |
| Résolution écran | 1024×768 | 1920×1080 |
| Connexion réseau | Ethernet | Ethernet Gigabit |

### 1.2 Choix de la version

| Édition | Bureau | Usage recommandé |
|---------|--------|-----------------|
| Cinnamon | Moderne, intuitif | ✅ Recommandé pour la majorité des agents |
| MATE | Léger, classique | ✅ Machines anciennes (< 4 Go RAM) |
| Xfce | Très léger | ✅ Très vieilles machines |

> 💡 **Recommandation :** Choisir **Linux Mint Cinnamon LTS** pour une stabilité maximale en entreprise.

### 1.3 Inventaire préalable

Pour chaque poste, noter :
- Marque / Modèle
- RAM installée
- Type de stockage (HDD/SSD)
- Architecture (64 bits confirmé)
- Présence de BIOS ou UEFI
- Mode Secure Boot (activé/désactivé)
- Adresse MAC (pour attribution IP fixe)

### 1.4 Outils nécessaires

- 🔌 Clé USB de **8 Go minimum**
- 💿 Logiciel de création de clé bootable : [Ventoy](https://www.ventoy.net) ou [Balena Etcher](https://www.balena.io/etcher)
- 🔐 Accès administrateur au réseau (DHCP, DNS, AD si applicable)
- 📄 Feuille de route de déploiement
- ⚙️ Fichier de configuration standardisé (voir [section 8](#8-création-dune-image-maître))

---

## 2. Téléchargement et création du support

### 2.1 Téléchargement de l'ISO

```bash
# URL officielle
https://www.linuxmint.com/download.php

# Choisir :
# - Linux Mint 21.x (ou version LTS en cours)
# - Édition Cinnamon
# - Architecture 64 bits
```

**Vérification de l'intégrité de l'ISO**

```bash
# Sous Linux
sha256sum linuxmint-21.x-cinnamon-64bit.iso

# Vérification automatique
echo "HASH_OFFICIEL  linuxmint-21.x-cinnamon-64bit.iso" | sha256sum -c
```

> ⚠️ **Important :** Ne jamais sauter cette étape en entreprise. Une ISO corrompue ou modifiée représente un risque de sécurité.

### 2.2 Création de la clé USB bootable

**Option A — Avec Ventoy** *(recommandé pour déploiement multiple)*

```bash
# Avantage : une seule clé USB peut contenir plusieurs ISOs
# 1. Télécharger Ventoy : https://www.ventoy.net
# 2. Installer Ventoy sur la clé USB
# 3. Copier simplement l'ISO sur la clé
# 4. La clé est prête
```

**Option B — Avec Balena Etcher** *(simple et rapide)*

1. Télécharger [Etcher](https://www.balena.io/etcher)
2. `Flash from file` → sélectionner l'ISO
3. `Select target` → sélectionner la clé USB
4. `Flash!` → attendre la fin du processus

---

## 3. Installation de base

### 3.1 Démarrage depuis la clé USB

1. Insérer la clé USB dans le poste cible
2. Redémarrer le poste
3. Accéder au BIOS/UEFI (`F2`, `F12`, `DEL` ou `ESC` selon constructeur)
4. Modifier l'ordre de démarrage : **USB en premier**
5. Sauvegarder et quitter (`F10` généralement)

> **Gestion du Secure Boot :** Linux Mint 21+ est compatible Secure Boot. Laisser activé si possible. Désactiver uniquement en cas de problème de démarrage.

### 3.2 Lancement de l'installation

Double-cliquer sur **"Install Linux Mint"** sur le bureau live.

| Étape | Action |
|-------|--------|
| Langue | Sélectionner `Français` |
| Clavier | Sélectionner `Français` → tester dans le champ de saisie |
| Codecs | ✅ Cocher "Installer les logiciels tiers..." |
| Fuseau horaire | Sélectionner `Paris` |

### 3.3 Partitionnement

> ⚠️ **Étape critique**

**Option A — Disque entier** *(postes dédiés)*
```
→ "Effacer le disque et installer Linux Mint"
→ Activer le chiffrement LVM si requis par la politique de sécurité
```

**Option B — Partitionnement manuel** *(recommandé)*

| Partition | Point de montage | Taille | Format |
|-----------|-----------------|--------|--------|
| EFI | `/boot/efi` | 512 Mo | FAT32 |
| Boot | `/boot` | 1 Go | ext4 |
| Racine | `/` | 30-40 Go | ext4 |
| Home | `/home` | Reste | ext4 |
| Swap | `swap` | = RAM | swap |

> 💡 Séparer `/home` permet de réinstaller le système sans perdre les données utilisateurs.

### 3.4 Création du compte utilisateur

```
Nom complet       : [Prénom Nom de l'agent]
Nom d'utilisateur : [prenom.nom]
Nom de l'ordinateur : [SERVICE-FONCTION-NUMERO]  ex: RH-BUREAU-03
Mot de passe      : [Selon politique de sécurité]
```

> 💡 **Convention de nommage recommandée :** `SERVICE-FONCTION-NUMERO` → ex: `INFO-COMPTA-01`, `IT-ADMIN-01`

---

## 4. Configuration post-installation

### 4.1 Mises à jour système

```bash
sudo apt update
sudo apt upgrade -y
sudo apt full-upgrade -y
sudo apt autoremove -y && sudo apt autoclean
```

### 4.2 Installation des drivers

```bash
# Via le gestionnaire graphique
Menu → Administration → Gestionnaire de pilotes

# Ou en ligne de commande
sudo ubuntu-drivers autoinstall
```

### 4.3 Configuration de la langue

```bash
sudo apt install language-pack-fr language-pack-fr-base -y
sudo locale-gen fr_FR.UTF-8
sudo update-locale LANG=fr_FR.UTF-8
sudo apt install hunspell-fr libreoffice-l10n-fr -y
```

### 4.4 Synchronisation NTP

```bash
sudo timedatectl set-ntp true
sudo timedatectl set-timezone Europe/Paris

# Si serveur NTP interne
sudo nano /etc/systemd/timesyncd.conf
# NTP=ntp.votre-entreprise.fr

sudo systemctl restart systemd-timesyncd
```

---

## 5. Sécurisation du poste

### 5.1 Pare-feu (UFW)

```bash
sudo ufw enable
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw status verbose
```

### 5.2 Gestion des droits sudo

```bash
# Créer un groupe administrateurs IT
sudo groupadd it-admins
sudo usermod -aG it-admins technicien

# Configurer sudoers
sudo visudo
# Ajouter : %it-admins ALL=(ALL:ALL) ALL

# Retirer sudo aux utilisateurs standards
sudo deluser nom_agent sudo
```

### 5.3 Politique de mots de passe

```bash
sudo apt install libpam-pwquality -y
sudo nano /etc/security/pwquality.conf
```

```ini
minlen = 12       # Longueur minimale
dcredit = -1      # Au moins 1 chiffre
ucredit = -1      # Au moins 1 majuscule
lcredit = -1      # Au moins 1 minuscule
ocredit = -1      # Au moins 1 caractère spécial
maxrepeat = 3     # Max 3 caractères identiques consécutifs
```

```bash
sudo nano /etc/login.defs
# PASS_MAX_DAYS   90
# PASS_MIN_DAYS   1
# PASS_WARN_AGE   7
```

### 5.4 Verrouillage automatique de session

```bash
gsettings set org.cinnamon.desktop.screensaver lock-enabled true
gsettings set org.cinnamon.desktop.session idle-delay 300
```

### 5.5 Désactivation des services inutiles

```bash
sudo systemctl disable bluetooth.service    # Si pas de Bluetooth
sudo systemctl disable cups.service         # Si pas d'imprimante locale
sudo systemctl disable avahi-daemon.service # Si pas de mDNS
```

### 5.6 Audit système

```bash
sudo apt install auditd -y
sudo systemctl enable auditd
sudo systemctl start auditd
```

---

## 6. Installation des logiciels métier

### 6.1 Script d'installation automatisée

```bash
#!/bin/bash
# install_logiciels_entreprise.sh
# Usage : sudo bash install_logiciels_entreprise.sh

echo "=== Installation des logiciels entreprise ==="

apt update && apt upgrade -y

# Bureautique
apt install -y libreoffice libreoffice-l10n-fr libreoffice-help-fr libreoffice-pdfimport

# Navigateurs
apt install -y firefox firefox-locale-fr

# Messagerie
apt install -y thunderbird thunderbird-locale-fr

# Outils système
apt install -y htop curl wget git vim net-tools nmap tree unzip p7zip-full

# Outils réseau
apt install -y remmina remmina-plugin-rdp openssh-client openvpn network-manager-openvpn-gnome

# PDF
apt install -y evince pdftk ghostscript

apt autoremove -y && apt autoclean
echo "=== Installation terminée ==="
```

### 6.2 Outils de communication

```bash
# Microsoft Teams (Flatpak)
sudo apt install flatpak -y
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
flatpak install flathub com.microsoft.Teams -y

# Zoom
wget https://zoom.us/client/latest/zoom_amd64.deb
sudo dpkg -i zoom_amd64.deb && sudo apt install -f -y
```

### 6.3 Connexion à distance

```bash
# Client RDP
sudo apt install remmina remmina-plugin-rdp remmina-plugin-vnc -y

# VPN
sudo apt install openvpn network-manager-openvpn-gnome -y
```

---

## 7. Intégration réseau d'entreprise

### 7.1 Configuration réseau statique

```bash
sudo nmcli con mod "Connexion filaire 1" \
    ipv4.method manual \
    ipv4.addresses "192.168.1.XX/24" \
    ipv4.gateway "192.168.1.1" \
    ipv4.dns "192.168.1.10,8.8.8.8"

sudo nmcli con up "Connexion filaire 1"
```

### 7.2 Intégration Active Directory

```bash
# Installer les paquets
sudo apt install -y realmd sssd sssd-tools libnss-sss libpam-sss \
    adcli samba-common-bin oddjob oddjob-mkhomedir packagekit

# Rejoindre le domaine
realm discover VOTRE-DOMAINE.LOCAL
sudo realm join --user=Administrateur VOTRE-DOMAINE.LOCAL

# Vérifier
realm list
sudo realm permit -g "Utilisateurs du domaine"
```

Configuration SSSD (`/etc/sssd/sssd.conf`) :

```ini
[sssd]
domains = VOTRE-DOMAINE.LOCAL
config_file_version = 2
services = nss, pam

[domain/VOTRE-DOMAINE.LOCAL]
default_shell = /bin/bash
cache_credentials = True
id_provider = ad
fallback_homedir = /home/%u@%d
ad_domain = votre-domaine.local
use_fully_qualified_names = False
ldap_id_mapping = True
access_provider = ad
```

```bash
sudo chmod 600 /etc/sssd/sssd.conf
sudo systemctl restart sssd
```

### 7.3 Montage des partages réseau (CIFS)

```bash
sudo apt install cifs-utils -y

# Fichier de credentials sécurisé
sudo nano /etc/samba/.smbcredentials
# username=utilisateur
# password=motdepasse
# domain=DOMAINE
sudo chmod 600 /etc/samba/.smbcredentials

# Ajout dans /etc/fstab
//192.168.1.10/partage  /mnt/partage  cifs  credentials=/etc/samba/.smbcredentials,iocharset=utf8  0  0
sudo mount -a
```

### 7.4 Configuration du proxy

```bash
sudo nano /etc/environment
```

```ini
http_proxy="http://proxy.entreprise.fr:3128/"
https_proxy="http://proxy.entreprise.fr:3128/"
no_proxy="localhost,127.0.0.1,192.168.0.0/16,*.entreprise.fr"
```

```bash
sudo nano /etc/apt/apt.conf.d/95proxies
# Acquire::http::Proxy "http://proxy.entreprise.fr:3128/";
# Acquire::https::Proxy "http://proxy.entreprise.fr:3128/";
```

---

## 8. Création d'une image maître

### 8.1 Préparation de la machine maître

```bash
# Nettoyage
sudo apt autoremove -y && sudo apt autoclean
sudo rm -rf /tmp/* /var/tmp/*
sudo journalctl --vacuum-time=1s

# Réinitialisation de l'identité machine
sudo rm -f /etc/machine-id /var/lib/dbus/machine-id
sudo systemd-machine-id-setup
sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

# Vider l'historique
history -c && sudo history -c
```

### 8.2 Création avec Clonezilla

```
1. Démarrer sur la clé Clonezilla
2. Choisir "device-image" (disque vers image)
3. Choisir la destination (NAS, disque externe, serveur SSH)
4. Sélectionner "savedisk"
5. Nommer l'image : "LinuxMint-Master-YYYYMMDD"
6. Lancer la sauvegarde
```

> 📥 Télécharger Clonezilla : https://clonezilla.org/downloads.php

### 8.3 Création avec dd (alternative)

```bash
# Sauvegarde compressée
sudo dd if=/dev/sda bs=4M status=progress | gzip -9 > /mnt/backup/linuxmint-master.img.gz

# Restauration
sudo gunzip -c /mnt/backup/linuxmint-master.img.gz | sudo dd of=/dev/sda bs=4M status=progress
```

---

## 9. Déploiement en masse

### 9.1 Déploiement via Clonezilla Server Edition

> Architecture requise : serveur DHCP/PXE, serveur TFTP, serveur NFS ou Samba  
> 📖 Documentation : https://clonezilla.org/clonezilla-SE/

### 9.2 Déploiement via Ansible

```bash
sudo apt install ansible -y
```

**Inventaire** (`/etc/ansible/hosts`) :

```ini
[postes_agents]
poste-01 ansible_host=192.168.1.101
poste-02 ansible_host=192.168.1.102
poste-03 ansible_host=192.168.1.103

[postes_agents:vars]
ansible_user=admin
ansible_become=yes
ansible_become_method=sudo
```

**Playbook** (`deploy_linux_mint.yml`) :

```yaml
---
- name: Configuration standard Linux Mint
  hosts: postes_agents
  become: yes

  tasks:
    - name: Mise à jour du système
      apt:
        update_cache: yes
        upgrade: full

    - name: Installation des logiciels
      apt:
        name:
          - libreoffice
          - libreoffice-l10n-fr
          - thunderbird
          - firefox
          - remmina
          - remmina-plugin-rdp
          - htop
          - curl
          - wget
        state: present

    - name: Configuration du pare-feu
      ufw:
        state: enabled
        policy: deny
        direction: incoming

    - name: Autoriser SSH
      ufw:
        rule: allow
        name: OpenSSH

    - name: Configuration NTP
      lineinfile:
        path: /etc/systemd/timesyncd.conf
        regexp: '^#?NTP='
        line: 'NTP=ntp.entreprise.fr'
      notify: restart timesyncd

  handlers:
    - name: restart timesyncd
      systemd:
        name: systemd-timesyncd
        state: restarted
```

```bash
# Vérifier la connectivité
ansible postes_agents -m ping

# Exécuter le déploiement
ansible-playbook deploy_linux_mint.yml --ask-become-pass
```

---

## 10. Maintenance et mises à jour

### 10.1 Script de maintenance hebdomadaire

```bash
#!/bin/bash
# maintenance_hebdo.sh
# Planifier : sudo crontab -e → 0 2 * * 0 /usr/local/bin/maintenance_hebdo.sh

LOG="/var/log/maintenance_$(date +%Y%m%d).log"
echo "=== Maintenance du $(date) ===" | tee $LOG

apt update && apt upgrade -y >> $LOG 2>&1
apt autoremove -y && apt autoclean >> $LOG 2>&1
df -h >> $LOG 2>&1

for service in ssh ufw sssd; do
    echo "$service : $(systemctl is-active $service)" | tee -a $LOG
done

logrotate -f /etc/logrotate.conf >> $LOG 2>&1
echo "=== Maintenance terminée ===" | tee -a $LOG
```

### 10.2 Surveillance avec Zabbix Agent

```bash
sudo apt install zabbix-agent -y
sudo nano /etc/zabbix/zabbix_agentd.conf
# Server=192.168.1.200
# Hostname=NOM-DU-POSTE

sudo systemctl enable --now zabbix-agent
```

### 10.3 Sauvegarde des données utilisateurs

```bash
#!/bin/bash
# sauvegarde_homes.sh

BACKUP_DEST="/mnt/nas-backup/postes"
DATE=$(date +%Y%m%d_%H%M%S)

rsync -avz --delete \
    --exclude='.cache' \
    --exclude='.local/share/Trash' \
    --exclude='Downloads' \
    /home/ \
    "$BACKUP_DEST/$(hostname)/home_$DATE/"
```

---

## 11. Dépannage courant

### 11.1 Tableau de référence rapide

| Problème | Commande de diagnostic | Solution rapide |
|----------|----------------------|-----------------|
| Pas de réseau | `ip link show` | `sudo systemctl restart NetworkManager` |
| Mise à jour bloquée | `sudo apt update` | `sudo rm /var/lib/apt/lists/lock` |
| Session bloquée | `Ctrl+Alt+F2` | `kill -9 PID_session` |
| Disque plein | `df -h` | `sudo apt autoremove && sudo apt autoclean` |
| Service planté | `systemctl status SERVICE` | `sudo systemctl restart SERVICE` |
| Mot de passe oublié | Démarrer en recovery mode | `passwd utilisateur` |

### 11.2 Problèmes réseau

```bash
ip link show
sudo systemctl restart NetworkManager
journalctl -u NetworkManager -f
ping -c 4 8.8.8.8
```

### 11.3 Problèmes d'authentification AD

```bash
sudo systemctl status sssd
sudo sssctl domain-status VOTRE-DOMAINE.LOCAL
sudo sss_cache -E
sudo systemctl restart sssd
sudo journalctl -u sssd -f
```

### 11.4 Réparation de GRUB

```bash
# Depuis un live CD
sudo mount /dev/sda2 /mnt
sudo mount /dev/sda1 /mnt/boot/efi
sudo mount --bind /dev /mnt/dev
sudo mount --bind /proc /mnt/proc
sudo mount --bind /sys /mnt/sys
sudo chroot /mnt
grub-install /dev/sda
update-grub
exit && sudo reboot
```

### 11.5 Analyse des performances

```bash
htop                                    # CPU et RAM
iotop                                   # Utilisation disque
ps aux --sort=-%cpu | head -10          # Processus gourmands
systemd-analyze blame                   # Temps de démarrage
```

---

## 📎 Annexes

### Annexe A — Checklist de déploiement

**Avant l'installation**
- [ ] Inventaire matériel réalisé
- [ ] ISO téléchargée et vérifiée (SHA256)
- [ ] Clé USB bootable créée
- [ ] Données sauvegardées (si remplacement)
- [ ] Informations réseau préparées (IP, masque, DNS)
- [ ] Identifiants AD disponibles

**Pendant l'installation**
- [ ] Langue : Français
- [ ] Clavier : Français
- [ ] Partitionnement selon schéma défini
- [ ] Chiffrement activé si requis
- [ ] Nom de machine selon convention
- [ ] Compte utilisateur créé

**Après l'installation**
- [ ] Mises à jour système appliquées
- [ ] Drivers installés
- [ ] Pare-feu configuré
- [ ] Politique de mots de passe appliquée
- [ ] Verrouillage de session configuré (5 min)
- [ ] Logiciels métier installés
- [ ] Intégration AD vérifiée
- [ ] Partages réseau montés
- [ ] Imprimante configurée
- [ ] Proxy configuré
- [ ] Agent de supervision installé
- [ ] Test utilisateur final réalisé
- [ ] Fiche de poste remplie et archivée

### Annexe B — Ressources utiles

| Ressource | URL |
|-----------|-----|
| Site officiel Linux Mint | https://www.linuxmint.com |
| Forums Linux Mint | https://forums.linuxmint.com |
| Documentation Ubuntu (compatible) | https://help.ubuntu.com |
| Clonezilla | https://clonezilla.org |
| Ventoy | https://www.ventoy.net |
| Ansible Documentation | https://docs.ansible.com |
| Zabbix | https://www.zabbix.com |
| Balena Etcher | https://www.balena.io/etcher |

---

## 🤝 Contribution

Les contributions sont les bienvenues ! Pour proposer des améliorations :

1. Forkez le dépôt
2. Créez une branche (`git checkout -b amelioration/ma-contribution`)
3. Committez vos changements (`git commit -m 'Ajout : description'`)
4. Pushez la branche (`git push origin amelioration/ma-contribution`)
5. Ouvrez une Pull Request

---

## 📄 Licence

Ce projet est sous licence MIT — voir le fichier [LICENSE](LICENSE) pour plus de détails.

---

<div align="center">

**Maintenu par l'équipe informatique** • Dernière mise à jour : 2025

*Document interne — Déploiement Linux Mint en environnement d'entreprise*

</div>
