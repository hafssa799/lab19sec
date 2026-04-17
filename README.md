#  LAB 19 : Snake – Résolution détaillée étape par étape

##  Introduction

Ce laboratoire présente une analyse complète de l’application Android **Snake.apk** dans le cadre du challenge Mobile Hard du PwnSec CTF 2024.

L’objectif est de :

* Analyser l’APK
* Contourner les mécanismes de protection (root, émulateur, Frida)
* Comprendre la logique interne
* Extraire le flag

##  Étape 1 : Préparation de l’environnement


### Télécharger : `Snake.apk`

<img width="281" height="67" alt="image" src="https://github.com/user-attachments/assets/02932d83-8298-45ad-9968-c985978c36ac" />

## Étape 2 : Analyse statique approfondie avec Jadx

Cette étape consiste à analyser le code décompilé de l’application afin de comprendre :

- La logique principale de l’application
- Les conditions d’exécution du flag
- Les classes critiques
- Les protections mises en place

 ## 1. Analyse avec Jadx-GUI

L’APK Snake.apk a été ouvert avec Jadx-GUI afin d’extraire le code source Java.

jadx-gui snake.apk

Capture 1 : APK ouvert dans Jadx
## 2. Structure de l’application

Après analyse, on identifie :

📦 Package principal : com.pwnsec.snake
🚀 Classe principale : MainActivity

Capture 2 : Structure du package
## 3. Logique de MainActivity

La classe MainActivity contient la logique exécutée au démarrage.

🔑 Condition principale

L’application vérifie un Intent Extra :

SNAKE = BigBoss

👉 Si la condition est respectée :

L’application continue son exécution
Sinon elle bloque certaines fonctionnalités
Capture 3 : MainActivity

## 4. Accès au stockage

Si la condition est validée :

Chemin utilisé :
/sdcard/
ou /storage/emulated/0/
Recherche :
📂 dossier Snake
📄 fichier Skull_Face.yml

## 5. SnakeYAML

Le fichier YAML est parsé avec :

new Yaml();

👉 Conversion YAML → objets Java

⚠️ Risque de désérialisation non sécurisée

## 6. Classe BigBoss

📦 com.pwnsec.snake.BigBoss

Fonctionnement :
Charge une librairie native :
System.loadLibrary("native-lib");
Contient une condition critique :
input.equals("Snaaaaaaaaaaaaaake")
## 7. Génération du flag

Si la condition est vraie :

stringFromJNI();

👉 Le flag est généré dynamiquement et affiché dans les logs :

Log.d() / Log.i()
## 8. Protections identifiées
🔒 Détection de root
📱 Détection d’émulateur
🧪 Détection de Frida


## Étape 3 : Patch Smali pour bypass des protections

Dans cette étape, l’APK a été décompilé afin d’analyser le code Smali et comprendre les mécanismes internes de l’application, notamment les vérifications liées à l’environnement d’exécution.

### Outils utilisés

Apktool (décompilation / recompilation)

Jadx (analyse du code Java décompilé)

VS Code (lecture des fichiers Smali)

ADB (tests sur appareil)

##  Décompilation de l’APK

L’APK a été décompilé afin d’accéder au code source bas niveau (Smali).

<img width="488" height="169" alt="image" src="https://github.com/user-attachments/assets/050ede83-a813-442b-b057-3f4ab73f364c" />

## Localiser les zones sensibles

- Une analyse a été effectuée dans Jadx afin de trouver les mécanismes de détection.

Recherche des mots-clés :
root
emulator
frida
su
debug
📸 Capture à ajouter :

Analyse du code Smali

Exploration des fichiers Smali générés.

Chemin :
smali/com/pwnsec/snake/
📸 Capture à ajouter :
<img width="728" height="142" alt="image" src="https://github.com/user-attachments/assets/b54f36aa-defd-4faa-b386-f7b4df504244" />

Ouverture d’un fichier contenant une condition
⚙️ Analyse des conditions logiques

Le code Smali contient des instructions de contrôle de flux :

if-eqz → condition si égal à 0
if-nez → condition si différent de 0
goto → saut dans le code
return → retour de fonction
📸 Capture à ajouter :
Exemple de code Smali avec condition (if-eqz ou if-nez)
🔄 Rebuild de l’application

L’application a été reconstruite après analyse.

Commande :
apktool b snake_smali -o snake_rebuilt.apk
📸 Capture à ajouter :
Build réussi sans erreur
📱 Test de l’application

Installation de l’APK reconstruit pour vérifier le comportement.

Commande :
adb install -r snake_rebuilt.apk
📸 Capture à ajouter :
Installation réussie sur appareil ou émulateur
