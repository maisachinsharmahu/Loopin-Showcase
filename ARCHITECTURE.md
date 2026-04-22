# Internal Architecture & System Design

This document provides a deep dive into the engineering principles and technical decisions behind **Loopin**.

---

## 1. High-Level System Architecture

Loopin follows a clean separation of concerns using a **Service-Provider-Repository** pattern.

```mermaid
graph LR
    subgraph UI_Layer
        SC[Screens]
        WD[Widgets]
    end

    subgraph State_Management
        PV[Providers]
    end

    subgraph Business_Logic
        RPG[RPG Engine]
        HAB[Habit Engine]
        SYNC[Sync Hub]
    end

    subgraph Data_Layer
        REP[Repository]
        HIVE[(Hive NoSQL)]
    end

    SC --> PV
    WD --> PV
    PV --> RPG & HAB & SYNC
    RPG & HAB --> REP
    SYNC --> REP
    REP --> HIVE
```

---

## 2. Data Schema & Persistence

### Local Data (Hive)
Hive is used over SQLite for its superior speed in Flutter. All data is categorized into **TypeAdapters**.

#### Habit Model
```json
{
  "id": "uuid",
  "title": "String",
  "targetCount": "int",
  "streak": "int",
  "difficulty": "enum[easy, medium, hard]",
  "createdAt": "DateTime"
}
```

#### RPG Profile
```json
{
  "displayName": "String",
  "xp": "int",
  "level": "int",
  "coins": "int",
  "inventory": "List<Item>",
  "unlockedBadges": "List<String>"
}
```

---

## 3. Synchronization Flow (Cloud Sync)

Syncing handles the transfer of local state to the user's private Google Drive storage.

```mermaid
sequenceDiagram
    participant App
    participant GSignIn as Google Sign-In SDK
    participant GDrive as Google Drive API v3
    
    App->>GSignIn: Authenticate (Silent/Explicit)
    GSignIn-->>App: OAuth Token (Access & Refresh)
    
    App->>App: Serialize Repository (JSON)
    App->>App: Compress & Encrypt (optional)
    
    App->>GDrive: Check for existing loopin_backup.loopin
    alt File Exists
        App->>GDrive: Update content (Media Multipart Upload)
    else File Doesn't Exist
        App->>GDrive: Create new file
    end
    
    GDrive-->>App: Success/Failure Response
```

---

## 4. UI/UX Principles: The Centered Timeline

The habit strip is a proprietary UI component. To ensure "Today" is always the hero, the following viewport logic is applied:

1. **Window of Focus:** 30 days of history + 2 days of future intention are calculated.
2. **Dynamic Offset:** The `ScrollController` calculates the exact pixel offset required to center the "Today" card within the current device width.
3. **Responsive Scaling:** Using `flutter_screenutil`, the cards scale proportionally across tablet and mobile layouts without losing the "Focal Point" center.

---

## 5. Security & Privacy

- **Zero PII Storage:** No Personally Identifiable Information (PII) is ever sent to an external server.
- **Local-Only:** The app functions 100% offline.
- **User-Owned Cloud:** Data backup uses the user's personal drive scope (`drive.appdata`), ensuring Loopin cannot access the user's personal files outside the app's own folder.
