# Installer Windows ADK 10.1.26100.2454 pour Windows 11 24H2  

## Objectif

Installer le **Windows Assessment and Deployment Kit (ADK)** ainsi que le module complémentaire **Windows PE Add-on**, nécessaires à la création des images de déploiement et des séquences avec **Microsoft Deployment Toolkit (MDT)**.

> Ce guide fait partie du projet de déploiement automatisé de Windows 11 24H2 avec MDT et WDS.  
> Pour la configuration de MDT, se référer à [`installation_mdt.md`](02_installation_mdt.md).  
> Pour l’installation de WDS, se référer à [`installation_wds.md`](https://github.com/MB7M/wds-pxe-proxmox/blob/56de0db88bb38679ed6aa2a60d64516de12031c3/docs/installation_wds.md).

---

## Prérequis

- Windows Server 2022 à jour
- Compte administrateur local ou domaine
- Connexion internet pour le téléchargement des outils

---

## A. Choix de la version de l’ADK 10.1.26100.2454 (décembre 2024) et compatibilité

Windows 11 24H2 étant uniquement disponible en **64 bits (x64)**, il est nécessaire d’utiliser une version récente des outils ADK et WinPE, compatibles avec cette architecture.  

Cette version de l’ADK et de son module complémentaire Windows PE prend en charge les systèmes suivants :

- **Windows 11, version 24H2** et toutes les versions antérieures prises en charge de Windows 10 et 11
    
- **Windows Server 2025** et **Windows Server 2022**

| Composant                | Version utilisée                | Remarque                                                          |
| ------------------------ | ------------------------------- | ----------------------------------------------------------------- |
| **Windows ADK**          | 10.1.26100.2454 (décembre 2024) | Compatible Windows 11 24H2, sans support du x86 (32 bits)         |
| **WinPE Add-on for ADK** | 10.1.26100.2454 (décembre 2024) | Indispensable pour la génération des images de démarrage UEFI x64 |


> ⚠️ L’utilisation d’anciennes versions de l’ADK peut entraîner des erreurs de compatibilité avec Windows 11.

---

## B. Installer Windows ADK et WinPE Add-on

### 1. Installation de l’ADK

#### Étapes :

1. [Télécharger l’ADK 10.1.26100.2454](https://go.microsoft.com/fwlink/?linkid=2289980)
2. Exécuter `adksetup.exe`.
3. Sélectionner **« Installer sur cet ordinateur »**.
4. Chemin par défaut : `C:\Program Files (x86)\Windows Kits\10`
       
![Sélection fonctionnalités ADK](/captures/adk_install.png)

5. Accepter les conditions de licence.
6. Sélectionner uniquement les fonctionnalités suivantes suffisantes pour faire du MDT puis installer :

![Sélection fonctionnalités ADK](/captures/adk_install_selection.png)


![Fin installation ADK](/captures/adk_install_finish.png)

---

### 2. Installation du module complémentaire WinPE

#### Étapes :

1. [Télécharger le **WinPE Add-on** pour ADK 10.1.26100.2454](https://go.microsoft.com/fwlink/?linkid=2289981)

2. Exécuter `adkwinpesetup.exe`.
3. Choisir **« Installer sur cet ordinateur »**.
4. Chemin par défaut (identique à l’ADK).
5. Accepter les conditions de licence.
6. Aucun choix supplémentaire à faire.

![Fin installation WinPE](/captures/winpe_install_finish.png)

---

## C. Vérification de l’installation

Vérifier la présence des dossiers suivants après installation :


![Fin installation WinPE](/captures/winpe_install_finish_dossiers.png)
