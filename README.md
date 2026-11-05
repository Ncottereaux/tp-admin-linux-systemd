# tp-admin-linux-systemd

# Rapport de Conformité — Projet Hélios Logistique

Ce document rassemble l'ensemble des preuves de configuration et de conformité pour le déploiement de l'infrastructure **Hélios Logistique** sur Rocky Linux 9 (`srv-core`).

---

## Synthèse des Exigences (E1 - E10)

| Exigence | Description | Statut | Fichiers / Services concernés |
| :--- | :--- | :---: | :--- |
| **E1** | Création du groupe `suivi` et de l'utilisatrice `lea` (politique de mdp) | **Validé** | `/etc/passwd`, `/etc/shadow`, `chage` |
| **E2** | Compte système `helios-suivi`, arborescence `/srv/helios/suivi/www` & ACLs | **Validé** | `/srv/helios/suivi/www`, `setfacl`, `Setgid 2770` |
| **E3** | Script de publication automatisé | **Validé** | `/usr/local/bin/helios-suivi-publish.sh` |
| **E4** | Automatisation Systemd (Service + Timer) | **Validé** | `helios-suivi-publish.service` & `.timer` |
| **E5** | Durcissement SSH (Port non standard 2242, clé unique) | **Validé** | `/etc/ssh/sshd_config.d/` |
| **E6** | Configuration du Pare-feu (Zone `drop` / `internal`) | **Validé** | `firewalld` |
| **E7** | Redondance Réseau (Bonding d'interfaces) | **Validé** | `nmcli` / NetworkManager |
| **E8** | Supervision système (Agent Zabbix 7.0 & sysstat) | **Validé** | `zabbix-agent2.service`, `sysstat-collect.timer` |
| **E9** | Publication initiale de la page d'accueil (`index.html`) | **Validé** | `/srv/helios/suivi/www/index.html` |
| **E10** | Dépôt de preuves et documentation | **Validé** | Dépôt Git |

---

## Détail des Preuves de Conformité

### 1. Gestion des Accès et Utilisateurs (E1, E2)
- **Compte `lea`** : Utilisateur d'exploitation (`UID 5104`, groupe `suivi` `GID 5003`).
  ```bash
  # Commande de vérification de la politique d'expiration :
  chage -l lea
  # Expiration mdp: 90j, Min: 1j, Alerte: 14j, Inactivité: 7j

### 3. Automatisation et Services (E3, E4)
- **Script exécutable** : `/usr/local/bin/helios-suivi-publish.sh` (`chmod +x`).
- **Commandes de vérification et statut des unités Systemd** :
  ```bash
  systemctl status helios-suivi-publish.timer
  systemctl status helios-suivi-publish.service
