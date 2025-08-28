# osCASH.me Molly Fork - Session Summary 2025-08-27

> **KRITISCHER ERFOLG:** APK-Installationsproblem vollständig gelöst! 🎉  
> **Device Testing:** Nokia 8.3 5G (Android 12) ✅ | Samsung Galaxy A13 5G (Android 14) ✅  
> **Status:** Bereit für strategische Langzeit-Planung und kontinuierliche Signal/Molly Updates

---

## 📊 **Session Highlights & Achievements**

### 🎯 **Hauptproblem gelöst: "Package appears invalid"**
- **Root Cause:** APK-Signatur Schema Inkompatibilität (v1+v2+v3 statt v2+v3)
- **Lösung:** Build-Konfiguration auf exakt Original Molly Schema angepasst
- **Keystore Issue:** Sonderzeichen-Passwort verursachte Shell-Escaping Problems
- **Final Fix:** Keystore mit alphanumerischem Passwort neu erstellt

### ✅ **Erfolgreich abgeschlossene Tasks:**
1. **Signing-Konfiguration korrigiert** (nur v2+v3, kein v1)
2. **Git-Funktionen für Docker-Build fehlerresistent** gemacht  
3. **Neuer Build mit korrigierter Signierung** erfolgreich
4. **Keystore-Passwort Problem gelöst** (osCASH2025 ohne Sonderzeichen)
5. **Lebende FAQ.md mit gesammeltem Wissen** erstellt (454 Zeilen)
6. **APK-Signatur-Schemas verifiziert** (v2+v3 Perfect Match)
7. **APKs für GitHub Release umbenannt** und organisiert
8. **GitHub Release v7.53.4-test6** erstellt und APKs hochgeladen
9. **FAQ.md mit erfolgreichen Build-Erkenntnissen** erweitert
10. **APK-Kompatibilität auf Nokia/Samsung** ERFOLGREICH GETESTET ✅

---

## 🔧 **Technische Details & Lösungen**

### **APK-Signatur Schema (KRITISCH)**
```bash
# KORREKTE Konfiguration (wie Original Molly):
Verified using v1 scheme (JAR signing): false     ✅
Verified using v2 scheme (APK Signature Scheme v2): true   ✅  
Verified using v3 scheme (APK Signature Scheme v3): true   ✅
Verified using v4 scheme (APK Signature Scheme v4): false  ✅
```

### **Build-Konfiguration in app/build.gradle.kts**
```kotlin
signingConfigs {
  create("ci") {
    // Multi-scheme signing matching original Molly
    enableV1Signing = false  // Original Molly uses v2+v3 only
    enableV2Signing = true   // APK signing scheme v2 (required)
    enableV3Signing = true   // APK signing scheme v3 (required) 
    enableV4Signing = false  // Disabled like original Molly
  }
}
```

### **Git Error Handling für Docker (WICHTIG)**
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

### **Environment Configuration (.env)**
```env
CI_BUILD_VARIANTS=prod(Gms|Foss)
CI_KEYSTORE_PATH=/molly/app/certs/osCASH-release.keystore
CI_KEYSTORE_PASSWORD=osCASH2025  # KEINE SONDERZEICHEN!
CI_KEYSTORE_ALIAS=oscash-key
```

### **Dockerfile Build-Tools Fix**
```dockerfile
ARG BUILD_TOOLS_VERSION=34.0.0  # NOT 35.0.0 - F-Droid compatibility!
```

---

## 📱 **Erfolgreiche APK-Varianten (v7.53.4-test6)**

### **GitHub Release:** https://github.com/osCASHme/mollyim-android/releases/tag/v7.53.4-osCASH-test6

**APK-Dateien:**
- `osCASH-v7.53.4-test6-FOSS-Store.apk` (84MB) - F-Droid kompatibel
- `osCASH-v7.53.4-test6-FOSS-Website.apk` (84MB) - Direct Download  
- `osCASH-v7.53.4-test6-GMS-Website.apk` (85MB) - Google Services

**SHA256 Checksums:**
- **FOSS-Store:** `e9944e339376048cbf9d3475dd62d6f4fdb23c8cdc5c66b8834a9766763fc355`
- **FOSS-Website:** `94da72c03bf69bcc29d5311799ed767d9eb858c4d236aae0d6b0f7a9fd985ccf`  
- **GMS-Website:** `459723f5bd583d88073e2c814fab1f13dfe8f82d40f2ad6873b50b2610daeebb`

**4096-bit RSA Keystore Details:**
- CN=osCASH.me, OU=Development, O=osCASH, L=Vienna, ST=Austria, C=AT
- SHA-256: `69aa2ec208828dff261c79368bfd589feb28f4703c26fc0067e3bf501ca7e94e`
- Gültigkeit: 10,000 Tage (27+ Jahre)

---

## 📚 **Dokumentation & Wissensmanagement**

