# Importer une image Windows 11 24H2 et créer une séquence de tâches dans MDT

## Objectif

Importer l’image de **Windows 11 version 24H2** dans le **Deployment Share MDT** et créer une **séquence de tâches** permettant de déployer automatiquement cet OS sur les machines cibles, avec mise à jour automatique post-installation.

> Prérequis :  
> - ISO de Windows 11 24H2 montée sur le serveur MDT  
> - Deployment Share déjà créé et configuré (`D:\MDTDeploymentShare` ou autre chemin selon votre environnement)  
> - ADK et WinPE Add-on installés [`installation_adk.md`](installation_adk.md)

---

## I. Importer une image Windows 11 dans MDT

### A. Préparer l’environnement

Dans ce projet, l’ISO de **Windows 11 24H2** est montée directement sur la VM **WDS/MDT** via l’interface **Proxmox VE**.

#### Étapes :

1. Accéder à la VM dans Proxmox.
2. Aller dans **Hardware → CD/DVD Drive → Edit**.
3. Choisir **Use CD/DVD disc image file (iso)**.
4. Sélectionner l’ISO : `Win11_24H2_French_x64.iso`.
5. Valider avec **OK**.

![Montage ISO Windows 11 24H2 Proxmox](/captures/mdt_iso_mount_proxmox.png)

---

### B. Importer l’image dans MDT

1. Ouvrir la console **Deployment Workbench**.
2. Aller dans :  
   **Deployment Shares → MDT Deployment Share → Operating Systems**.
3. Clic droit → **New Folder** → Créer un dossier nommé `Windows 11 24H2`.
4. Clic droit  sur le nouveau dossier → **Import Operating System**.
5. Sélectionner **Full set of source files**.
6. Indiquer le lecteur monté (exemple : `E:\`).
7. Choisir le répertoire de destination dans le Deployment Share.
8. Finaliser l’assistant.

![Assistant Import OS](/captures/mdt_import_os_wizard.png)  
![Fin importation OS](/captures/mdt_import_os_finish.png)

---

### C. Supprimer les éditions inutiles 

1. Après l’import, dans **Operating Systems**, repérer les éditions disponibles dans le `install.wim`.
2. Supprimer les éditions non utilisées (exemple : conserver uniquement **Windows 11 Pro**).

![Fin importation OS](/captures/mdt_supp_editions.png)  

---

## II. Créer une séquence de tâches pour Windows 11

### A. Organiser les séquences de tâches

1. Dans **Deployment Workbench → Deployment Shares → MDT Deployment Share → Task Sequences**,  
2. Clic droit → **New Folder** → Nommer le dossier `Windows 11`.

---

### B. Créer la séquence de tâches

3. Clic droit sur le dossier **Windows 11** → **New Task Sequence**.
4. Renseigner :

![Création Task Sequence](/captures/mdt_create_task_sequence.png)  

5. A l'étape **Select Template**, selectionner `Standard Client Task Sequence`
---

### C. Sélectionner l’image Windows 11

6. Lors de l’étape **Select OS**, choisir l’image de Windows 11 24H2 précédemment importée.

![Création Task Sequence](/captures/mdt_select_os.png)  

---

### D. Configurer les paramètres de la séquence

7. **Product Key** : Laisser vide si activation par KMS ou si clé post-déploiement.  
8. **OS Settings** : Indiquer le nom du compte utilisateur qui sera créé par défaut sur la machine déployée, ainsi que le nom de l'organisation. Ce compte sera administrateur de la machine.
   
![Paramètres OS Task Sequence](/captures/mdt_os_settings.png)  

9. **Administrator password** : Définir le mot de passe du compte administrateur local de la machine.
10. Finaliser l'assistant

![Paramètres OS Task Sequence](/captures/mdt_task_finish.png)  

### E. Vérifier et personnaliser la séquence de tâches

Une fois la séquence de tâches créée, il est possible de l’éditer pour vérifier ou ajuster les différentes étapes du processus de déploiement.

#### Modifier la séquence de tâches :

1. Clic droit sur la séquence de tâches → **Properties**.  

![Task Sequence Update activé](/captures/mdt_modifier_sequence_tâche.png)

2. Aller dans l’onglet **Task Sequence**.

Cet onglet contient l’ensemble des tâches qui seront exécutées pendant le déploiement de la machine :  `Partitionnement du disque, installation du système d’exploitation, installation de pilotes et d’applications, configuration post-installation`.

Chaque tâche peut être :  Activée, désactivée, configurée pour interrompre le déploiement si elle échoue.

---

#### Exemple : Activer les mises à jour Windows après l’installation

Pour maintenir les machines à jour automatiquement à la fin du déploiement, il est conseillé d’activer la tâche :  
**Windows Update (Post-Application Installation)**.

- Cliquer sur cette étape dans l’arborescence des tâches.
- Aller dans l’onglet **Options** à droite.
- **Décocher** la case **Disable this step** (activer l’étape).
- Vérifier que la case **Continue on error** est cochée (permet au déploiement de continuer même si cette étape échoue).

![Task Sequence Update activé](/captures/mdt_task_windows_update.png)  

3. Selectionner **OK**, la séquence de tâche est prête !
