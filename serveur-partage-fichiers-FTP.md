# FICHE DE PROJET DE STAGE : Serveur de partages de fichiers (FTP)

## Informations Générales
* **Intitulé du sujet :** Déploiement et sécurisation des accès d'un serveur FTP cloisonné et partagé sous Debian 13.
* **Environnement technique :** Debian 13 (serveur VPS vierge déjà déployé), vsftpd, Commandes CLI Linux (gestion des utilisateurs, groupes et droits POSIX).
* **Objectif principal :** Configurer un serveur de fichiers FTP où chaque utilisateur possède son espace privé étanche, tout en accédant à un dossier commun partagé, avec un suivi rigoureux des logs par l'administrateur.

---

## Contexte et Problématique
L'entreprise dispose d'un serveur VPS vierge sous Debian 13. Elle souhaite l'utiliser pour mettre en place un espace de dépôt et d'échange de fichiers. L'enjeu de ce projet n'est pas le chiffrement réseau, mais la **sécurité logique et le cloisonnement des données** directement sur le système de fichiers.

Le stagiaire doit répondre à un besoin d'isolation stricte : les utilisateurs ne doivent pas pouvoir espionner ou modifier les fichiers des autres, ni naviguer dans l'arborescence globale du serveur. En revanche, ils doivent pouvoir collaborer au sein d'un unique dossier partagé nommé `elements_partages`.

---

## Objectifs de la Configuration
Le projet repose sur quatre piliers techniques que le stagiaire devra implémenter :
1. **Cloisonnement (Chroot) :** Enfermer chaque utilisateur dans son propre répertoire personnel dès la connexion FTP.
2. **Espace Collaboratif :** Rendre le dossier `/elements_partages` accessible en lecture/écriture à tous les utilisateurs au sein même de leur espace cloisonné.
3. **Traçabilité :** Configurer le serveur pour enregistrer les connexions, les transferts et les suppressions de fichiers.
4. **Administration système :** Comprendre et appliquer les commandes manuelles pour gérer le cycle de vie des utilisateurs (création et révocation).

---

## Déroulement du Projet et Format d'Accompagnement
Pour guider le stagiaire sans faire le travail à sa place, l'apprentissage est découpé en étapes progressives associant **action**, **questionnement** et **validation**.

### Semaine 1 : Installation et Découverte du Cloisonnement
* **Action :** Se connecter au VPS Debian 13 vierge, installer le paquet `vsftpd` et créer deux utilisateurs de test (ex: `user1` et `user2`).
* **Accompagnement / Guidage :** Inviter le stagiaire à se connecter en FTP avec ces comptes. Lui faire remarquer qu'un utilisateur peut remonter à la racine du serveur (`/etc`, `/var`, etc.) et voir les fichiers de l'autre.
* **Objectif Sécurité :** Le stagiaire doit rechercher et activer la directive de configuration (`chroot_local_user`) dans `vsftpd.conf` pour bloquer les utilisateurs dans leur répertoire personnel.
* **Point de vigilance :** Le tuteur guidera le stagiaire sur l'erreur classique de vsftpd concernant les droits d'écriture sur la racine du chroot pour lui faire trouver la solution appropriée (droits du dossier ou option de configuration dédiée).

