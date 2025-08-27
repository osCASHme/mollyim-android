# 🔧 osCASH.me Molly Fork - Entwickler FAQ

> **Lebende Dokumentation der Erkenntnisse aus der gemeinsamen Entwicklung von Q (osCASH.me) und A (Claude Code)**
> **Ziel:** Reproduzierbare Builds, APK-Signierung und Android-Kompatibilität für Custom Signal/Molly Forks

---

## 🚀 **Projekt-Kontext**

**Was ist osCASH.me?**
Ein Privacy-fokussierter Molly Fork mit reaktivierter Signal Wallet für MOB und eUSD Zahlungen über die MobileCoin Blockchain.

**Entwicklungsumgebung:**
- Debian 12 LXC Container (Proxmox)
- Docker 28.3.3 mit Compose v2.39.1
- Reproducible Builds nach mollyim/mollyim-android Standard

---

## 📱 **APK-Signierung & Installation**

### **Q: "Molly - Die App wurde nicht installiert, da das Paket offenbar ungültig ist" - Was ist die Ursache?**

**A:** Das Problem liegt in der **APK-Signaturkompatibilität**. Nach umfassender Analyse der Original Molly APK haben wir folgendes herausgefunden:

**Original Molly verwendet:**
```
Verified using v1 scheme (JAR signing): false
Verified using v2 scheme (APK Signature Scheme v2): true  
Verified using v3 scheme (APK Signature Scheme v3): true
Verified using v4 scheme (APK Signature Scheme v4): false
```

**Lösung:** Build-Konfiguration anpassen:
```kotlin
// app/build.gradle.kts
signingConfigs {
  create("ci") {
    // Multi-scheme signing matching original Molly
    enableV1Signing = false  // Original Molly uses v2+v3 only, not v1
    enableV2Signing = true   // APK signing scheme v2 (required)
    enableV3Signing = true   // APK signing scheme v3 (required) 
    enableV4Signing = false  // Disabled like original Molly
  }
}
```

### **Q: Welche Android-Versionen sind betroffen?**

**A:** Getestet auf:
- **Nokia 8.3 5G (Android 12)** - Installation schlägt fehl ohne korrekte v2+v3 Signierung
- **Samsung Galaxy A13 5G (Android 14)** - Zusätzliche Knox-Sicherheitsebene, braucht v2+v3

**Android 12+ erfordert zwingend v2+ Signaturen** für Custom APKs.

### **Q: Warum ist die build-tools Version wichtig?**

**A:** **Kritischer Punkt:** Build-tools 35+ verursachen F-Droid/apksigcopier Inkompatibilität.

**Problem:**
- Docker installiert automatisch build-tools 35.0.0
- F-Droid kann APKs von apksigner v35+ nicht mit apksigcopier verifizieren

**Lösung:** Im Dockerfile explizit build-tools 34.0.0 festlegen:
```dockerfile  
ARG BUILD_TOOLS_VERSION=34.0.0
```

---

## 🐳 **Docker & Reproducible Builds**

### **Q: Warum schlägt der Build mit "Keystore not found" fehl?**

**A:** **Volume-Mapping Problem**. Der Keystore muss korrekt gemountet werden:

**Docker-Compose Konfiguration:**
```yaml
# docker-compose.yml
services:
  assemble:
    volumes:
      - ./certs:/molly/app/certs:ro  # Keystore-Verzeichnis mounten
    environment:
      - CI_KEYSTORE_PATH=/molly/app/certs/osCASH-release.keystore
      - CI_KEYSTORE_PASSWORD=your_password
      - CI_KEYSTORE_ALIAS=your_alias
```

**Häufiger Fehler:** Verschachtelte Verzeichnisse (`certs/certs/keystore.jks` statt `certs/keystore.jks`)

### **Q: Wie erstellt man einen kompatiblen Release-Keystore?**

**A:** **4096-bit RSA mit langer Gültigkeit:**

