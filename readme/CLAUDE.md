# CLAUDE.md - Kontext-Aufwärmung für osCASH.me Molly Fork

> **🧠 SCHNELLER CONTEXT-RELOAD:** Alles was Claude Code für effektive Session-Fortsetzung wissen muss

---

## 🚀 **Quick Start für neue Sessions**

```bash
# Erste 60 Sekunden - Context Loading Protocol:

# 1. Session Summary lesen (PRIORITÄT 1)
cat /home/mayer/prog/claude/osCASH.me/mollyim-android/readme/SESSION_SUMMARY_2025-08-27.md

# 2. Critical Fixes checken (PRIORITÄT 2) 
cat /home/mayer/prog/claude/osCASH.me/mollyim-android/readme/osCASH-CRITICAL-FIXES.md

# 3. Diese Datei (CLAUDE.md) scannen (PRIORITÄT 3)
cat /home/mayer/prog/claude/osCASH.me/mollyim-android/readme/CLAUDE.md

# 4. Aktueller Release-Status (PRIORITÄT 4)
gh release view --repo osCASHme/mollyim-android
```

---

## 📍 **Projekt-Kontext Essentials**

### **Was ist osCASH.me?**
- **Vision:** "Privacy is a human right. Crypto payments should be private."
- **Ziel:** MOB/eUSD Wallet Integration in Signal/Molly Messenger
- **Community:** recode@ Developer Community, Privacy-focused

### **Peter (Q) - Projekt-Founder:**
- osCASH.me Gründer & Strategie
- Max Claude Code Abo für unlimited sessions
- Device Testing: Nokia 8.3 5G (Android 12), Samsung Galaxy A13 5G (Android 14)
- Arbeitsweise: "Siga, siga" - achtsam, nachhaltig, strategisch

### **Claude (A) - Technical Implementation:**
- Docker Reproducible Builds
- APK Signing & Compatibility
- GitHub Release Management
- Living Documentation

---

## 🏗️ **3-Tier Architektur (KERN-STRATEGIE)**

```
┌─ Tier 1: Signal → Molly (upstream) ──────────┐
│   • Community maintained                     │
│   • Signal Features + Molly Privacy Features │
│   • Regelmäßige Updates                      │
└─────────────────────────────────────────────────┘
           ↓ 1:1 Fork & Reproducible Build
┌─ Tier 2: Molly → Molly-Repro (osCASH.me) ───┐
│   ✅ ERFOLGREICH IMPLEMENTIERT!               │
│   • 1:1 Reproducible Builds                  │
│   • Perfekte APK-Kompatibilität             │
│   • Release: v7.53.5-1 live!                │
└─────────────────────────────────────────────────┘
           ↓ Feature Extension
┌─ Tier 3: Molly-Repro → osCASH.me Messenger ─┐
│   🔄 NÄCHSTE PHASE                           │
│   • MOB/eUSD Wallet Features                 │
│   • Plugin Architecture                      │
│   • Community Extensions                     │
└─────────────────────────────────────────────────┘
```

---

## 📂 **Repository-Struktur (Quick Reference)**

```bash
/home/mayer/prog/claude/osCASH.me/mollyim-android/
├── 📄 SESSION_SUMMARY_2025-08-27.md      # Vollständiger Session-Kontext
├── 📄 osCASH-CRITICAL-FIXES.md           # Kritische Build-Issues & Lösungen  
├── 📄 osCASHme-MOLLY-FAQ.md             # 454 Zeilen Community-Dokumentation
├── 📄 CLAUDE.md                          # Diese Datei - Quick Context
├── 🔧 app/build.gradle.kts               # APK Signing Konfiguration (KRITISCH!)
├── 🔧 app/gradle.properties              # Branding: Molly-Repro
├── 🔧 reproducible-builds/.env           # Docker Build Environment
├── 📱 reproducible-builds/outputs/apk/   # Generated APKs
└── 🐳 reproducible-builds/docker-compose.yml # Build System
```

---

## ⚠️ **KRITISCHE ERKENNTNISSE (Absolute Must-Know)**

### **1. APK-Signing Schema (DER Schmerzpunkt)**
**Problem:** APK Installation scheitert mit "Package appears invalid"  
**Root Cause:** Signing Schema Inkompatibilität

**LÖSUNG (auswendig lernen!):**
```kotlin
// app/build.gradle.kts - signingConfigs section
enableV1Signing = false  // Original Molly uses v2+v3 only
enableV2Signing = true   // APK signing scheme v2 (required)
enableV3Signing = true   // APK signing scheme v3 (required) 
enableV4Signing = false  // Disabled like original Molly
```

**Verifikation (IMMER prüfen!):**
```bash
# Original Molly MUSS haben:
Verified using v2 scheme (APK Signature Scheme v2): true ✅  
Verified using v3 scheme (APK Signature Scheme v3): true ✅
# Unser Build MUSS identisch sein!
```

### **2. Keystore-Passwort (Shell-Escaping Falle)**
- **❌ Funktioniert NICHT:** `osCASH@2025!` (Sonderzeichen)
- **✅ Funktioniert:** `osCASH2025` (nur alphanumerisch)

### **3. Package ID (Upgrade-Kompatibilität)**
- **NIEMALS ändern:** `im.molly.app` 
- **Grund:** Users müssen von Original Molly upgraden können

---

## 🔧 **Standard-Workflows**

