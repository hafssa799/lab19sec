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

la logique principale de l’application

les conditions d’exécution du flag

les classes critiques

les protections mises en place

## 1. Analyse avec Jadx-GUI

L’APK Snake.apk a été ouvert avec Jadx-GUI afin d’extraire le code source Java et analyser le comportement de l’application.

<img width="960" height="226" alt="image" src="https://github.com/user-attachments/assets/8b13bcf2-648b-47ca-9074-8c2c09b49610" />


## 2. Structure générale de l’application

Après analyse, on identifie la structure suivante :

Package principal : com.pwnsec.snake

Classe principale : MainActivity

<img width="713" height="390" alt="image" src="https://github.com/user-attachments/assets/433c0f53-8b94-4931-8a61-3cf864908ea4" />


Classe critique : BigBoss

<img width="658" height="400" alt="image" src="https://github.com/user-attachments/assets/76c34006-e611-4893-807e-8a4126d6bac9" />

Méthode principale : onCreate()

<img width="771" height="405" alt="image" src="https://github.com/user-attachments/assets/c02f06c6-81c1-475a-9320-511f32a7c559" />

## 3. Analyse de la méthode onCreate()

La méthode onCreate() de MainActivity représente le point d’entrée principal de l’application Android.

Lors du lancement de l’application, les opérations suivantes sont exécutées :

🔐 Vérification de sécurité (anti-root)

L’application commence par vérifier si l’appareil est rooté via la fonction :

<img width="319" height="30" alt="image" src="https://github.com/user-attachments/assets/8bcfb203-f695-4c21-a9f9-572a8322a53c" />

- un message est affiché dans les logs (Root detected)
l’application est fermée immédiatement via :
finish();
System.exit(0);

## Vérification des permissions

L’application vérifie ensuite la permission :

<img width="643" height="163" alt="image" src="https://github.com/user-attachments/assets/a09a68ba-ef07-4d39-9604-ed7a5ae6e26e" />

- Cette permission est nécessaire pour accéder au stockage externe et lire le fichier YAML.

🚀 Déclenchement de la logique principale

Si la permission est accordée, la méthode suivante est appelée :

C();

Cette méthode contient la logique principale du challenge.

<img width="593" height="272" alt="image" src="https://github.com/user-attachments/assets/04da864c-db4e-4b3e-8aea-116a1ede8b42" />


💀 5. Classe BigBoss (classe critique)

La classe BigBoss est la cible principale du challenge.

Elle contient les éléments suivants :

Chargement d’une librairie native :
System.loadLibrary("snake")
Exécution d’une méthode native (JNI)
Vérification d’une chaîne spécifique :
Snaaaaaaaaaaaaaake

Si la condition est respectée :
👉 le flag est généré et affiché dans les logs Android (logcat)

<img width="658" height="400" alt="image" src="https://github.com/user-attachments/assets/76c34006-e611-4893-807e-8a4126d6bac9" />

💣 6. Vulnérabilité identifiée

L’application utilise la librairie SnakeYAML pour parser un fichier externe :

Skull_Face.yml

Cette désérialisation est vulnérable (CVE-2022-1471) et permet :

l’instanciation de classes Java arbitraires
l’exécution de code non prévu par l’application

👉 Cela permet de déclencher la classe BigBoss via un payload YAML.

🛡️ 7. Protections présentes

L’application intègre plusieurs mécanismes anti-reverse :

Détection de root (fichiers su, apps root)
Détection d’émulateur (propriétés système)
Détection de Frida (via logique native)
Fermeture forcée de l’application si environnement suspect
🎯 8. Conclusion de l’analyse

L’objectif du challenge est de :

contourner les protections anti-reverse
exploiter la vulnérabilité SnakeYAML
déclencher l’instanciation de la classe BigBoss
récupérer le flag via les logs Android

Étape 3 : Patch Smali pour bypass des protections (Root / Emulator / Frida)

Cette étape consiste à modifier le code Smali de l’application afin de contourner les mécanismes de protection anti-reverse engineering.

L’objectif est de neutraliser les vérifications de sécurité pour permettre l’exécution normale de l’application.

## 1. Décompilation de l’APK

L’APK est décompilé avec apktool afin d’accéder au code Smali :

apktool d snake.apk -o snake_smali
## 2. Accès au code source Smali

Navigation vers le dossier principal :

cd snake_smali/smali/com/pwnsec/snake/

📸 Capture 1 : structure du dossier Smali
(insérer ici capture)

🔍 3. Identification des protections

Les protections suivantes doivent être recherchées :

Root detection (su, /system/bin/su)
Emulator detection (ro.hardware, goldfish, emulator)
Frida detection
Build tags (test-keys)

👉 Recherche recommandée dans Jadx avant patch :

root
su
frida
emulator
test-keys

📸 Capture 2 : méthode de détection dans Jadx
(insérer ici capture)

💀 4. Analyse des conditions Smali

Dans le code Smali, les protections sont généralement sous forme de conditions :

if-eqz → si égal à 0
if-nez → si différent de 0

Exemple :

if-eqz v0, :safe
invoke-static {}, Ljava/lang/System;->exit(I)V

👉 Ici, si la condition est vraie → l’application se ferme

🛠️ 5. Méthodes de bypass
✔ Méthode 1 : Forcer retour FALSE

Remplacer la fonction de détection par :

const/4 v0, 0x0
return v0

👉 Cela signifie :

aucune détection = false
l’application continue normalement
✔ Méthode 2 : suppression du bloc dangereux

Supprimer ou neutraliser les lignes :

System.exit(0)
finish()
✔ Méthode 3 : bypass conditionnel

Remplacer :

if-eqz v0, :cond_safe

par un saut direct :

goto :cond_safe
⚙️ 6. Fichiers à patcher

Les modifications doivent être appliquées dans :

MainActivity.smali
Classes de détection (Root / Emulator / Frida)

📸 Capture 3 : modification Smali avant/après
(insérer ici capture)

🔁 7. Rebuild de l’APK

Après modification :

apktool b snake_smali -o snake_patched.apk
🔏 8. Signature de l’APK

L’APK doit être signé avant installation :

apksigner sign --ks mykeystore.jks snake_patched.apk
📲 9. Installation de l’APK patché
adb install -r snake_patched.apk
🎯 10. Résultat attendu

Après patch :

l’application ne se ferme plus
les protections anti-root sont désactivées
l’exécution de la logique principale devient possible