```bash
# Im Docker Container ausführen  
docker run --rm --entrypoint="" -v "$PWD/certs:/certs" reproducible-molly sh -c \
"keytool -genkey -v \
  -keystore /certs/osCASH-release.keystore \
  -alias osCASH-key \
  -keyalg RSA \
  -keysize 4096 \
  -validity 10000 \
  -dname 'CN=osCASH.me, OU=Development, O=osCASH, L=Vienna, ST=Austria, C=AT' \
  -storepass 'secure_password' \
  -keypass 'secure_password'"
```

**Warum 4096-bit?** Bessere Sicherheit und Zukunftssicherheit gegenüber 2048-bit.

---

## 🔍 **APK-Analyse & Debugging**

### **Q: Wie analysiert man APK-Signaturen?**

**A:** **Mit Android SDK build-tools im Docker Container:**

```bash
# APK-Grundinfos
docker run --rm --entrypoint="" -v "$PWD:/workspace" reproducible-molly sh -c \
"/opt/android-sdk-linux/build-tools/34.0.0/aapt dump badging /workspace/your.apk"

# Signatur-Verifikation
docker run --rm --entrypoint="" -v "$PWD:/workspace" reproducible-molly sh -c \
"java -jar /opt/android-sdk-linux/build-tools/34.0.0/lib/apksigner.jar verify --print-certs --verbose /workspace/your.apk"
```

### **Q: Wie vergleicht man Original vs. selbst gebaute APK?**

**A:** **Systematischer Vergleich:**

```bash
# 1. Original Molly APK herunterladen
curl -L -o original-molly.apk "https://github.com/mollyim/mollyim-android/releases/download/v7.53.4-1/Molly-v7.53.4-1-FOSS.apk"

# 2. Beide APKs analysieren
# Original: 
docker run --rm --entrypoint="" -v "$PWD:/workspace" reproducible-molly sh -c \
"java -jar /opt/android-sdk-linux/build-tools/34.0.0/lib/apksigner.jar verify --print-certs --verbose /workspace/original-molly.apk"

# Eigene APK:
docker run --rm --entrypoint="" -v "$PWD:/workspace" reproducible-molly sh -c \
"java -jar /opt/android-sdk-linux/build-tools/34.0.0/lib/apksigner.jar verify --print-certs --verbose /workspace/your-built.apk"
```

---

## 🛠️ **Molly Build-System**

### **Q: Welche Probleme gibt es mit der Molly Documentation?**

**A:** **Issue #115 - Veraltete Pfade in der Dokumentation.**

**Veraltete Pfade** (funktionieren nicht):
```
apks/prodNonFree/release/Molly-prod-unsigned-$VERSION.apk
```

**Aktuelle korrekte Pfade:**
```
outputs/apk/prodGmsWebsite/release/Molly-unsigned-$VERSION.apk
outputs/apk/prodFossWebsite/release/Molly-unsigned-$VERSION-FOSS.apk
outputs/apk/prodFossStore/release/Molly-store-unsigned-$VERSION-FOSS.apk
```

### **Q: Welche APK-Varianten werden gebaut?**

**A:** **3 Hauptvarianten:**

1. **FOSS-Store** (F-Droid kompatibel)
   - Keine Google Services
   - Maximale Privacy
   - 84MB

2. **FOSS-Website** (Direct Download)  
   - Keine Google Services
   - OpenStreetMap statt Google Maps
   - 84MB

3. **GMS-Website** (Standard Android)
   - Google Services enthalten
   - FCM Push-Notifications  
   - 85MB

---

## 🔧 **Environment & Konfiguration**

### **Q: Welche Environment Variables werden benötigt?**

**A:** **Für Docker-Build (.env Datei):**

```env
CI_BUILD_VARIANTS=prod(Gms|Foss)
CI_KEYSTORE_PATH=/molly/app/certs/osCASH-release.keystore
CI_KEYSTORE_PASSWORD=your_secure_password
CI_KEYSTORE_ALIAS=your_key_alias
```

**Optionale Parameter:**
```env
CI_APP_TITLE=osCASH.me
CI_APP_FILENAME=osCASH
CI_PACKAGE_ID=me.oscash.molly
```