### **Build-Workflow (Docker):**
```bash
cd /home/mayer/prog/claude/osCASH.me/mollyim-android/reproducible-builds
docker compose up --build
# ⏱️ Dauer: ~15-20 Minuten
```

### **APK-Verification (IMMER machen!):**
```bash
# Original herunterladen
curl -L -o original.apk "https://github.com/mollyim/mollyim-android/releases/download/v{VERSION}/Molly-v{VERSION}-FOSS.apk"

# Beide APKs vergleichen
docker run --rm --entrypoint="" -v "$PWD:/workspace" reproducible-molly sh -c \
"java -jar /opt/android-sdk-linux/build-tools/35.0.0/lib/apksigner.jar verify --print-certs --verbose /workspace/original.apk"

docker run --rm --entrypoint="" -v "$PWD:/workspace" reproducible-molly sh -c \
"java -jar /opt/android-sdk-linux/build-tools/35.0.0/lib/apksigner.jar verify --print-certs --verbose /workspace/outputs/apk/prodFossWebsite/release/Molly-Repro-untagged-FOSS.apk"
```

### **GitHub Release Workflow:**
```bash
# 1. APKs umbenennen und organisieren
mkdir release-files-v{VERSION}
cp outputs/apk/prodFossStore/release/*.apk release-files-v{VERSION}/Molly-Repro-v{VERSION}-FOSS-Store.apk
# ... weitere APK-Varianten

# 2. Checksums erstellen
cd release-files-v{VERSION} && sha256sum *.apk > SHA256SUMS.txt

# 3. Release erstellen
gh release create v{VERSION} --title "Molly-Repro v{VERSION} - 1:1 Reproducible Build" --notes-file RELEASE_NOTES.md
gh release upload v{VERSION} *.apk SHA256SUMS.txt RELEASE_NOTES.md --clobber
```

---

## 🎯 **Aktueller Status (Stand: 27. August 2025)**

### **✅ Erfolgreich implementiert:**
- **Molly-Repro v7.53.5-1** erfolgreich released
- **GitHub Release:** https://github.com/osCASHme/mollyim-android/releases/tag/v7.53.5-1
- **3 APK Varianten:** FOSS-Store, FOSS-Website, GMS-Website
- **Perfect Compatibility:** v2+v3 signing schema matching Original Molly

### **🔄 Nächste Phase (Tier 3):**
- **osCASH.me Repository Setup:** Plugin-System für MOB/eUSD Features
- **Update-Automatisierung:** Molly-Upstream Detection
- **Community Features:** F-Droid Repository

---

## 🚨 **Troubleshooting Schnellhilfe**

### **Build schlägt fehl?**
1. **ERST:** `osCASH-CRITICAL-FIXES.md` checken
2. **DANN:** Docker Clean: `docker compose down && docker system prune -f`
3. **ZULETZT:** Fresh Build: `docker compose up --build`

### **APK Installation scheitert?**
1. **SOFORT:** APK Signing Schema prüfen (siehe oben)
2. **Falls identisch:** Package ID verification  
3. **Debug:** `adb install -r -d your.apk`

### **Git/GitHub Probleme?**
1. **Auth:** `gh auth status` - Token scope "workflow" benötigt
2. **Remote:** `git remote -v` - Korrekt osCASHme/mollyim-android?
3. **Branch:** Immer neue Branches für releases

---

## 📚 **Dokumentations-Hierarchie**

```
Schnelle Problembehebung:
├── 1. osCASH-CRITICAL-FIXES.md     ← Build-Probleme & Lösungen
├── 2. CLAUDE.md (diese Datei)      ← Kontext & Workflows
├── 3. SESSION_SUMMARY_2025-08-27   ← Vollständige Session-Historie  
└── 4. osCASHme-MOLLY-FAQ.md       ← Detaillierte Community-Doku
```

---

## 🤖 **Session-Collaboration Pattern**

### **Peter's Arbeitsweise:**
- **Strategisch:** Langfristige Vision, Community Impact
- **Geduldig:** "Siga, siga" - keine Hetze, durchdachte Lösungen
- **Testing-focused:** Real-device validation wichtig

### **Claude's Aufgaben:**
- **Technical Implementation:** Docker, Builds, GitHub
- **Living Documentation:** Kontinuierliche Dokumentation
- **Problem-Solving:** Reproduzierbare Lösungen entwickeln
- **Community-Benefit:** Transparent für andere Entwickler

### **Zusammenarbeit Erfolgspattern:**
1. **Systematische Analyse:** Root Cause finden, nicht Symptome
2. **Docker-First:** Reproducible builds in containerized environment  
3. **Transparent Development:** Alle Schritte dokumentiert
4. **Real-world Testing:** Nokia/Samsung device validation

---

**🤖 Generated with [Claude Code](https://claude.ai/code)**  
**Co-Authored-By: Claude & Peter (osCASH.me)**

*Last Updated: 27. August 2025 - Post Molly-Repro v7.53.5-1 Success*

---

## 💡 **Pro-Tips für effektive Sessions**

1. **Immer zuerst diese 4 Dateien lesen** bevor du anfängst zu coden
2. **Bei kritischen Fixes:** Sofort in `osCASH-CRITICAL-FIXES.md` dokumentieren
3. **Jede Session:** `SESSION_SUMMARY` erweitern mit neuen Erkenntnissen  
4. **Build-Probleme?** Erst Dokumentation, dann Code
5. **APK-Kompatibilität ist HEILIG** - niemals ohne Verifikation releasen