### **Lebende FAQ erstellt:**
- **Datei:** `/home/mayer/prog/claude/osCASH.me/mollyim-android/osCASHme-MOLLY-FAQ.md`
- **Umfang:** 454 Zeilen umfassende Dokumentation
- **Inhalt:** Alle Erkenntnisse aus gemeinsamer Entwicklung mit Claude Code
- **Zweck:** Community-Sharing und kontinuierliche Erweiterung

**Abschnitte der FAQ:**
1. Projekt-Kontext & Entwicklungsumgebung
2. APK-Signierung & Installation (Android 12+ Kompatibilität)
3. Docker & Reproducible Builds
4. APK-Analyse & Debugging Workflows
5. Molly Build-System Besonderheiten
6. Environment & Konfiguration
7. Performance & Optimierung
8. Häufige Probleme & Lösungen (inkl. Keystore Password Issues)
9. Community & Sharing (GitHub Releases)
10. Roadmap & Zukünftige Entwicklung
11. Externe Ressourcen & Links
12. Erfolgs-Metriken & Checklisten

---

## 🎯 **Aktuelle strategische Herausforderung**

### **Signal → Molly → osCASH.me Update-Chain Problem**
```
Signal (upstream) → Molly (mollyim/mollyim-android) → osCASH.me (osCASHme/android)
```

**Peter's strategische Priorität:**
> "Es ist sehr wichtig, dass wir mit dem Molly Fork zeitnah alle Updates vom Signal Messenger in unseren osCASH.me Messenger auf https://github.com/osCASHme/android integrieren."

**Kritische Fragen für nächste Session:**

1. **Repository-Struktur optimieren?**
   - Aktuell: `osCASHme/mollyim-android` (Test-Repository)
   - Ziel: `osCASHme/android` (Production-Repository)
   - Migration-Strategie erforderlich?

2. **Update-Häufigkeit & Workflow definieren?**
   - Signal releases: ~2-4x pro Monat
   - Molly folgt meist binnen 1-2 Wochen  
   - osCASH.me Ziel-Rhythmus definieren?

3. **Feature-Branching Strategy?**
   - MobileCoin Features in separaten Branches?
   - Hauptbranch immer Molly-kompatibel halten?
   - Merge-Conflicts bei Updates minimieren?

4. **Automatisierung & CI/CD?**
   - Automated Molly-Update Detection?
   - Reproducible Builds Pipeline?
   - Testing-Automation für Device-Kompatibilität?

---

## 💡 **Nächste Entwicklungsphasen (Roadmap)**

### **Phase 1: Update-Strategy & Repository Setup** (NÄCHSTE SESSION)
- Repository-Struktur für langfristige Molly-Updates optimieren
- Automated Update-Detection von Molly-Upstream implementieren
- CI/CD Pipeline für reproducible builds etablieren

### **Phase 2: MobileCoin Integration (v7.53.5)**
- Signal Wallet Features reaktivieren
- MOB/eUSD Payment Gateway implementieren
- QR-Code Payment System entwickeln

### **Phase 3: osCASH Branding & UI (v7.54.x)**
- Custom UI/UX für osCASH.me Identity
- Community-Feedback Integration
- F-Droid Repository Setup

### **Phase 4: Production Release (v8.0.0)**
- Stability & Performance Optimizations
- Comprehensive Testing Suite
- Community Distribution & Marketing

---

## 🔄 **Session-Kontext für Fortsetzung**

### **Aktuelle Arbeitsumgebung:**
- **Workstation:** Debian 12 LXC Container (Proxmox)
- **Docker:** 28.3.3 mit Compose v2.39.1
- **Working Directory:** `/home/mayer/prog/claude/osCASH.me/mollyim-android/reproducible-builds`
- **GitHub Repository:** `osCASHme/mollyim-android` (authenticated via gh CLI)
- **Claude Code Settings:** Max Abo aktiviert für unlimited sessions

### **Entwickler-Zusammenarbeit:**
- **Peter (Q):** osCASH.me Founder, strategische Planung, Device-Testing
- **Claude Code (A):** Technical Implementation, Build-System, Dokumentation
- **Arbeitsweise:** Collaborative Learning, Community-benefit focused
- **Philosophie:** "Privacy is a human right. Crypto payments should be private."

### **Erfolgreiche Patterns aus dieser Session:**
1. **Systematische Problem-Analyse:** Root Cause durch APK-Vergleich gefunden
2. **Docker-First Approach:** Reproducible builds in containerized environment
3. **Living Documentation:** FAQ kontinuierlich während Entwicklung erweitert
4. **Community Testing:** Real-device validation auf Nokia/Samsung
5. **Transparent Development:** Alle Schritte dokumentiert für Nachvollziehbarkeit

### **Tools & Commands die sich bewährt haben:**
```bash
# APK Signature Verification
docker run --rm --entrypoint="" -v "$PWD:/workspace" reproducible-molly sh -c \
"java -jar /opt/android-sdk-linux/build-tools/34.0.0/lib/apksigner.jar verify --print-certs --verbose /workspace/file.apk"

# Docker Build
docker compose up --build

# GitHub Release Management  
gh release create v7.53.4-osCASH-test6 --title "..." --notes-file RELEASE_NOTES.md --prerelease
gh release upload v7.53.4-osCASH-test6 *.apk SHA256SUMS.txt --clobber
```

