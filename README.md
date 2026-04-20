#  LAB 19 : Snake – Résolution détaillée étape par étape

##  Introduction

Ce laboratoire présente une analyse complète de l’application Android **Snake.apk** dans le cadre du challenge Mobile Hard du PwnSec CTF 2024.

L’objectif est de :

* Analyser l’APK
* Contourner les mécanismes de protection (root, émulateur, Frida)
* Comprendre la logique interne
* Extraire le flag

##  Étape 1 : Préparation de l’environnement
Outils nécessaires :

- apktool — pour décompiler/recompiler l'APK
  
- jadx — pour l'analyse statique Java
  
- uber-apk-signer — pour signer l'APK patché
  
- adb — pour installer et interagir avec l'application


### Télécharger : `Snake.apk`

<img width="281" height="67" alt="image" src="https://github.com/user-attachments/assets/02932d83-8298-45ad-9968-c985978c36ac" />

## Étape 2 : Analyse statique approfondie avec Jadx

Cette étape consiste à analyser le code décompilé de l’application afin de comprendre :

- la logique principale de l’application

- les conditions d’exécution du flag

- les classes critiques

- les protections mises en place

Ouvrir l'APK dans Jadx et naviguer vers MainActivity.onCreate().

### Trois points critiques sont identifiés :

1. Détection de root — l'app appelle isDeviceRooted() et se ferme si root détecté
   
  <img width="712" height="113" alt="image" src="https://github.com/user-attachments/assets/f9646c93-a404-445b-a9de-63c7ac0fa22d" />

<img width="478" height="98" alt="image" src="https://github.com/user-attachments/assets/6b4aa87c-440c-4cfc-b8ef-9ea490cdbc60" />

   
2. Vérification de permission — READ_EXTERNAL_STORAGE requise
   
   <img width="687" height="347" alt="image" src="https://github.com/user-attachments/assets/635292c9-dbc9-4e6d-8a47-5c59c32977ed" />

3.Méthode principale C() — lit et traite un fichier YAML depuis /sdcard/Snake/

<img width="593" height="272" alt="image" src="https://github.com/user-attachments/assets/04da864c-db4e-4b3e-8aea-116a1ede8b42" />

## Étape 3 : Patch Smali

### Décompiler avec apktool :


<img width="485" height="203" alt="image" src="https://github.com/user-attachments/assets/6649367b-6a02-4bc7-841f-d064c87a0230" />

- Modifier les checks dans le fichier smali de MainActivity pour bypasser la détection de root et forcer l'exécution de C().
  
### Recompiler :

<img width="466" height="125" alt="image" src="https://github.com/user-attachments/assets/f8d3d7b9-26b1-4ae2-b6c2-7bda0f95eb1a" />

### Signer :

<img width="478" height="326" alt="image" src="https://github.com/user-attachments/assets/96e8810f-fb99-4539-818d-be450e40f5ef" />

### Installer :

<img width="323" height="93" alt="image" src="https://github.com/user-attachments/assets/3e8a77bd-38cd-41ee-83af-a67ca3e4fd18" />

## Étape 4 : Création du payload YAML

Créer le fichier /sdcard/Snake/Skull_Face.yml avec le contenu attendu par la méthode C().


<img width="239" height="23" alt="image" src="https://github.com/user-attachments/assets/3c893c61-418f-4667-b21b-9bffbcc9f54e" />


<img width="372" height="53" alt="image" src="https://github.com/user-attachments/assets/ab62f24e-ff7a-450d-811f-8a3e22ef9581" />

<img width="417" height="98" alt="image" src="https://github.com/user-attachments/assets/49f5f7a5-33b6-4dd1-8bd7-7f9f1863386e" />

## Étape 5 : Lancement avec Intent

<img width="412" height="31" alt="image" src="https://github.com/user-attachments/assets/f1cab90e-592f-485a-ba9a-8a7af460ae9c" />


## Étape 6 : Récupération du flag via logcat

<img width="826" height="55" alt="image" src="https://github.com/user-attachments/assets/9a26bdad-aac5-4389-9a3d-d882e41e55df" />

- Le flag apparaît dans les logs est :

  <img width="826" height="85" alt="image" src="https://github.com/user-attachments/assets/b1f3b572-32d5-4ab1-9ce0-4de2ce8d0943" />