### **Q: Was ist die empfohlene Docker-Konfiguration?**

**A:** **Aktuelle docker-compose.yml:**

```yaml
services:
  assemble:
    image: reproducible-molly
    build:
      context: ..
    command: :app:assembleRelease :app:bundleRelease --no-daemon
    volumes:
      - ./certs:/molly/app/certs:ro
      - ./outputs:/molly/app/build/outputs
    environment:
      - GRADLE_OPTS
      - CI_APP_TITLE
      - CI_APP_FILENAME  
      - CI_PACKAGE_ID
      - CI_BUILD_VARIANTS
      - CI_KEYSTORE_PATH
      - CI_KEYSTORE_PASSWORD
      - CI_KEYSTORE_ALIAS
```

---

## 📈 **Performance & Optimierung**

### **Q: Wie lange dauert ein kompletter Build?**

**A:** **Typische Build-Zeiten:**

- **Erster Build:** ~20 Minuten (Downloads + Kompilierung)
- **Incremental Build:** ~5-10 Minuten (Cache genutzt)
- **Build mit neuen Dependencies:** ~15 Minuten

**Optimierungen:**
- Gradle-Cache nutzen
- Docker Layer-Caching
- SSD Storage für bessere I/O

### **Q: Welche Docker Image-Größe ist normal?**

**A:** **reproducible-molly Image: ~6GB**

Enthält:
- OpenJDK 17
- Android SDK 35
- Build-tools 34.0.0  
- Gradle 8.11.1
- Kotlin 2.0.20

---

## 🚨 **Häufige Probleme & Lösungen**

### **Q: "BUILD FAILED: validateSigningProdFossStoreRelease"**

**A:** **Keystore-Pfad oder -Permissions Problem:**

```bash
# 1. Keystore-Existenz prüfen
ls -la certs/
ls -la certs/*.keystore

# 2. Docker Volume-Mapping testen  
docker run --rm -v "$PWD/certs:/certs" reproducible-molly sh -c "ls -la /certs/"

# 3. Berechtigungen korrigieren
chmod 644 certs/your-keystore.jks
```

### **Q: Kotlin Version Warnung - kritisch?**

**A:** **Nicht kritisch für APK-Funktionalität:**

```
WARNING: The embedded-kotlin and kotlin-dsl plugins rely on features of Kotlin 2.0.20 
that might work differently than in the requested version 2.1.0.
```

**Bedeutung:**
- Nur Build-Tool Kompatibilitätswarnung
- APK funktioniert normal auf Android
- **Später:** Docker-Umgebung parallel zu Molly-upstream updaten

### **Q: "Package appears invalid" auf Samsung-Geräten?**

**A:** **Knox-Sicherheit kann zusätzliche Beschränkungen haben:**

1. **Settings → Biometrics and Security → Install unknown apps**
2. **Knox-Restrictions deaktivieren** (falls vorhanden)
3. **Family Account/Parental Controls** prüfen
4. **v2+v3 Signierung verifizieren** (wie oben beschrieben)

---

## 🌍 **Community & Sharing**

### **Q: Wie teilt man APKs sicher mit der Community?**

**A:** **GitHub Releases mit SHA256-Verifikation:**

```bash
# 1. SHA256-Hashes generieren
sha256sum *.apk > SHA256SUMS.txt

# 2. GitHub Release erstellen
gh release create v7.53.4-osCASH-test6 \
  --repo osCASHme/mollyim-android \
  --title "osCASH.me v7.53.4-test6 - Fixed APK Signing" \
  --notes-file RELEASE_NOTES.md \
  --prerelease

# 3. APKs hochladen
gh release upload v7.53.4-osCASH-test6 \
  --repo osCASHme/mollyim-android \
  osCASH-v7.53.4-test6-FOSS-Store.apk \
  osCASH-v7.53.4-test6-FOSS-Website.apk \
  osCASH-v7.53.4-test6-GMS-Website.apk \
  SHA256SUMS.txt \
  --clobber
```

