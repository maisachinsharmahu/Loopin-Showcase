# 🌀 Loopin: The Privacy-Centric Productivity Ecosystem

[![Flutter Suite](https://img.shields.io/badge/Stack-Flutter%20%7C%20Hive%20%7C%20Provider-02569B?style=for-the-badge&logo=flutter)](https://flutter.dev)
[![Privacy First](https://img.shields.io/badge/Architecture-Zero--Knowledge-4CAF50?style=for-the-badge&logo=shield)](https://loopin.app)
[![Build Status](https://img.shields.io/badge/Build-v1.0.0--Stable-orange?style=for-the-badge)](https://github.com/maisachinsharmahu/Loopin-Showcase/releases)

**Loopin** is an engineering-first productivity application that merges **Behavioral Psychology (RPG Mechanics)** with a **High-Performance Local-First Architecture**. It is designed for high-agency individuals who demand absolute data ownership without sacrificing the dopamine-driven engagement of modern gamified apps.

---

## 📈 The Investor Case: Scaling Privacy

In an era of increasing data regulation and user privacy awareness, Loopin is built on a **Zero-Server Business Model**:
- **Zero Infrastructure Overhead:** No centralized databases or backend servers. Operations scale horizontally at zero cost per user.
- **Data Sovereignty:** Users own their data on their private Google Drive. This eliminates liabilities related to data breaches (GDPR/CCPA compliant by design).
- **Engagement Loop:** Integrated RPG Engine maintains high retention rates through a 24-hour "Consistency Challenge" loop.

[**Download Production APK**](https://github.com/maisachinsharmahu/Loopin-Showcase/releases/tag/v1.0.0) | [**Deep Technical Architecture**](#-deep-dive-loopin-system-architecture)

---

## 🏗 Engineering Architecture

Loopin is architected using **Reactive MVVM (Model-View-ViewModel)** principles, ensuring a strict separation between UI presentation and complex behavioral logic.

### High-Level Design Pattern
```mermaid
graph TD
    subgraph View_Layer
        HS[Home Screen - Centered Focal Point]
        RPG[RPG Profile - Stats Display]
        WB[Wellbeing - Canvas Graphics]
    end

    subgraph State_Engine
        HP[Habit Provider]
        RP[RPG Provider - Event Queue]
        WBP[Wellbeing Provider]
    end

    subgraph Core_Mechanics
        Sync[Google Drive Sync Engine]
        Loot[Procedural Loot Generator]
    end

    View_Layer -->|Listen| State_Engine
    State_Engine -->|Orchestrate| Core_Mechanics
    Core_Mechanics -->|Persist| Hive[(Local-First Hive NoSQL)]
```

---

## 🔬 Engineering Case Studies (Technical "Wins")

### 1. The Centered Timeline Focal Point
**Problem:** Traditional scrollable lists lose the "Current Day" context when the user navigates past dates, leading to cognitive friction.
**Solution:** A custom `ScrollController` with a dynamic viewport calculation.
- **The Math:** `Offset = (TargetIndex * CardWidth) + Padding - (ScreenWidth / 2) + (CardWidth / 2)`
- Real-time DPI-aware scaling ensures "Today" is always the hero across any device factor.

### 2. Atomic Cloud Synchronization
**Problem:** Network failures during large data syncs can corrupt the state, leading to "Partial Restore" bugs.
**Solution:** An atomic **Write-Ahead Sync Strategy**.
- Data is serialized into a proprietary binary-streamed JSON format.
- Syncing triggers an atomic overwrite on a hidden `appDataFolder` scope in Google Drive.
- Verification happens pre-restore to ensure total data integrity before local ingestion.

### 3. iOS Custom Document Type Handling
**Problem:** iOS restricts file selection for unknown extensions, making `.loopin` backup files "invisible" to users.
**Solution:** Explicit UTI (Uniform Type Identifier) registration.
- Implemented `UTExportedTypeDeclarations` and `CFBundleDocumentTypes` in `Info.plist`.
- This ensures the iOS Files app recognizes `.loopin` files as professional editor documents, enabling seamless cross-platform backup sharing.

---

## 🛠 Technical Specification

- **Framework:** Flutter 3.x (Dart)
- **Local Database:** [Hive](https://pub.dev/packages/hive) — NoSQL for sub-millisecond local-first lookups.
- **State Management:** [Provider](https://pub.dev/packages/provider) — Simplified dependency injection and reactive state.
- **Sync Protocol:** OAuth 2.0 + Google Drive API v3.
- **Graphics:** Custom Flutter `Canvas` for the dynamic Mood Facial Engine.

---

## 📸 Experience Gallery

| 📅 The Centered Strip | 🛡 Character Growth | 📊 Performance Stats |
| :---: | :---: | :---: |
| ![Today](assets/home_preview.png) | ![Profile](assets/rpg_preview.png) | ![Stats](assets/stats_preview.png) |

---

## 🛤 Professional Roadmap

### Phase 1: Foundation (Current)
- [x] Local-first Hive architecture.
- [x] Initial RPG XP/Level Logic.
- [x] Google Drive Sync Bridge (Android/iOS).

### Phase 2: Social & Scaling (Q3 2026)
- [ ] **Rivals Peer-to-Peer:** Real-time habit tracking with encrypted P2P data exchange.
- [ ] **AI-Coaching:** On-device habit difficulty adjustment based on mood-habit correlation data.

---

## 🏗 Deep-Dive: Loopin System Architecture

This specialized section explores the low-level engineering design of the Loopin ecosystem, intended for **Technical Architects** and **Senior Engineering Leads**.

### 1. Interaction Design: The Event-Driven RPG Engine

Instead of direct state mutation, the RPG engine uses an **Event-Queue Pattern**. This ensures that rewards (XP, Coins, Achievements) are processed in a cinematic sequence and never overlap, preventing a "notification storm."

#### Event Lifecycle
1. **Trigger:** `HabitProvider` detects a completion.
2. **Push:** Sends a `LootResult` to the `RpgProvider` event queue.
3. **Queue Logic:**
    - Checks for **Achievements** (e.g., First habit, 7-day streak).
    - Checks for **Rank Up** (If new XP > Level Threshold).
    - Checks for **Inventory Drops**.
4. **Dispatch:** The `MainNavigationScreen` listens to the queue and pops overlays one by one using a `ValueKey` based `AnimatedSwitcher`.

### 2. Data Persistence Layer (Local-First)

Loopin uses **Hive**, a high-performance Key-Value NoSQL database written in pure Dart. This was chosen over SQLite to eliminate the overhead of SQL-to-Object mapping and to provide synchronous reads for UI performance.

#### Schema Definitions (TypeAdapters)

| Box Name | Data Type | Key Pattern | Purpose |
| :--- | :--- | :--- | :--- |
| `habit_box` | `HabitModel` | UUID | Core habit settings and metadata. |
| `checkin_box` | `CheckInEntry` | `${habitId}_${date}` | High-density completion tracking. |
| `rpg_profile` | `RpgProfile` | `main_profile` | Player level, XP, inventory, coins. |
| `wellbeing` | `WellBeingEntry` | `${date}` | Mood scoring and daily labels. |

### 3. Synchronization & Cloud Brokerage

The sync logic is decoupled into a **GoogleDriveSyncService** to handle OAuth and API orchestration.

#### The Atomic Sync Sequence
```mermaid
sequenceDiagram
    participant App as Loopin App
    participant Broker as Sync Broker
    participant GDrive as Google Drive API v3
    
    App->>Broker: triggerSync()
    Broker->>App: Fetch All Hive Boxes
    App-->>Broker: [Habits, Checkins, RPG, Mood]
    
    Broker->>Broker: Package to JSON (Atomic Stream)
    
    Broker->>GDrive: Query for appDataFolder scope
    GDrive-->>Broker: File Metadata
    
    alt File Exists
        Broker->>GDrive: Media Update (Atomic Overwrite)
    else File Missing
        Broker->>GDrive: Create File (v1.0.0 header)
    end
    
    GDrive-->>Broker: 200 OK
    Broker->>App: Update lastSyncTimestamp
```

### 4. UI Rendering: Computational Visuals

#### Custom Canvas Mood Engine
The mood faces are not SVGs or PNGs; they are **programmatically drawn** using the `CustomPainter` API.
- **Why?** Programmatic drawing allows for seamless interpolation between mood states (e.g., Bored to Happy) and dynamic coloring (e.g., Black-on-Green vs White-on-Dark logic).
- **Efficiency:** Drastically reduces app bundle size and GPU memory usage compared to raster assets.

#### Center-Point Navigation Math
The calendar navigation relies on `_todayScrollOffset` logic to maintain user focus:
- `logical_slot = (card_width + gap) * screen_util_ratio`
- `today_position = (past_days_count * logical_slot) + left_padding`
- `centralized_offset = today_position - (screen_width / 2) + (logical_slot / 2)`

This ensures the user's focus is always on the immediate present ("Today"), improving completion conversion rates.

### 5. Security Architecture

- **OAuth 2.0 Scopes:** Limited strictly to `drive.appdata` (access to app-specific hidden folder) and `userinfo.email`. 
- **Encryption:** (Roadmap) TEE-based (Trusted Execution Environment) encryption for the backup file before cloud upload.

--- 
*Note: This repository is a technical portfolio showcasing architectural decisions and engineering outcomes. The source code is proprietary.*