---

---

## 🎯 **UPDATE: Strategische 3-Tier Architektur ERFOLGREICH implementiert!**

### **Session Fortsetzung: 27. August 2025, 22:00 CET**

**✅ MEILENSTEIN ERREICHT:** Molly-Repro v7.53.5-1 erfolgreich released!

#### **3-Tier osCASH.me Architektur - Status Update:**

```
1. Signal → Molly (upstream)   ← Community maintains this ✅
2. Molly → Molly-Repro (1:1)   ← We provide reproducible builds ✅ DONE!
3. Molly-Repro → osCASH.me     ← Enhanced features & MOB wallet 🔄 NEXT
```

#### **Erfolgreich abgeschlossene Tasks (Session 2):**
1. **✅ Molly v7.53.5-1 Analysis & Merge** - 889 geänderte Dateien erfolgreich integriert
2. **✅ Branding auf 'Molly-Repro' angepasst** - Klare 1:1 Reproducible Build Kommunikation
3. **✅ APK Signing Schema GEFIXT** - v2+v3 Schema perfekt matching mit Original Molly
4. **✅ Docker Build erfolgreich** - 3 APK-Varianten generated
5. **✅ APK-Signatur Verifikation** - 100% identisch mit Original Molly
6. **✅ GitHub Release v7.53.5-1 erstellt** - https://github.com/osCASHme/mollyim-android/releases/tag/v7.53.5-1

#### **Das kritische Learning:**
**Problem wiederholt:** APK-Signing Schema vergessen (gleicher Fix wie in Session 1!)  
**Lösung:** Umfassende Dokumentation erstellt für permanente Wissenserhaltung

#### **Neue Dokumentation erstellt:**
- **osCASH-CRITICAL-FIXES.md** - Alle kritischen Build-Issues & Lösungen
- **CLAUDE.md** - Kontext-Aufwärm-Anleitung für neue Sessions  
- **FAQ erweitert** - v7.53.5-1 Erkenntnisse dokumentiert

#### **GitHub Release Details:**
- **3 APK Varianten:** FOSS-Store (83MB), FOSS-Website (83MB), GMS-Website (84MB)
- **Signing Certificate:** 4096-bit RSA (CN=osCASH.me)
- **Package ID:** `im.molly.app` (upgrade-kompatibel)
- **Perfect Compatibility:** v2+v3 signing schema matching Original Molly

---

## 🧠 **Wissensmanagement für nachhaltige Entwicklung**

### **Problembehebung für Gedächtnisverlust:**
1. **osCASH-CRITICAL-FIXES.md** ← Erste Anlaufstelle bei Build-Problemen
2. **SESSION_SUMMARY_2025-08-27.md** ← Vollständiger Session-Kontext  
3. **osCASHme-MOLLY-FAQ.md** ← Detaillierte Community-Dokumentation
4. **CLAUDE.md** ← Schnelle Kontext-Aufwärmung für neue Sessions

### **Session-Start Protokoll:**
```bash
# 1. Session Summary lesen
cat /osCASH.me/mollyim-android/SESSION_SUMMARY_2025-08-27.md

# 2. Critical Fixes checken  
cat /osCASH.me/mollyim-android/osCASH-CRITICAL-FIXES.md

# 3. Claude Context laden
cat /osCASH.me/mollyim-android/CLAUDE.md

# 4. Aktuellen Stand prüfen
gh release view --repo osCASHme/mollyim-android
```

---

## 🚀 **Ready for Next Phase**

**Status:** Molly-Repro (Tier 2) erfolgreich implementiert ✅  
**Next Focus:** osCASH.me Messenger (Tier 3) - MOB/eUSD Wallet Integration  
**Peter's Max Abo:** Unlimited sessions für kontinuierliche Entwicklung  
**Mindset:** "Siga, siga" - strategische Langzeit-Planung mit solider Basis

### **Nächste strategische Ziele:**
1. **Update-Automatisierung:** Molly-Upstream Detection & Integration
2. **osCASH.me Repository:** Plugin-System für MOB/eUSD Features
3. **Community-Features:** F-Droid Repository, Documentation Website

**Bei Session-Restart: Alle 4 Dokumentationsdateien lesen für vollständigen Kontext.**

---

*Session Summary erstellt: 27. August 2025, 18:35 CET*  
*Claude Code Session mit Peter (osCASH.me) - Vollständiger Erfolg bei APK-Signing Fix*

*Session Update: 27. August 2025, 23:45 CET*  
*🎉 STRATEGIC MILESTONE: 3-Tier Architecture Tier 2 erfolgreich implementiert*  
*Molly-Repro v7.53.5-1 Production Release mit perfekter APK-Kompatibilität*