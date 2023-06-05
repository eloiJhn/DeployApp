# 🚀 DeployApp
**Official tutorial for deploying a .NET MAUI application on Firebase with Github Actions [FREE] [ANDROID/APK ONLY]**

## 📚 Prerequisites
Before getting started, please ensure that:

- You have a Firebase account.
- You have a Firebase project ready to use.
- You have an Android .NET MAUI application you wish to deploy.

## I: Creating a FIREBASE account + Project 📝
1. First, create your FIREBASE account at the following address: https://firebase.google.com/

2. Click on "Add a Project" and complete the 3 necessary steps.

<p align="center">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/1%20createproject.png" width="400">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/2%20create%20project.png" width="400">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/3%20create%20project.png" width="400">
</p>

3. Once the project has been created, select the Android icon and complete the following 4 steps :

  a)

<p align="center">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/1%20createapp.png" width="1000">
</p>

  b) Download the `google-services.json` file. For **.NET MAUI**: place it in the `Resources` folder.
  
 <p align="center">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/2%20createapp.png" width="600">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/2%20createappfolder.png" width="600">
 </p>
 
  c) You don't need to install the Firebase SDK,  you can skip this step.
  
  d) 

<p align="center">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/4%20createapp.png" width="400">
</p>

4. Finally, go to the App Distribution tab (Publish and monitor - App Distribution) and click on "Start".

<p align="center">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/start%20appdistribution.png" width="600">
</p>

## II: Project deployment configuration 📦
**Android** requires all applications to be signed before they can be installed or updated on a device. This can be achieved with the "*keytool*" command supplied with the JDK. 
Once the *keystore* file has been created, add it to your **.NET MAUI** project. In this example, we'll place it at the root of the .NET MAUI project and call it `my-release-key.keystore`.

1. Create a keystore file to sign your application:
    - Use the `keytool` command in the terminal at the root of your :
    ```bash
    keytool -genkey -v -keystore my-release-key.keystore -alias alias_name -keyalg RSA -keysize 2048 -validity 10000
    ```
    - Enter the password that will correspond to `KEYSTORE_PASSWORD` in your Github project's secrets (it must be longer than 6 characters)
    - Complete all the following steps

<p align="center">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/1%20keystore.png" width="600">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/2%20keystore.png" width="600">
</p>


2. Application deployment parameters :
    - Make sure that in your `NameProject.csproj` file, you remove this :
        - `net7.0-ios`
        - `net7.0-maccatalyst`

<p align="center">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/2%20settingdeploy.png" width="600">
</p>

- In the `AndroidManifest.xml` file (located in `Platforms-Android-AndroidManifest.xml`), replace its contents with the following:

      <?xml version="1.0" encoding="utf-8"?>
      <manifest xmlns:android="http://schemas.android.com/apk/res/android" package="<YourNamePackage>">
          <uses-sdk android:minSdkVersion="21" android:targetSdkVersion="30" />
          <application android:allowBackup="true" android:usesCleartextTraffic="true" android:icon="@mipmap/appicon" android:supportsRtl="true"></application>
          <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
          <uses-permission android:name="android.permission.INTERNET" />
      </manifest>
    
- Replace `<YourNamePackage>` with the name you chose when you created the project.

<p align="center">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/namepackage.png" width="600">
</p>

## III: Github deployment configuration 📤
1. Add the keystore information as secrets in GitHub:
    - Go to your repository page on GitHub, click on "Settings", then on "Secrets". Add the following information as secrets:
        - `KEYSTORE_PASSWORD`: The keystore password.
        - `KEYSTORE_ALIAS`: The key alias you defined with the KeyTool command.
        - `KEY_PASSWORD`: The key password (it must be identical to the KeyStore password)
        - `FIREBASE_TOKEN`: The Firebase token
                
<p align="center">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/createsecret.png" width="1000">
</p>

2. To obtain the `FIREBASE_TOKEN` :
    - Install the Firebase CLI tools if you haven't already done so. You can do this using npm (Node Package Manager) with the following command at the root of your project: `npm install -g firebase-tools`.
    - Once you've installed Firebase CLI, you can log in to your Firebase account using the following command: `firebase login`.
    - After logging in, you can obtain a Firebase token using the following command: `firebase login:ci` 

<p align="center">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/firebasetoken.png" width="400">
</p>

3. Create a workflow file for GitHub Actions:
    - Create a new file in the `.github/workflows` directory of your repository. You can call it `deploy-to-firebase.yml`. This file will contain the configuration of your GitHub Actions workflow.
    - Depending on the type of your repository (private or public), follow the appropriate instructions below :


:closed_lock_with_key: **For a private repository :**
  - Copy the contents of this file: [Workflow Deploy for private repository](https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/WorkflowDeployStepPrivate.yml)

:unlock: **For a public repository :**
  - Copy the contents of this file: [Workflow Deploy for public repository](https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/WorkflowDeployStepPublic.yml)
  - You need to encode the keystore file, as it contains confidential data. To do this, run the following command on your local (Windows) project:
    ```
    certutil -encode my-release-key.keystore keystore_base64.txt
    ```
  - Next, you need to put the contents of the `keystore_base64.txt` file in Github's secrets to be able to retrieve the keystore file in the workflow.

<p align="center">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/1%20keystorebase64.png" width="600">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/2%20keystorebase64.png" width="600">
</p>

4. In the worflow you've copied, I've commented out all the places you need to modify:

  - `<YourSolutionPath>`
  - `<YourKeystorePath>`
  - `<YourProjectPath>`
  - `<YourApkFile>`
  - `<YourAppID>`

5. Replace the name of your APK and the ID of your application on this line:
   ```bash
    args: appdistribution:distribute ./publish/android/<YourApkFile> --app <YourAppID>
    ```
    
   - Your APK name and application ID can be found here 

<p align="center">
  <img src="https://github.com/eloiJhn/DeployApp/blob/main/DeployApp/images/namepackage.png" width="600">
</p>

## IV: Conclusion 🔚
And that's it! With this configuration, every time you push changes to the main branch of your repository, GitHub Actions will build your .NET MAUI application and deploy it to Firebase App Distribution.

## V: Known problems 🥺
If you encounter any problems when using this configuration, please make sure that :

- Your .NET MAUI application can be built and published locally on your machine.
- You are using the correct version of .NET (at present, .NET MAUI requires .NET 7).
- You have correctly configured your `AndroidManifest.xml` file.
- Your keystore information is correct (aliases, passwords, etc.).
- You have correctly configured the `deploy-to-firebase.yml` file.

If you continue to encounter problems after checking these points, please feel free to open an issue in this repository in the "Issues" tab. I'll be happy to help!

---

## VI : Sources ℹ️

- [Firebase documentation](https://firebase.google.com/docs?authuse)
- [Documentation Github Actions](https://docs.github.com/en/actions/deployment/about-deployments/deploying-with-github-actions)



2023 Eloi J. - All rights reserved.
