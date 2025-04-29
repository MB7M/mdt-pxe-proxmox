# Générer l’image Lite Touch et l’importer dans WDS

### Objectif

Générer l’image de démarrage **Lite Touch** avec MDT, contenant toutes les personnalisations (bootstrap.ini, CustomSettings.ini, drivers, etc.), puis l’ajouter dans **WDS** comme image de démarrage pour un déploiement PXE.

---

### Prérequis

- Deployment Share correctement configuré avec tous les fichiers à jour (Task Sequence, OS, paramètres, drivers).
- Support 32 bits désactivé dans MDT (Windows 11 ne supporte que le 64 bits).
- Console MDT et WDS fonctionnelles.
- Partition ou disque hébergeant le Deployment Share accessible.

---

## A. Générer l’image Lite Touch depuis MDT

1. Ouvrir la console **Deployment Workbench**.
2. Clic droit sur le **Deployment Share** → **Update Deployment Share**.

   
![Update Deployment Share](/captures/mdt_update_deployment_share_start.png)

3. Sélectionner la première option :  

![Update Deployment Share](/captures/mdt_update_options.png)

   - **Optimize the boot image updating process** (rapide, pour des modifications mineures)
   - **Completely regenerate the boot images** (recommandé si modifications importantes ou incohérences)

4. Lancer la génération et patienter.
5. Une fois terminé, cliquer sur **Finish**.

 
![Génération terminée](/captures/mdt_generation_litetouch_done.png)

> L’image générée se trouve dans :  
> `W:\DeploymentShare\Boot\LiteTouchPE_x64.wim` (chemin à adapter à l'environnement)

---

## B. Importer l’image Lite Touch dans WDS

1. Ouvrir la console **WDS** sur le serveur.
2. Clic droit sur **Images de démarrage** → **Ajouter une image de démarrage**.
3. Naviguer jusqu’au fichier `.wim` généré :  
   `W:\DeploymentShare\Boot\LiteTouchPE_x64.wim`
       
![Ajout image WDS](/captures/mdt_wds_lifetouch.png)  

4. Donner un nom parlant à l’image (ex. : `MDT Lite Touch Windows 11`), puis cliquer sur **Suivant**.

![Ajout image WDS](/captures/mdt_wds_litetouch_import.png)

5. Patienter pendant l’import → cliquer sur **Terminer**.

![Image LiteTouch importée](/captures/wds_litetouch_done.png)

6. Vérifier que l’image est bien en **ligne** dans WDS.

7. **Désactiver** les anciennes images de démarrage inutilisées pour éviter toute confusion au démarrage PXE.

---
