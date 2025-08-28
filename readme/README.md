# osCASH.me Molly Fork - Wissensmanagement-System 📚

> **🧠 Zentrale Dokumentation für nachhaltige Entwicklung und Community-Wissen**

---

## 📂 **Dokumentations-Übersicht**

### **🚨 Erste Hilfe & Problembehebung**
- **[osCASH-CRITICAL-FIXES.md](./osCASH-CRITICAL-FIXES.md)** - Alle kritischen Build-Issues & bewährte Lösungen
- **[CLAUDE.md](./CLAUDE.md)** - Schnelle Kontext-Aufwärmung für Claude Code Sessions

### **📊 Session-Historie & Kontext**  
- **[SESSION_SUMMARY_2025-08-27.md](./SESSION_SUMMARY_2025-08-27.md)** - Vollständiger Kontext: APK-Signing Fixes & Molly-Repro v7.53.5-1 Release
- **[SESSION_SUMMARY_2025-08-26.md](./SESSION_SUMMARY_2025-08-26.md)** - Erste Session: Docker Setup & APK-Kompatibilitätsprobleme

### **📚 Community-Dokumentation**
- **[osCASHme-MOLLY-FAQ.md](./osCASHme-MOLLY-FAQ.md)** - Umfassende FAQ mit 500+ Zeilen Community-Wissen

---

## 🚀 **Schnellstart für neue Claude Code Sessions**

### **60-Sekunden Context-Loading Protocol:**
```bash
# 1. Session Summary lesen (PRIORITÄT 1)
cat /home/mayer/prog/claude/osCASH.me/mollyim-android/readme/SESSION_SUMMARY_2025-08-27.md

# 2. Critical Fixes checken (PRIORITÄT 2) 
cat /home/mayer/prog/claude/osCASH.me/mollyim-android/readme/osCASH-CRITICAL-FIXES.md

# 3. Claude Context laden (PRIORITÄT 3)
cat /home/mayer/prog/claude/osCASH.me/mollyim-android/readme/CLAUDE.md

# 4. Aktueller Release-Status (PRIORITÄT 4)
gh release view --repo osCASHme/mollyim-android
```

---

## 🎯 **Strategische osCASH.me 3-Tier Architektur**

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

### **Aktueller Status (27. August 2025):**
- **✅ Tier 2 erfolgreich:** Molly-Repro v7.53.5-1 mit perfekter APK-Kompatibilität
- **🔄 Tier 3 nächste Phase:** osCASH.me Messenger mit MOB/eUSD Wallet Integration

---

## 🛠️ **Für Entwickler: Wichtigste Erkenntnisse**

### **APK-Signing Schema (KRITISCH!):**
```kotlin
// app/build.gradle.kts - signingConfigs section
enableV1Signing = false  // Original Molly uses v2+v3 only
enableV2Signing = true   // APK signing scheme v2 (required)
enableV3Signing = true   // APK signing scheme v3 (required) 
enableV4Signing = false  // Disabled like original Molly
```

### **Keystore-Passwort:**
- **❌ Funktioniert NICHT:** `osCASH@2025!` (Sonderzeichen → Shell-Escaping)
- **✅ Funktioniert:** `osCASH2025` (nur alphanumerisch)

### **Package ID Kompatibilität:**
- **NIEMALS ändern:** `im.molly.app` (upgrade-fähig mit Original Molly)

---

## 📱 **Aktuelle Releases**

### **Molly-Repro v7.53.5-1** 
- **GitHub:** https://github.com/osCASHme/mollyim-android/releases/tag/v7.53.5-1
- **APK-Varianten:** FOSS-Store, FOSS-Website, GMS-Website
- **Signing:** 4096-bit RSA Certificate (CN=osCASH.me)
- **Kompatibilität:** 100% identisch mit Original Molly v7.53.5-1

---

## 🤝 **Community & Zusammenarbeit**

### **Philosophie:**
> "Privacy is a human right. Crypto payments should be private."

### **Entwicklungs-Team:**
- **Peter (Q):** osCASH.me Founder, strategische Planung, Device-Testing
- **Claude Code (A):** Technical Implementation, Build-System, Documentation
- **Community:** recode@ Projekt, GrapheneOS Community

### **Arbeitsweise:**
- **"Siga, siga"** - achtsam, nachhaltig, strategisch planen
- **Collaborative Learning** - Community-benefit focused
- **Transparent Development** - alle Schritte dokumentiert

---

## 📈 **Roadmap & Nächste Schritte**

### **Kurzfristig (Tier 2 Optimierung):**
1. **Update-Automatisierung:** Molly-Upstream Detection & Integration
2. **F-Droid Repository:** Community Distribution Setup
3. **Documentation Website:** Zentrale Anlaufstelle

### **Mittelfristig (Tier 3 Implementation):**
1. **osCASH.me Repository:** `osCASHme/android` für erweiterte Features
2. **MOB/eUSD Wallet:** Integration in Molly-Core
3. **Plugin-System:** Community-Feature Extensions

### **Langfristig (Community & Scaling):**
1. **MobileCoin Gateway:** QR-Code Payment System
2. **Privacy-First Features:** Enhanced Security & Anonymity
3. **Global Community:** International recode@ Network

---

## 🔄 **Kontinuierliche Verbesserung**

### **Dokumentations-Update Protokoll:**
1. **Nach jedem kritischen Fix:** `osCASH-CRITICAL-FIXES.md` erweitern
2. **Nach jeder Session:** Entsprechendes `SESSION_SUMMARY` aktualisieren
3. **Bei neuen Features:** `osCASHme-MOLLY-FAQ.md` mit Details ergänzen
4. **Bei Architektur-Änderungen:** `CLAUDE.md` Context anpassen

### **Versionierung:**
- Session Summaries: `SESSION_SUMMARY_YYYY-MM-DD.md`
- Release-bezogene Updates in entsprechenden Dateien
- Archivierung älterer Versionen bei Bedarf

---

**🤖 Generated with [Claude Code](https://claude.ai/code)**  
**Co-Authored-By: Claude & Peter (osCASH.me)**

*Wissensmanagement-System etabliert: 27. August 2025*  
*Living Documentation für nachhaltige Community-Entwicklung*