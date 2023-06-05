**To read this document in English, click [here](https://github.com/eloiJhn/DeployApp/blob/main/README_EN.md).**


# 🚀 DeployApp
**Tutoriel officiel pour déployer une application .NET MAUI sur Firebase avec Github Actions [GRATUIT] [SEULEMENT ANDROID/APK]**

## 📚 Prérequis
Avant de commencer, assurez-vous que :

- Vous avez un compte Firebase.
- Vous avez un projet Firebase prêt à l'emploi.
- Vous avez une application Android .NET MAUI que vous souhaitez déployer.

## I : Création d'un compte FIREBASE + Projet 📝
1. Tout d'abord, créez votre compte **FIREBASE** à l'adresse suivante : https://firebase.google.com/

2. Cliquez sur "*Ajouter un Projet*" et remplissez les 3 étapes nécessaires.

<p align="center">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/1%20createproject.png" width="400">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/2%20create%20project.png" width="400">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/3%20create%20project.png" width="400">
</p>

3. Une fois le projet créé, sélectionnez l'icône Android et remplissez les 4 étapes suivantes :

  a)

<p align="center">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/1%20createapp.png" width="1000">
</p>

  b) Téléchargez le fichier `google-services.json`. Pour **.NET MAUI** : placez-le dans le dossier `Resources`

<p align="center">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/2%20createapp.png" width="600">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/2%20createappfolder.png" width="600">
</p>

  c) Il n'est pas nécessaire d'installer les SDK Firebase, vous pouvez passer cette étape.
   
  d) 

<p align="center">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/4%20createapp.png" width="400">
</p>

4. Enfin aller dans l'onglet App Distribution (Publier et surveiller - App Distribution) et cliquer sur "Commencer"

<p align="center">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/start%20appdistribution.png" width="600">
</p>


## II : Configuration déploiement Projet 📦
**Android** exige que toutes les applications soient signées avant qu'elles puissent être installées ou mises à jour sur un appareil. Cela peut être réalisé avec la commande "*keytool*" fournie avec le JDK. Une fois le fichier *keystore* créé, ajoutez-le à votre projet **.NET MAUI**. Dans cet exemple, nous le placerons à la racine du projet .NET MAUI et l'appellerons `my-release-key.keystore`.

1. Créez un fichier keystore pour signer votre application :
    - Utilisez la commande `keytool` dans le terminal à la racine de votre projet :
    ```bash
    keytool -genkey -v -keystore my-release-key.keystore -alias alias_name -keyalg RSA -keysize 2048 -validity 10000
    ```
    - Entrez le mot de passe qui correspondra à `KEYSTORE_PASSWORD` dans les secrets de votre projet Github (il doit comporter plus de 6 caractères)
    - Complétez toutes les étapes suivantes

<p align="center">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/1%20keystore.png" width="600">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/2%20keystore.png" width="600">
</p>



2. Paramètres de déploiement de l'application :
    - Assurez-vous que dans votre fichier `NameProject.csproj`, vous enlevez ceci :
        - `net7.0-ios`
        - `net7.0-maccatalyst`