**User-Verifikation:**
```bash
# APK nach Download verifizieren
sha256sum downloaded-app.apk
# Mit SHA256SUMS.txt vergleichen
sha256sum -c SHA256SUMS.txt
```

---

## 🔮 **Roadmap & Zukünftige Entwicklung**

### **Q: Was sind die nächsten Schritte für osCASH.me?**

**A:** **Entwicklungsroadmap:**

**Kurzfristig (v7.53.5):**
- MobileCoin Wallet Integration reaktivieren
- QR-Code Payment Gateway implementieren  
- Community Feedback aus aktuellen Tests einarbeiten

**Mittelfristig (v7.54.x):**
- Mesh-Network Features entwickeln
- osCASH Branding vervollständigen
- F-Droid Repository Setup
- Automated Testing Pipeline

**Langfristig (v8.0.0):**
- Production-Ready Release  
- App Store Distribution
- User Onboarding & Documentation

### **Q: Wie hält man die Docker-Umgebung aktuell?**

**A:** **Upstream-Synchronisation mit Molly:**

```bash
# 1. Molly's aktuelle Tool-Versionen prüfen
curl -s https://raw.githubusercontent.com/mollyim/mollyim-android/master/Dockerfile

# 2. Eigene Versions in Dockerfile anpassen
# 3. Gradle/Kotlin Versionen parallel updaten
# 4. Test-Build durchführen  
# 5. Bei Success: Production-Update
```

**Monitoring:** Molly's `gradle/libs.versions.toml` für Dependency-Updates beobachten.

---

## 📚 **Externe Ressourcen**

### **Q: Welche Dokumentation ist hilfreich?**

**A:** **Wichtige Links:**

- **Molly Reproducible Builds:** https://github.com/mollyim/mollyim-android/blob/master/reproducible-builds/README.md
- **Reproducible Builds Projekt:** https://reproducible-builds.org/
- **Android APK Signing:** https://developer.android.com/studio/publish/app-signing
- **F-Droid Reproducible Builds:** https://f-droid.org/docs/Reproducible_Builds/

**Community:** 
- **GrapheneOS Community:** Erfolgreiche Molly-Fork Installationen
- **recode.at Projekt:** https://recode.at/verbunden-um-frei-zu-sein/

---

## 🎯 **Erfolgs-Metriken**

### **Q: Woran erkennt man einen erfolgreichen Build?**

**A:** **Checkliste für erfolgreiche APK-Erstellung:**

✅ **Build-Prozess:**
- Docker-Build ohne kritische Fehler
- Alle 3 APK-Varianten erstellt (FOSS-Store, FOSS-Website, GMS-Website)  
- Keystore-Signierung erfolgreich
- APK-Größen im erwarteten Bereich (84-85MB)

✅ **Signatur-Verifikation:**
```bash
# Alle APKs müssen zeigen:
Verified using v2 scheme (APK Signature Scheme v2): true
Verified using v3 scheme (APK Signature Scheme v3): true
```

✅ **Installation-Test:**
- Erfolgreiche Installation auf Nokia 8.3 5G (Android 12)  
- Erfolgreiche Installation auf Samsung Galaxy A13 5G (Android 14)
- App startet und funktioniert normal

✅ **Community-Bereitstellung:**
- GitHub Release mit allen APK-Varianten
- SHA256SUMS.txt für Verifikation
- Release Notes mit Änderungen

---

## 🔄 **Kontinuierliche Aktualisierung**

> **Diese FAQ wird kontinuierlich erweitert basierend auf:**
> - Neuen Erkenntnissen aus der Entwicklung
> - Community-Feedback und Fragen  
> - Updates vom Molly-upstream Projekt
> - Neue Android-Versionen und Kompatibilitätsanforderungen

**Letzte Aktualisierung:** 27. August 2025  
**Version:** osCASH.me Molly Fork v7.53.4 Serie

---

*"Privacy is a human right. Crypto payments should be private."* - osCASH.me Team
