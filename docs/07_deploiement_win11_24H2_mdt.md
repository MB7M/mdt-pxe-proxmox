# Déployer Windows 11 24H2 avec MDT

### Objectif

Effectuer un déploiement complet d’une machine Windows 11 24H2 via MDT et WDS, en utilisant l’image Lite Touch générée précédemment.  
Ce déploiement utilise un environnement PXE avec un serveur DHCP distinct.

---

### Prérequis

- Image Lite Touch correctement générée et importée dans WDS (`LiteTouchPE_x64.wim`).
- Séquence de tâches existante et prête à être utilisée.
- ISO de Windows 11 24H2 montée et importée dans MDT.
- VM vierge configurée en boot PXE (Mode UEFI), carte réseau en INTEL E1000 pour compatibilité windows PE.

---

## A. Démarrer la machine en PXE

1. Allumer la VM vierge configurée pour booter sur le réseau (**PXE** activé, mode UEFI).
2. Le serveur DHCP attribue automatiquement une adresse IP à la machine.
3. L’écran du **WDS Boot Manager** s’affiche.

![WDS Boot Manager](/captures/mdt_bootmanager_litetouch.png)

4. Appuyez sur **Entrée** pour démarrer les services réseau.
5. Le fichier **LiteTouchPE_x64.wim** est chargé depuis le serveur WDS.

![Chargement image LiteTouchPE](/captures/mdt_boot_litetouch.png)

---

## B. Chargement de l’environnement Lite Touch

- L’image Lite Touch est téléchargée depuis WDS.
- Un écran gris **Microsoft Deployment Toolkit** apparaît.
- Après quelques secondes, l’assistant Lite Touch se lance automatiquement.

> Si l’assistant reste bloqué sur l’écran gris, vérifier les droits sur le Deployment Share (permissions NTFS et de partage).

![Chargement MDT](/captures/mdt_litetouch_grey_screen.png)

---

## C. Sélectionner la séquence de tâches

1. Dans l’onglet **Task Sequence**, sélectionner la séquence de tâches créée pour Windows 11 24H2.
2. Cliquer sur **Next**.

![Sélection Task Sequence](/captures/mdt_select_task_sequence.png)

3. Renseigner un **nom de machine** 
4. Indiquer des paramètres d’intégration au domaine Active Directory  si vous voulez la joindre à l'AD (ici on reste en workgroup).
5. Next


---

## D. Passer les étapes non pertinentes

- **Backup / Restore** : Inutile pour une machine neuve → cliquer sur **Next**.

![Options déploiement MDT](/captures/mdt_options_deploiement1.png)

- **Locale and Time** : Vérification de la langue, clavier, fuseau horaire → tout est déjà correct grâce aux fichiers `.ini`.

![Options déploiement MDT](/captures/mdt_options_deploiement2.png)

- **Capture Image** : (Utilisée pour créer une image d'installation (image WIM) à partir de la machine actuelle. En l'occurrence ici, ce n'est pas une machine de référence, mais une machine à déployer) donc choisir **Do not capture an image of this compute**  

![Options déploiement MDT](/captures/mdt_options_deploiement3.png)

- **BitLocker** : (Sert à chiffrer le disque) ne pas configurer pour l’instant → cliquer sur **Next**.

![Options déploiement MDT](/captures/mdt_options_deploiement4.png)

---

## E. Lancer la séquence de tâches

1. Cliquer sur **Begin** pour lancer le déploiement automatique.  

![Options déploiement MDT](/captures/mdt_options_deploiement5.png)

2. MDT procède au partitionnement du disque, à l’installation de Windows 11, aux personnalisations post-installation (updates, scripts, applications selon la séquence).

![Déploiement en cours](/captures/mdt_deploiement_en_cours.png)

---

## F. Fin du déploiement

- Une fois Windows installé, le PC redémarre automatiquement.
- Vérifier que :
  - Le **nom de la machine** correspond à celui saisi.
  - Windows Update post-installation est en cours si activé.  

![Déploiement terminé](/captures/mdt_finish1.png)

- À la fin, MDT affiche un récapitulatif avec :
  - Déploiement réussi
  - Éventuelles erreurs ou avertissements.

![Déploiement terminé](/captures/mdt_finish2.png)

Cliquer sur **Finish** pour terminer la session Lite Touch.  

---
