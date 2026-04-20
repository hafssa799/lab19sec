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
   
   <img width="771" height="405" alt="image" src="https://github.com/user-attachments/assets/c02f06c6-81c1-475a-9320-511f32a7c559" />
   
2. Vérification de permission — READ_EXTERNAL_STORAGE requise
   
3.Méthode principale C() — lit et traite un fichier YAML depuis /sdcard/Snake/

<img width="593" height="272" alt="image" src="https://github.com/user-attachments/assets/04da864c-db4e-4b3e-8aea-116a1ede8b42" />



