<p align="center">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/2%20settingdeploy.png" width="600">
</p>

   - Dans le fichier `AndroidManifest.xml` (situé dans `Platforms-Android-AndroidManifest.xml`), remplacez son contenu par ceci :
    
    <?xml version="1.0" encoding="utf-8"?>
    <manifest xmlns:android="http://schemas.android.com/apk/res/android" package="<YourNamePackage>">
        <uses-sdk android:minSdkVersion="21" android:targetSdkVersion="30" />
        <application android:allowBackup="true" android:usesCleartextTraffic="true" android:icon="@mipmap/appicon" android:supportsRtl="true"></application>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <uses-permission android:name="android.permission.INTERNET" />
    </manifest>
    ```
    

   - Remplacez `<YourNamePackage>`, par le nom que vous avez choisi lorsque vous avez créé le projet.

<p align="center">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/namepackage.png" width="600">
</p>


## III : Configuration déploiement Github 📤
1. Ajoutez les informations du keystore comme secrets dans GitHub :
    - Allez dans la page de votre dépôt sur GitHub, cliquez sur "Settings", puis sur "Secrets". Ajoutez les informations suivantes comme secrets :
        - `KEYSTORE_PASSWORD`: Le mot de passe du keystore.
        - `KEYSTORE_ALIAS`: L'alias de la clé que vous avez défini avec la commande KeyTool
        - `KEY_PASSWORD`: Le mot de passe de la clé (il doit être identique à celui du KeyStore)
        - `FIREBASE_TOKEN`: Le token Firebase

<p align="center">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/createsecret.png" width="1000">
</p>


2. Pour obtenir le `FIREBASE_TOKEN` :
    - Installez les outils CLI de Firebase si vous ne l'avez pas déjà fait. Vous pouvez le faire en utilisant npm (Node Package Manager) avec la commande suivante à la racine de votre projet : `npm install -g firebase-tools`
    - Une fois que vous avez installé Firebase CLI, vous pouvez vous connecter à votre compte Firebase en utilisant la commande suivante : `firebase login`
    - Après vous être connecté, vous pouvez obtenir un token Firebase en utilisant la commande suivante : `firebase login:ci` 

<p align="center">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/firebasetoken.png" width="400">
</p>

3. Créez un fichier de workflow pour GitHub Actions :
    - Créez un nouveau fichier dans le répertoire `.github/workflows` de votre dépôt. Vous pouvez l'appeler `deploy-to-firebase.yml`. Ce fichier contiendra la configuration de votre workflow GitHub Actions.
    - Selon le type de votre dépôt (privé ou public), suivez les instructions appropriées ci-dessous :

:closed_lock_with_key: **Pour un dépôt privé :**
  - Copiez le contenu de ce fichier : [Workflow Deploy pour dépôt privé](https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/WorkflowDeployStepPrivate.yml)

:unlock: **Pour un dépôt public :**
  - Copiez le contenu de ce fichier : [Workflow Deploy pour dépôt public](https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/WorkflowDeployStepPublic.yml)
  - Vous devez encoder le fichier keystore, car il contient des données confidentielles. Pour cela, exécutez la commande suivante sur votre projet en local (Windows) :
    ```
    certutil -encode my-release-key.keystore keystore_base64.txt
    ```
  - Ensuite, vous devez mettre le contenu du fichier `keystore_base64.txt` dans les secrets de Github pour pouvoir récupérer le fichier keystore dans le workflow

<p align="center">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/1%20keystorebase64.png" width="600">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/2%20keystorebase64.png" width="600">
</p>


4. Dans le worflow que vous aurez copié, j'ai mis en commentaire tous les endroits que vous devez modifier :

  - `<YourSolutionPath>`
  - `<YourKeystorePath>`
  - `<YourProjectPath>`
  - `<YourApkFile>`
  - `<YourAppID>`

5. Remplacez le nom de votre APK et l'ID de votre application sur cette ligne :
    ```bash
    args: appdistribution:distribute ./publish/android/<YourApkFile> --app <YourAppID>
    ```
    - Le nom de votre APK et l'ID de votre application se trouve ici 

<p align="center">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/namepackage.png" width="600">
</p>

## IV : Conclusion 🔚
Et voilà ! Avec cette configuration, chaque fois que vous poussez des modifications sur la branche main de votre dépôt, GitHub Actions construira votre application .NET MAUI et la déployera sur Firebase App Distribution.

## V : Problèmes connus 🥺
Si vous rencontrez des problèmes lors de l'utilisation de cette configuration, assurez-vous que :

- Votre application .NET MAUI peut être construite et publiée localement sur votre machine.
- Vous utilisez la bonne version de .NET (à l'heure actuelle, .NET MAUI nécessite .NET 7).
- Vous avez correctement configuré votre fichier `AndroidManifest.xml`.
- Vos informations de keystore sont correctes (alias, mots de passe, etc.).
- Vous avez correctement configuré le fichier `deploy-to-firebase.yml`.

Si vous continuez à rencontrer des problèmes après avoir vérifié ces points, n'hésitez pas à ouvrir un problème dans ce dépôt dans l'onglet "Issues". Je serai heureux de vous aider !

---

## VI : Sources ℹ️

- [Documentation Firebase](https://firebase.google.com/docs?authuse&hl=fr)
- [Documentation Github Actions](https://docs.github.com/en/actions/deployment/about-deployments/deploying-with-github-actions)



© 2023 Eloi J. - Tous droits réservés.