### Semaine 2 : Mise en place de l'Espace Partagé
* **Action :** Créer le dossier commun `elements_partages` sur le serveur.
* **Accompagnement / Guidage :** Amener le stagiaire à réfléchir : si un utilisateur est techniquement "enfermé" dans son dossier `/home/user1`, comment peut-il accéder à un dossier situé ailleurs sur le serveur sans casser le cloisonnement ?
* **Objectif Sécurité :** Le stagiaire doit manipuler les mécanismes du système de fichiers Linux. Il découvrira comment lier des répertoires (via l'option de montage `--bind` dans `/etc/fstab`) et comment configurer les groupes Linux pour que `user1` et `user2` puissent écrire dans ce dossier commun sans conflit de droits.

### Semaine 3 : Configuration des Logs et Administration Manuelle
* **Action :** Activer et localiser les fichiers de journaux (logs) de vsftpd.
* **Accompagnement / Guidage :** Demander au stagiaire d'effectuer des actions de transfert, puis de chercher en temps réel dans quel fichier de `/var/log/` ces actions sont enregistrées.
* **Objectif Métier :** Le stagiaire ne doit pas automatiser par script, mais exécuter et comprendre les commandes système de base. Il doit documenter pour lui-même la séquence exacte des commandes pour :
    * Créer un utilisateur de A à Z (affectation du dossier, du groupe, et mise en place du lien vers le dossier partagé).
    * Révoquer un accès immédiatement (bloquer le compte utilisateur ou modifier son shell) tout en conservant ses fichiers intacts sur le disque.

---

## Procédure de Test et de Validation (Recette)
Pour valider le travail du stagiaire à la fin du projet, le tuteur effectuera les tests suivants en sa présence :

| Étape de test | Action demandée | Résultat attendu (Validation) |
| :--- | :--- | :--- |
| **1. Cloisonnement** | Connexion FTP avec le compte `user1`. Tenter de remonter dans le dossier parent ou de taper un chemin absolu comme `/etc`. | **Succès :** Le logiciel FTP refuse de remonter. La racine `/` apparente de l'utilisateur est son propre dossier. |
| **2. Étanchéité** | Depuis la session FTP de `user1`, tenter d'accéder au dossier de `user2`. | **Succès :** Le dossier de `user2` est invisible ou strictement inaccessible. |
| **3. Espace Commun** | Entrer dans le dossier `elements_partages`. Déposer un fichier "test_user1.txt". Se déconnecter, puis se connecter avec `user2`. | **Succès :** `user2` voit le fichier déposé par `user1`, peut le lire, le modifier ou le supprimer. |
| **4. Révocation** | L'administrateur applique la méthode de blocage sur le compte `user1`. Tenter une reconnexion FTP avec ce compte. | **Succès :** Le serveur FTP rejette immédiatement la connexion de `user1`. Ses fichiers restent présents sur le serveur. |
| **5. Audit des Logs** | L'administrateur ouvre le fichier de log configuré (`/var/log/vsftpd.log` ou `xferlog`). | **Succès :** On y retrouve la trace exacte de la connexion de `user1`, le nom du fichier déposé, et la tentative de connexion refusée après la révocation. |

---

## Livrables Attendus
1. **Le fichier vsftpd.conf final :** Le fichier de configuration du serveur, nettoyé et commenté par le stagiaire pour expliquer le rôle de chaque directive utilisée.
2. **Le journal technique d'administration :** Un document synthétique récapitulant les commandes Linux exactes utilisées pour ajouter un utilisateur, le lier au partage, et pour couper ses accès en cas d'urgence.

## Étape 1 : Installation et configuration de base de vsftpd

L'objectif est d'installer le service et de s'assurer qu'il démarre automatiquement, puis de configurer l'accès de base pour les utilisateurs locaux du système (Debian).

### 1. Installation du paquet
```bash
apt update
apt install -y vsftpd
```

### 2 : Modification du fichier de configuration de base
Ouvrez le fichier /etc/vsftpd.conf avec un éditeur de texte (comme nano) et assurez-vous que les directives suivantes sont décommentées et configurées ainsi :

```TOML
# Activer le démarrage du serveur en mode autonome (background daemon)
listen=YES

# Permettre aux utilisateurs locaux du système Linux de se connecter en FTP
local_enable=YES

# Autoriser les utilisateurs à écrire/modifier des fichiers (indispensable pour les dépôts)
write_enable=YES

# Interdire strictement les connexions anonymes (Sécurité de base)
anonymous_enable=NO
```

### 3 : Redémarrage du service

```bash
systemctl restart vsftpd
systemctl enable vsftpd
```

> Pourquoi ? restart applique les modifications. enable garantit que le serveur FTP se lancera automatiquement si le VPS redémarre.

## Étape 2 : Cloisonnement des utilisateurs (Chroot)
Par défaut, un utilisateur FTP Linux peut naviguer dans l'intégralité du système (voir /etc, /var, etc.). Il faut l'enfermer de force dans son propre répertoire personnel.

### 1. Activation du Chroot dans /etc/vsftpd.conf
Ajoutez ou décommentez ces lignes :

```TOML
# Enfermer les utilisateurs locaux dans leur répertoire personnel (chroot)
chroot_local_user=YES

# Empêcher vsftpd de planter si le répertoire racine de l'utilisateur a les droits en écriture
allow_writeable_chroot=YES
```

> Pourquoi la directive allow_writeable_chroot=YES ? Par mesure de sécurité, vsftpd refuse de lancer un utilisateur cloisonné si son dossier racine est accessible en écriture. Cette option permet de contourner cette sécurité proprement sans bloquer l'accès aux utilisateurs de Première année qui ont besoin de déposer des fichiers directement à la racine de leur espace FTP.

Appliquez la modification :

```bash
systemctl restart vsftpd
```

## Étape 3 : Gestion des utilisateurs et de l'espace partagé
Nous allons créer un groupe commun, créer le dossier partagé global sur le serveur, puis créer les utilisateurs de test.

### 1. Création du groupe et du dossier global partagé

```bash
# Créer un groupe unique pour tous les membres du FTP
groupadd ftp_users

# Créer le dossier partagé d'échange à la racine du serveur
mkdir /elements_partages

# Donner la propriété du dossier au groupe ftp_users
chown -R root:ftp_users /elements_partages

# Configurer les permissions : root a tous les droits, le groupe peut lire/écrire
chmod 775 /elements_partages
```

### 2. Création des utilisateurs de test (user1 et user2)

```bash
# Créer user1, l'ajouter au groupe ftp_users, et lui attribuer un shell standard
useradd -m -g ftp_users -s /bin/bash user1
passwd user1

# Faire de même pour user2
useradd -m -g ftp_users -s /bin/bash user2
passwd user2
```

> Pourquoi -m ? Cela crée automatiquement le dossier /home/user1 qui servira de racine FTP isolée.

> Pourquoi -g ftp_users ? Cela lie l'utilisateur au groupe commun pour la gestion du dossier partagé.

### 3. Injection sécurisée du dossier partagé dans chaque espace privé
Puisque les utilisateurs sont enfermés dans leur /home/userX, ils ne peuvent pas accéder physiquement à /elements_partages. Les liens symboliques (ln -s) ne fonctionnent pas à travers un Chroot FTP. La solution consiste à créer un point de montage miroir (bind mount).

```bash
# Créer les dossiers de réception dans chaque Home
mkdir /home/user1/elements_partages
mkdir /home/user2/elements_partages

# Monter le dossier central dans le dossier de l'utilisateur (Liaison virtuelle)
mount --bind /elements_partages /home/user1/elements_partages
mount --bind /elements_partages /home/user2/elements_partages
```

### 4. Rendre ces liaisons permanentes (Survivre au redémarrage)
Ouvrez le fichier /etc/fstab et ajoutez ces deux lignes tout à la fin :

```text
/elements_partages /home/user1/elements_partages none bind 0 0
/elements_partages /home/user2/elements_partages none bind 0 0
```

> Comment ça marche ? À chaque démarrage du VPS, Linux va cloner virtuellement l'accès de /elements_partages dans les espaces FTP isolés des utilisateurs.

## Étape 4 : Configuration de la Traçabilité (Logs)

L'administrateur doit savoir précisément qui fait quoi sur le serveur.

### 1. Activation des logs dans /etc/vsftpd.conf
Modifiez le fichier pour y intégrer ces options :

```TOML
# Activer la journalisation générale des requêtes FTP
xferlog_enable=YES

# Utiliser le format de log standard vsftpd (plus détaillé que le format d'échange standard wu-ftpd)
xferlog_std_format=NO

# Emplacement du fichier de log unique du serveur
vsftpd_log_file=/var/log/vsftpd.log

# Enregistrer toutes les requêtes, pas seulement les transferts de fichiers (connexions, erreurs, commandes)
log_ftp_protocol=YES
```

```bash
systemctl restart vsftpd
```

### 2. Comment lire les logs en temps réel ?
Pour observer l'activité du serveur pendant les tests, l'administrateur peut exécuter cette commande dans son terminal SSH :

```bash
tail -f /var/log/vsftpd.log
```

> Pourquoi ? L'option -f (follow) permet d'afficher les nouvelles lignes de log instantanément à l'écran dès qu'un utilisateur se connecte ou interagit avec le FTP.

## Étape 5 : Procédures manuelles d'administration (Cycle de vie)
Voici les commandes exactes à retenir pour l'administration au quotidien.

### 1. Ajouter un nouvel utilisateur (Ex: user3)

```bash
# 1. Création de l'utilisateur dans le bon groupe
useradd -m -g ftp_users -s /bin/bash user3
passwd user3

# 2. Création du point d'accès pour le partage
mkdir /home/user3/elements_partages
mount --bind /elements_partages /home/user3/elements_partages

# 3. Rendre persistant le partage (Ajouter la ligne suivante dans /etc/fstab)
echo "/elements_partages /home/user3/elements_partages none bind 0 0" >> /etc/fstab
```

### 2. Révoquer un utilisateur en urgence (Ex: user1)
Pour bloquer l'accès FTP d'un utilisateur sur-le-champ sans pour autant détruire ses fichiers sur le serveur, il faut désactiver son compte au niveau du système Linux.

```bash
# Exclure l'utilisateur en verrouillant son mot de passe
usermod -L user1
```

> Comment ça marche ? L'option -L (Lock) ajoute un caractère spécial devant le mot de passe chiffré dans le système. Le serveur vsftpd refusera immédiatement la connexion de cet utilisateur.

> Pour réactiver l'accès plus tard : usermod -U user1 (Unlock).
