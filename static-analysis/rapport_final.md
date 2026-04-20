# Rapport d'Audit de Sécurité Mobile - LAB 6
**Application :** InsecureBankv2  
**Analyste :** Noussair (ENSA Marrakech)  
**Date :** 17 Avril 2026  

---

## 1. Informations Générales
* **Fichier :** `app-debug.apk`
* **Taille :** 3.4 MB
* **Hash SHA-256 :** `b18af2a0e44d7634bbcdf93664d9c78a2695e050393fcfbb5e8b91f902d194a4`
* **Package Name :** `com.android.insecurebankv2`
* **Outils utilisés :** MobSF v4.0.6 (Docker Edition), Debian 10 Mobexler.

## 2. Résumé Exécutif
L'analyse statique de l'application InsecureBankv2 révèle un niveau de risque **CRITIQUE** (Score MobSF : 28/100). L'application présente des failles de conception majeures affectant la confidentialité des données utilisateurs. Les principales vulnérabilités concernent le stockage de clés de chiffrement en dur, l'exportation non sécurisée de composants sensibles et l'absence totale de protection du trafic réseau. En l'état, cette application ne respecte pas les standards de sécurité bancaire élémentaires.

## 3. Top 3 Vulnérabilités Critiques

### V1 : Stockage de secrets cryptographiques en clair
* **Sévérité :** Critique
* **Référence MASVS :** MASVS-STORAGE-1
* **Description :** Une clé de chiffrement AES (`w41pUA...`) est codée en dur dans le code source.
* **Preuve :** Identifié dans la section "Hardcoded Secrets" et le code décompilé.
* **Impact :** Un attaquant peut extraire cette clé et déchiffrer l'intégralité des données locales des clients (historiques, identifiants).
* **Remédiation :** Utiliser le **Android Keystore System** pour générer et stocker les clés de manière isolée au niveau matériel.

### V2 : Communication réseau non chiffrée (Cleartext Traffic)
* **Sévérité :** Élevée
* **Référence MASVS :** MASVS-NETWORK-1
* **Description :** L'application autorise le trafic HTTP via le flag `usesCleartextTraffic`.
* **Preuve :** Absent de `network_security_config.xml` et paramètre implicite dans le Manifeste (SDK 22).
* **Impact :** Risque de vol de données via des attaques Man-In-The-Middle (MITM) sur des réseaux Wi-Fi non sécurisés.
* **Remédiation :** Implémenter un fichier `network_security_config.xml` et forcer `https://` pour tous les domaines.

### V3 : Composants d'application exportés sans protection
* **Sévérité :** Élevée
* **Référence MASVS :** MASVS-PLATFORM-1
* **Description :** Plusieurs activités (ex: `.DoTransfer`, `.ViewStatement`) sont marquées `android:exported="true"`.
* **Preuve :** Manifeste XML, 4 activités identifiées comme vulnérables à StrandHogg 2.0.
* **Impact :** Des applications malveillantes tierces peuvent invoquer ces écrans pour effectuer des transferts ou lire des relevés sans authentification.
* **Remédiation :** Passer `android:exported` à `false` pour tous les composants qui n'ont pas besoin d'être accessibles par d'autres apps.

## 4. Recommandations Priorisées
1. **Sécurisation des secrets :** Migrer toutes les clés et tokens vers le Keystore Android.
2. **Protection Réseau :** Désactiver le trafic en clair et implémenter le Certificate Pinning.
3. **Durcissement du Manifeste :** Désactiver `android:debuggable`, `android:allowBackup` et restreindre les composants exportés.
4. **Hachage Fort :** Remplacer MD5/SHA-1 par Argon2 ou PBKDF2 pour la gestion locale des secrets.

## 5. Annexes Techniques
* **Permissions dangereuses identifiées :** SEND_SMS, READ_CONTACTS, WRITE_EXTERNAL_STORAGE.
* **Endpoints détectés :** server_ip, server_port (Entrées manuelles utilisateur).
