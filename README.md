# Audit de Sécurité Mobile - Analyse Statique (SAST)

Ce dépôt documente la réalisation du **Laboratoire 6** du cursus Ingénieur en **Cyberécurité et Télécommunications** à l'ENSA Marrakech. L'objectif principal était de mener un audit de sécurité complet sur une application Android vulnérable en utilisant des méthodologies professionnelles.

## Objectifs du Projet
- Configurer un environnement d'analyse sécurisé et fonctionnel (Debian/Mobexler).
- Automatiser l'audit d'un APK via **MobSF (Mobile Security Framework)**.
- Identifier les vulnérabilités de code, de réseau et de configuration du Manifeste.
- Corréler les résultats avec le standard international **OWASP MASVS**.

## Environnement Technique
- **Système d'exploitation :** VM Mobexler (Debian 10 Buster).
- **Moteur d'analyse :** MobSF v4.0.6 déployé via **Docker**.
- **Cible d'analyse :** `InsecureBankv2.apk` (Application bancaire intentionnellement vulnérable).

## Méthodologie & Étapes Réalisées

### 1. Préparation de l'environnement (SysAdmin)
Résolution des erreurs critiques de dépôts `apt` liées à la fin de vie (EOL) de Debian 10. Redirection des sources vers les serveurs d'archives pour permettre l'installation des dépendances.

### 2. Analyse du Manifeste & Permissions
- Identification des **permissions excessives** (SMS, Contacts, Stockage externe).
- Détection de la surface d'attaque **IPC** via les composants exportés (`android:exported="true"`).
- Analyse des drapeaux de sécurité (`android:debuggable`, `android:allowBackup`).

### 3. Analyse Réseau
- Vérification de la politique de trafic en clair.
- Identification de l'absence de fichier `network_security_config.xml`.
- Détection des risques d'attaques **MITM** (Man-in-the-Middle) dus à l'utilisation du protocole HTTP.

### 4. Audit du Code (SAST)
- Recherche de **secrets hardcodés** (clés AES, tokens de session).
- Identification d'algorithmes de hachage obsolètes (MD5, SHA-1).
- Détection de fuites d'informations via les logs système (Logcat).

## Résumé des Résultats
| Métrique | Valeur |
| :--- | :--- |
| **Security Score** | **28/100** (Critique) |
| **Vulnerabilités High/Critical** | 7 identifiées |
| **Activités Exportées** | 4 (Potentiellement vulnérables à StrandHogg 2.0) |

## 📁 Structure du dépôt
- [`/static-analysis/rapport_final.md`](./static-analysis/rapport_final.md) : Rapport d'audit détaillé avec preuves et remédiations.
- [`/static-analysis/data/`](./static-analysis/data/) : Données brutes extraites (Permissions, Endpoints, Corrélation MASVS).
- [`/environment-setup/fix-debian-sources.md`](./environment-setup/fix-debian-sources.md) : Guide technique sur la résolution des erreurs de dépôts Debian.
