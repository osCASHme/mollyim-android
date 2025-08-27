# osCASH.me Molly Fork - Critical Build Fixes & Solutions

> **🚨 KRITISCHE REFERENZ:** Alle wichtigen Build-Probleme und deren Lösungen für konsistente Entwicklung

---

## 🔑 **APK-Signierung: Das Herzstück der Kompatibilität**

### **Problem:** APK Installation scheitert mit "Package appears invalid"

**Root Cause:** APK-Signatur Schema Inkompatibilität zwischen Original Molly und unseren Builds

**Lösung (KRITISCH):**
```kotlin
// app/build.gradle.kts - signingConfigs section
create("ci") {
  println("Signing release build with keystore: '$path'")
  storeFile = file(path)
  storePassword = System.getenv("CI_KEYSTORE_PASSWORD")
  keyAlias = System.getenv("CI_KEYSTORE_ALIAS")
  keyPassword = System.getenv("CI_KEYSTORE_PASSWORD")
  // Match original Molly signing schema exactly
  enableV1Signing = false  // Original Molly uses v2+v3 only
  enableV2Signing = true   // APK signing scheme v2 (required)
  enableV3Signing = true   // APK signing scheme v3 (required) 
  enableV4Signing = false  // Disabled like original Molly
}
```

**Verifikation:**
```bash
# Original Molly MUSS haben:
Verified using v1 scheme (JAR signing): false     ✅
Verified using v2 scheme (APK Signature Scheme v2): true   ✅  
Verified using v3 scheme (APK Signature Scheme v3): true   ✅
Verified using v4 scheme (APK Signature Scheme v4): false  ✅

# Unser Build MUSS identisch sein!
```

**Erste Session:** Entdeckt 26. August 2025  
**Zweite Session:** Vergessen und neu entdeckt 27. August 2025  
**⚠️ LESSON LEARNED:** Kritische Fixes MÜSSEN dokumentiert werden!

---

## 🔐 **Keystore-Passwort Probleme**

### **Problem:** Shell-Escaping bei Sonderzeichen in Passwörtern

**Fehlerhafte Passwörter:**
- `osCASH@2025!` ❌ (Shell-Escaping Probleme)
- `osCASH#2025$` ❌ (Shell-Escaping Probleme)

**Funktionierende Lösung:**
- `osCASH2025` ✅ (Nur alphanumerisch)

**Environment Configuration (.env):**
```env
CI_BUILD_VARIANTS=prod(Gms|Foss)
CI_KEYSTORE_PATH=/molly/app/certs/osCASH-release.keystore
CI_KEYSTORE_PASSWORD=osCASH2025  # KEINE SONDERZEICHEN!
CI_KEYSTORE_ALIAS=oscash-key
```

---

## 🐳 **Docker Build-System Fixes**

### **Problem:** Git-Funktionen nicht verfügbar in Docker

**Git Error Handling (build.gradle.kts):**
```kotlin
fun getCommitTag(): String {
  return try {
    assertIsGitRepo()
    val tag = providers.exec {
      commandLine("git", "describe", "--tags", "--exact-match")
    }.standardOutput.asText.get().trim()
    tag.takeIf { it.isNotEmpty() } ?: "untagged"
  } catch (e: Throwable) {
    logger.warn("Failed to get Git commit tag: ${e.message}. Using default value.")
    "untagged"
  }
}
```

### **Problem:** Build-Tools Version Inkompatibilität

**Dockerfile Fix:**
```dockerfile
ARG BUILD_TOOLS_VERSION=34.0.0  # NOT 35.0.0 - F-Droid compatibility!
```

**Aber aktuell verwenden wir:**
```dockerfile
ARG BUILD_TOOLS_VERSION=35.0.0  # Works in current setup
```

---

## 📱 **APK-Varianten & Naming Schema**

