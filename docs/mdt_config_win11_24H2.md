# Configurer MDT pour Windows 11 

Pour déployer Windows 11 24H2 avec MDT, quelques ajustements sont nécessaires afin d'éviter des erreurs connues pouvant bloquer le processus de déploiement.

---

### A. Erreur MMC sur l'onglet Windows PE

Lors de l'accès aux propriétés du Deployment Share, un clic sur l’onglet **Windows PE** peut générer l’erreur suivante :

![Désactivation du support x86](/captures/mdt_MMC_error.png)

#### Solution :

1. Créer le dossier manquant :

   ```powershell
   mkdir "C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Windows Preinstallation Environment\x86\WinPE_OCs"
   ```

2. Dans les propriétés du Deployment Share, décocher le **support x86**.

📸 **Capture recommandée :**  
![Désactivation du support x86](/captures/mdt_disable_x86_support.png)

Après ces modifications, l'accès à l'onglet **Windows PE** fonctionne normalement.

---

### B. Erreur de script lors du lancement d’une tâche

Lors du lancement d'une séquence de tâches, l’erreur suivante peut apparaître :

![Mise à jour du Deployment Share](/captures/mdt_script_error.png)

#### Cause :

Cette erreur est liée à une modification du moteur de script HTA dans l’ADK depuis Windows 11 22H2 et persiste avec l’ADK 10.1.26100.2454 utilisé pour Windows 11 24H2.

Microsoft propose une solution officielle à cette erreur :  
 [Voir la documentation Microsoft - MDT Known Issues](https://learn.microsoft.com/en-us/mem/configmgr/mdt/known-issues#issue-mdt-script-error-occurs-during-windows-11-deployment)

> **Important :**  
> Cette page Microsoft peut être mise à jour. Pensez à vérifier régulièrement les dernières recommandations officielles en cas de modification ou d'évolution sur les versions futures de Windows ou de l'ADK.

---

#### Solution :

1. Sauvegarder l’existant :

   ```powershell
   copy "C:\Program Files\Microsoft Deployment Toolkit\Templates\Unattend_PE_x64.xml" "C:\Program Files\Microsoft Deployment Toolkit\Templates\Unattend_PE_x64_backup.xml"
   ```

2. Remplacer le contenu de `Unattend_PE_x64.xml` par le contenu suivant :

 ```xml
 <unattend xmlns="urn:schemas-microsoft-com:unattend">
    <settings pass="windowsPE">
        <component name="Microsoft-Windows-Setup" processorArchitecture="amd64" publicKeyToken="31bf3856ad364e35" language="neutral" versionScope="nonSxS" xmlns:wcm="http://schemas.microsoft.com/WMIConfig/2002/State">
            <Display>
                <ColorDepth>32</ColorDepth>
                <HorizontalResolution>1024</HorizontalResolution>
                <RefreshRate>60</RefreshRate>
                <VerticalResolution>768</VerticalResolution>
            </Display>
            <RunSynchronous>
                <RunSynchronousCommand wcm:action="add">
                    <Description>Fix HTA scripts error Windows 11 ADK 22H2</Description>
                    <Order>1</Order>
                    <Path>reg.exe add "HKLM\Software\Microsoft\Internet Explorer\Main" /t REG_DWORD /v JscriptReplacement /d 0 /f</Path>
                </RunSynchronousCommand>
                <RunSynchronousCommand wcm:action="add">
                    <Description>Lite Touch PE</Description>
                    <Order>2</Order>
                    <Path>wscript.exe X:\Deploy\Scripts\LiteTouch.wsf</Path>
                </RunSynchronousCommand>
            </RunSynchronous>
        </component>
    </settings>
</unattend>
```

Les deux erreurs ont été corrigées.

