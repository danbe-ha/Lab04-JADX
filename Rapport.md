# 📑 Rapport d'Audit de Sécurité Mobile
**Cible :** UnCrackable Level 1  
**Auditeur :** Ali Benrioui

---

## 1. Résumé Exécutif
L'analyse de l'application UnCrackable Level 1 a révélé des vulnérabilités critiques liées à la gestion des secrets et à la configuration du package. Bien que l'application implémente des contrôles de sécurité au démarrage (Anti-Root/Anti-Debug), ceux-ci sont inefficaces contre une analyse statique approfondie.

---

## 2. Méthodologie
L'audit a été réalisé selon les étapes suivantes :
* **Vérification d'intégrité** : Calcul de hash SHA-256 et inspection des en-têtes hexadécimaux pour valider l'authenticité de l'APK.
* **Analyse Statique Spécialisée** : Utilisation de **JADX-GUI** pour la décompilation et l'analyse du Manifeste.
* **Analyse Comparative** : Conversion du bytecode via **dex2jar** et inspection via **JD-GUI** pour valider les constantes identifiées.



---

## 3. Constats Détaillés

### 3.1 Stockage de secrets en clair (Sévérité : CRITIQUE)
* **Localisation** : Classe `sg.vantagepoint.uncrackable1.a`.
* **Détail** : Une clé AES de 128 bits (`8d127684cbc37c17616d806cf50473cc`) et un secret encodé en Base64 sont présents en clair dans le code source.
* **Impact** : Un attaquant peut déchiffrer les données sensibles sans aucune interaction avec l'application.

### 3.2 Mécanismes de protection contournables (Sévérité : MOYENNE)
* **Localisation** : Classe `MainActivity` et package `sg.vantagepoint.a`.
* **Détail** : L'application vérifie la présence du binaire `su` et l'état du flag de débogage.
* **Impact** : Ces protections sont purement logiques et peuvent être neutralisées par analyse statique ou modification du code.

### 3.3 Configuration du Manifeste (Sévérité : FAIBLE)
* **Détail** : L'attribut `android:allowBackup` est défini sur `true`.
* **Impact** : Permet l'extraction des données privées de l'application via une sauvegarde ADB.

---

## 4. Recommandations de Sécurité
Pour sécuriser l'application, les mesures suivantes sont préconisées :
1. **Obfuscation** : Utiliser ProGuard ou DexGuard pour rendre le code difficilement lisible par les décompilateurs.
2. **Gestion des clés** : Migrer les secrets vers le **Android Keystore System** plutôt que de les coder en dur dans le code Java.
3. **Renforcement du Manifeste** : Désactiver `allowBackup` pour empêcher les fuites de données via les sauvegardes locales.