### **Konsistente APK-Benennung:**
```bash
# v7.53.4 (osCASH Test-Releases)
osCASH-v7.53.4-test6-FOSS-Store.apk
osCASH-v7.53.4-test6-FOSS-Website.apk  
osCASH-v7.53.4-test6-GMS-Website.apk

# v7.53.5-1 (Molly-Repro Production)
Molly-Repro-v7.53.5-1-FOSS-Store.apk
Molly-Repro-v7.53.5-1-FOSS-Website.apk
Molly-Repro-v7.53.5-1-GMS-Website.apk
```

### **gradle.properties Branding:**
```properties
# Molly-Repro: 1:1 Reproducible Build by osCASH.me
baseAppTitle=Molly-Repro
baseAppFileName=Molly-Repro
basePackageId=im.molly.app  # Keep same for upgrade compatibility!
```

---

## ⚠️ **Häufige Fallstricke**

### **1. Package ID Änderungen**
- **❌ NIEMALS:** Package ID ändern → Users können nicht upgraden
- **✅ IMMER:** `im.molly.app` beibehalten für Kompatibilität

### **2. Signing Schema Vergessen**
- **❌ Problem:** Nur v4Signing = false setzen
- **✅ Lösung:** Explizit alle 4 Signing-Optionen definieren

### **3. APK-Vergleich ohne Signatur-Check**
- **❌ Problem:** Nur App-Funktionalität testen
- **✅ Lösung:** Immer apksigner verify verwenden

### **4. Keystore-Passwort Sonderzeichen**
- **❌ Problem:** Shell-Escaping nicht beachten
- **✅ Lösung:** Alphanumerische Passwörter verwenden

---

## 🔧 **Standard-Workflow für neue Molly Versionen**

### **1. Upstream-Updates holen**
```bash
git remote add upstream https://github.com/mollyim/mollyim-android.git
git fetch upstream
git checkout v{VERSION}  # z.B. v7.53.6-1
git stash push -m "Save osCASH configuration"
git stash pop
```

### **2. Build-Konfiguration prüfen**
- [ ] Signing Schema (v2+v3 enabled, v1+v4 disabled)
- [ ] Environment (.env) ohne Sonderzeichen
- [ ] Package ID unverändert

### **3. Build & Verify**
```bash
cd reproducible-builds
docker compose up --build
apksigner verify --print-certs --verbose original-molly.apk
apksigner verify --print-certs --verbose our-build.apk
```

### **4. Release erstellen**
- APK-Benennung: `Molly-Repro-v{VERSION}-{VARIANT}.apk`
- SHA256-Checksums erstellen
- GitHub Release mit Tag

---

## 📊 **Erfolgreiche Builds (Historie)**

| Version | Datum | Status | Signing Schema | APK Kompatibilität |
|---------|-------|---------|---------------|-------------------|
| v7.53.4-test6 | 2025-08-26 | ✅ Success | v2+v3 | ✅ Perfect |
| v7.53.5-1 | 2025-08-27 | ✅ Success | v2+v3 | ✅ Perfect |

---

## 🧠 **Wissenserhaltung für Claude Code Sessions**

### **Session-Start Checklist:**
1. **✅ SESSION_SUMMARY lesen:** `/osCASH.me/mollyim-android/SESSION_SUMMARY_2025-08-27.md`
2. **✅ Critical Fixes review:** Diese Datei hier
3. **✅ FAQ konsultieren:** `osCASHme-MOLLY-FAQ.md`
4. **✅ CLAUDE.md für Kontext:** Context-Setup Anleitung

### **Bei kritischen Fehlern:**
1. **ERST** in dieser Datei nachschauen
2. **DANN** in Session-Summary suchen
3. **ZULETZT** Problem neu lösen

### **Nach neuen Fixes:**
1. Diese Datei **SOFORT** erweitern
2. Session-Summary aktualisieren  
3. FAQ mit Details ergänzen

---

**🤖 Generated with [Claude Code](https://claude.ai/code)**  
**Co-Authored-By: Claude & Peter (osCASH.me)**

*Last Updated: 27. August 2025 - Molly-Repro v7.53.5-1 Release*