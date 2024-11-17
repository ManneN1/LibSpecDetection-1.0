# LibSpecDetection-1.0

**Spec Detection Library for WoW 3.3.5, 4.3.4, 5.4.8**

**Status:** Alpha  
**Note:** Untested on 4.3.4, 5.4.8, and RCE-TBC.

---

## **Overview**

LibSpecDetection-1.0 is a library designed to track and identify the specializations of units in World of Warcraft.

### **Supported Versions**
- WotLK (3.3.5, including RCE-TBC)
- Cataclysm (4.3.4)
- Mists of Pandaria (5.4.8)

---

## **Features**

- Detects and tracks the specializations of units.
- Supports callbacks for events like spec detection and loss.
- Provides functions for querying detected specializations.
- Automatically manages timers to reset outdated specs (60-minute duration).
- Integrates with `LibGroupTalents`/`LibGroupInSpecT` for talent-based spec detection (depending on expansion).

---

## **API Reference**

### **Callbacks**

| Callback          | Description                                                                                       |
|-------------------|---------------------------------------------------------------------------------------------------|
| `SPEC_DETECTED`   | Fired when a spec is detected for a unit. Includes `unitID`, `guid`, `specID`, and `isEnemy`.    |
| `SPEC_LOST`       | Fired when a detected spec expires or is reset.                                                   |
| `SPEC_RESET`      | Fired when all detected specs are cleared (e.g., on world entry or loading screen).               |

### **Functions**

| Function                  | Description                                                                                 |
|---------------------------|---------------------------------------------------------------------------------------------|
| `specID = lib:GetSpecID(guid)` | Retrieves the specialization ID for a given GUID. Returns `nil` if the spec is not detected. |
| `isEnemy = lib:IsEnemy(guid)` | Checks if the detected spec belongs to an enemy unit. Returns a boolean.                  |

---

## **Events**

### SPEC_DETECTED
Triggered when a spec is detected for a unit.  

#### Arguments:
- `event`: The event name (`"SPEC_DETECTED"`).
- `unitID`: The unit ID of the detected unit (can be `nil` if no unit ID is available).
- `guid`: The GUID of the detected unit.
- `specID`: The specialization ID of the detected unit.
- `isEnemy`: Boolean indicating whether the detected unit is an enemy.

### SPEC_LOST
Triggered when the spec of a unit is no longer valid or has been forgotten. This is automatically triggered after 60 minutes of a guid's spec being detected.

#### Arguments:
- `event`: The event name (`"SPEC_LOST"`).
- `guid`: The GUID of the unit whose spec is lost.

### SPEC_RESET
Triggered when all detected specs are cleared (e.g., during a loading screen or upon entering the world).

#### Arguments:
- `event`: The event name (`"SPEC_RESET"`).

---



## **Example Usage**

### Fetching the Library and Registering a Callback

```lua
-- Fetch the library
local LSD = LibStub("LibSpecDetection-1.0")
if not LSD then
    print("LibSpecDetection library not found!")
    return
end

-- Define the callback function for SPEC_DETECTED
local function OnSpecDetected(event, unitID, guid, specID, isEnemy)
    print("Spec detected: Event =", event, "Unit ID =", unitID or "N/A", "GUID =", guid, "Spec ID =", specID, "Is Enemy =", isEnemy)
    
    -- Get the spec ID for the GUID
    local currentSpecID = LSD:GetSpecID(guid)
    if currentSpecID then
        print("Current spec ID:", currentSpecID)
    end

    -- Check if the detected spec belongs to an enemy
    local enemyStatus = LSD:IsEnemy(guid)
    print("Is Enemy:", enemyStatus)
end

-- Register the SPEC_DETECTED callback
LSD.callbacks:RegisterCallback("SPEC_DETECTED", OnSpecDetected)
