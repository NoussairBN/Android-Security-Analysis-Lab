```markdown
# Audit de Sécurité Mobile - Analyse Statique avec MobSF

Ce dépôt documente la réalisation du **Laboratoire 6** (Analyse statique d'un APK) dans le cadre du cycle ingénieur en **Cyberécurité et Télécommunications** à l'ENSA Marrakech.

## Objectifs du Projet
* Configurer un environnement d'analyse sécurisé sur **VM Mobexler**.
* Automatiser l'audit d'une application Android via **MobSF (Mobile Security Framework)**.
* Identifier les vulnérabilités de code, de configuration réseau et de permissions.
* Corréler les découvertes avec le standard international **OWASP MASVS**.

## Environnement de Travail
* **Machine Virtuelle :** Mobexler (Basée sur Debian 10 Buster).
* **Moteur d'analyse :** MobSF v4.0.6 déployé via **Docker**.
* **Cible d'analyse :** `InsecureBankv2.apk` (Application pédagogique volontairement vulnérable).

## Méthodologie appliquée
1. **Intégrité :** Calcul du Hash SHA-256 de l'échantillon pour garantir la traçabilité.
2. **Reverse Engineering :** Décompilation des fichiers DEX en Java via le moteur MobSF.
3. **Analyse du Manifeste :** Audit des composants exportés, des drapeaux de debug et des permissions abusives.
4. **Analyse Réseau :** Vérification de la politique de sécurité réseau (Trafic HTTP en clair).
5. **Analyse Statique (SAST) :** Recherche de secrets hardcodés (clés AES) et d'algorithmes de hachage faibles (MD5).

## Résumé des Résultats
* **Score de Sécurité global :** 28/100 (Niveau Critique).
* **Vulnérabilités majeures :**
    * Clé de chiffrement AES exposée en dur dans le code.
    * Trafic réseau non chiffré (Cleartext autorisé).
    * Activités sensibles exportées (Risque de StrandHogg 2.0).

## Structure du Dépôt
* `/static-analysis/rapport_final.md` : Rapport d'audit complet avec preuves et remédiations.
* `/static-analysis/data/` : Fichiers extraits (Permissions, Endpoints, MASVS).
* `/environment-setup/` : Guide de résolution des problèmes système rencontrés.
