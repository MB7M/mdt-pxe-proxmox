# Personnaliser le bootstrap.ini et le CustomSettings.ini

### Objectif

Configurer les fichiers **bootstrap.ini** et **CustomSettings.ini** de MDT afin de personnaliser le processus de déploiement :  
- Automatiser la connexion au Deployment Share,  
- Définir la langue du clavier dans l’environnement WinPE,  
- Optimiser l’assistant Lite Touch (suppression des étapes inutiles, timezone, etc.).

Ces paramètres influencent directement le comportement de l’image Lite Touch générée par MDT, utilisée pour le démarrage PXE des clients.

---

### Prérequis

- Deployment Share existant et configuré (ex. : `D:\MDTDeploymentShare`).
- Compte de service dédié MDT déjà créé (ex. : `Service_MDT`).
- Nom de domaine ou nom du serveur si utilisation d’un compte local (ex. : `SRV-WDS`).
- Accès administrateur local sur le serveur MDT.

---

## A. Modifier le fichier `CustomSettings.ini`

Le fichier `CustomSettings.ini` contrôle le comportement de l’assistant Lite Touch durant le déploiement. Par défaut, certaines directives sont déjà présentes selon les options choisies lors de la création du Deployment Share.

>  **Nous allons ici ajouter des paramètres supplémentaires pour personnaliser le fichier et adapter l’assistant à notre environnement.**

### Accès au fichier :

- Dans **Deployment Workbench**, clic droit sur le Deployment Share → **Properties** → onglet **Rules**, modifier directement dans l'onglet.

### Exemple de configuration :

```ini
[Settings]
Priority=Default
Properties=MyCustomProperty

[Default]
OSInstall=Y
SkipCapture=NO
SkipAdminPassword=YES
SkipProductKey=YES
SkipComputerBackup=NO
SkipBitLocker=NO

_SMSTSORGNAME=mbits

TimeZone=105
TimeZoneName=Romance Standard Time
```

#### Détails :

| Directive              | Description                                       |
|-------------------------|---------------------------------------------------|
| `SkipAdminPassword=YES` | Le mot de passe admin est déjà défini dans la Task Sequence, pas besoin de le redemander. |
| `_SMSTSORGNAME`         | Nom affiché dans l’environnement WinPE.           |
| `TimeZone` / `TimeZoneName` | Définit le fuseau horaire pendant le déploiement. |

![Configuration CustomSettings.ini](/captures/mdt_customsettings_ini.png)

---

## B. Modifier le fichier `bootstrap.ini`

Le fichier `bootstrap.ini` est utilisé au démarrage de l’environnement WinPE pour établir la connexion au Deployment Share.

### Accès au fichier :

- Toujours depuis l’onglet **Rules**, cliquer sur **Edit Bootstrap.ini** (en bas à droite).

### Exemple de configuration :

```ini
[Settings]
Priority=Default

[Default]
DeployRoot=\\SRV-WDS\DeploymentShare$
UserID=Service_MDT
UserPassword=héhé
UserDomain=SRV-WDS
SkipBDDWelcome=YES
KeyboardLocalePE=040c:0000040c
```

#### Détails :

| Directive               | Description                                       |
|--------------------------|---------------------------------------------------|
| `DeployRoot`             | Chemin UNC vers le Deployment Share.              |
| `UserID` / `UserPassword`| Compte de service utilisé pour accéder au partage MDT. |
| `UserDomain`             | Nom du serveur (compte local) ou du domaine (AD). |
| `SkipBDDWelcome=YES`     | Masque l’écran de bienvenue de l’assistant.       |
| `KeyboardLocalePE=040c:0000040c` | Définit le clavier français dans WinPE.  |

![Configuration Bootstrap.ini](/captures/mdt_bootstrap_ini.png)

> Le fait d’indiquer l’identifiant et le mot de passe permet d’automatiser la connexion au Deployment Share sans intervention manuelle.

---

## C. Vérifier les paramètres de l’image Lite Touch 

Dans **Deployment Share → Properties → Onglet Windows PE** :

1. Choisir la **Platform : x64**.  
2. Onglet **Drivers and Patches** : vérifier que seuls les pilotes de stockage et de réseau sont intégrés (par défaut).

> ⚠️ Ajouter uniquement les pilotes nécessaires pour éviter d’alourdir l’image WinPE.


![Sélection des pilotes WinPE](/captures/mdt_winpe_drivers.png)
