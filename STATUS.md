# Current System Status & Roadmap

**Last Updated:** June 21, 2026  
**System Owner:** Eric (thibbledorf)  
**Location:** Vessel (marine installation)

---

## 🔋 Current System Architecture

### Power Sources

| Component | Spec | Status | Notes |
|-----------|------|--------|-------|
| **Main Battery Bank** | 4× 200Ah LiFePO4 | ✅ Active | 800Ah total usable capacity |
| **Engine Battery** | 4D AGM | ✅ Active | Engine starting battery, separate from main bank |
| **Solar Array** | 6× 200W panels (1200W) | ✅ Active | 2 strings of 3 panels via concentrator |

### Charge Management

| Component | Model | Status | Notes |
|-----------|-------|--------|-------|
| **Solar MPPT** | Renogy 150/60 | ✅ Active | Primary solar controller, single 12V input from concentrator |
| **DC-DC Converter** | Renogy Energy 50A | 🔄 Planned Replacement | Low quality unit, will be replaced by Orion S 50/50 |
| **Engine-to-Solar Bridge** | Orion S 50/50 | 📦 Pending Installation | Will manage charge flow between 4D AGM and solar array |

### Power Distribution & Loads

| Component | Model | Status | Notes |
|-----------|-------|--------|-------|
| **Main Distribution Bus** | Victron Bus | ✅ Active | DC power hub connecting all sources to loads/inverters |
| **Main Inverter/Charger** | Magnum 2000 | ✅ Active | 2000W rated, primary AC power and charging |
| **Remote Control** | Magnum ME-ARC | ✅ Active | Remote monitoring/control of Magnum 2000 |
| **Monitoring Hub** | Victron Cerbo GX | 📦 Pending Installation | Will provide unified monitoring of Victron ecosystem |

### Planned AC Loads

| Component | Spec | Status | Notes |
|-----------|------|--------|-------|
| **A/C Inverter** | 8kW slow-start | 📦 Pending Integration | Dedicated A/C unit with inrush management |

**Current DC Load Coverage:** Port and sea operations (all DC loads except A/C)

---

## 📊 Power Flow Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                    SOLAR GENERATION                              │
│   6× 200W Panels (2 strings) → Concentrator → 12V Single Line   │
└────────────────────────────┬────────────────────────────────────┘
                              │
                    ┌─────────▼────────────┐
                    │  Renogy 150/60 MPPT │
                    └─────────┬────────────┘
                              │
    ┌─────────────────────────┼─────────────────────────┐
    │                         │                         │
┌───▼────────────────┐   ┌───▼──────────────┐   ┌────▼──────────────┐
│  4D AGM Engine     │   │ Victron Main     │   │ Orion S 50/50     │
│  Battery (Separate)│───│ Distribution Bus │   │ (Bridge: Engine←→ │
│                    │   │                  │   │  Solar)           │
└────────────────────┘   └───┬──────────────┘   └───────────────────┘
                              │
                    ┌─────────▼────────────┐
                    │ 4× 200Ah LiFePO4     │
                    │ Main Battery Bank    │
                    │ (800Ah Total)        │
                    └─────────┬────────────┘
                              │
            ┌─────────────────┼─────────────────┐
            │                 │                 │
    ┌───────▼────────┐   ┌───▼──────────┐   ┌─▼────────────────┐
    │  DC Loads      │   │ Magnum 2000  │   │ 8k A/C Inverter  │
    │  (In Port/Sea) │   │ Inv/Charger  │   │ (Pending)        │
    └────────────────┘   └──────────────┘   └──────────────────┘
                              │
                        ┌─────▼─────┐
                        │   ME-ARC   │
                        │  (Remote)  │
                        └────────────┘
```

---

## 📈 Expansion Plans

### Phase 1: Solar Array Expansion (⏳ PENDING DECISION)

**New Panels:** 2× 200W (additional 400W capacity)

**Configuration Options:**

**Option A: Add to Existing Strings**
- Pros: Single MPPT controller, simpler wiring
- Cons: Unbalanced strings (3+3 → 4+3 or 3+4), potential efficiency loss
- Decision: *Need to evaluate string voltage/current impact*

**Option B: Dedicated Second Energy MPPT**
- Pros: Balanced strings, optimal MPPT tracking, future flexibility
- Cons: Additional equipment, more complex wiring
- Decision: *Aligns with Victron ecosystem plan*

**Status:** ⏳ **Awaiting decision on panel configuration**

---

### Phase 2: DC-DC Converter Upgrade (🔄 IN PROGRESS)

**Current:** Renogy Energy 50A (low quality, to be replaced)  
**Replacement:** Orion S 50/50 (pending installation)

**Function:** Bridge between 4D AGM engine battery and solar array  
**Bidirectional:** Can charge from solar or engine alternator

**Tasks:**
- [ ] Physical installation and wiring
- [ ] Configuration via VictronConnect (see Configuration section below)
- [ ] Testing and commissioning
- [ ] Integration with Cerbo GX monitoring

**Status:** 📦 **Pending installation**

---

### Phase 3: Monitoring System Upgrade (📦 PENDING INSTALLATION)

**Component:** Victron Cerbo GX

**Purpose:** 
- Unified monitoring of Victron ecosystem (Orion S, MPPT, future additions)
- Integration point for real-time data
- Remote monitoring capability
- Eventual coordination with Magnum 2000

**Integration Points:**
- Victron bus (via VE.Can or Ethernet)
- Solar MPPT (Renogy 150/60 via USB/Ethernet if supported)
- Orion S DC-DC (via VE.Can or Ethernet)

**Status:** 📦 **Pending installation and configuration**

---

### Phase 4: A/C Power Expansion (📦 PENDING INTEGRATION)

**Component:** 8kW slow-start A/C inverter

**Integration Questions:**
- [ ] Power source: Direct from 800Ah main bank or separate tap?
- [ ] Load distribution: Run A/C through Magnum 2000 + dedicated 8k inverter?
- [ ] Startup coordination: How does slow-start interact with bank capacity?
- [ ] Breaker/protection sizing: 8k startup inrush requirements?

**Estimated Timeline:** Post-Orion S + Cerbo GX installation

**Status:** 📋 **Pending integration planning**

---

## 🛠️ Configuration Tasks (Chat-Based)

The following components require configuration assistance via chat:

### ✅ Completed
- *None yet — this is the starting point*

### 🔄 In Progress or Pending

1. **Orion S 50/50 Configuration**
   - VictronConnect setup
   - Voltage/current settings for 4D AGM input
   - Solar input parameters
   - Charging profile tuning
   - Integration with Victron bus

2. **Cerbo GX Installation & Setup**
   - Network configuration
   - Device discovery and pairing
   - Dashboard setup for monitoring
   - Integration with existing Magnum system
   - Remote access configuration

3. **Solar Array Expansion (Post-Decision)**
   - If Option B selected: New Energy MPPT configuration
   - Concentrator settings for expanded array
   - Load balancing across MPPT controllers

4. **8kW A/C Inverter Integration**
   - Power source routing
   - Breaker sizing and placement
   - Coordination with Magnum 2000
   - Load management strategy

---

## ❓ Outstanding Questions

These need resolution before proceeding with each phase:

**Solar Expansion:**
1. What is the current voltage output of each 3-panel string?
2. What is the current output per string under typical conditions?
3. Can the existing Renogy 150/60 handle a 4-3 string configuration, or is Option B (new MPPT) preferred?

**8kW A/C Inverter:**
1. Direct battery connection or separate tap from main bank?
2. Will A/C loads be split between Magnum 2000 and 8k inverter, or dedicated split?
3. Slow-start ramp specifications — what inrush current does it limit to?
4. Total system A/C capacity planning: Magnum 2000 + 8k = potential 10kW, but does battery support simultaneous use?

**Cerbo GX Integration:**
1. Will it be the primary monitoring interface, replacing ME-ARC?
2. Internet connectivity desired (for remote monitoring)?
3. Integration with Magnum 2000 — direct connection or via separate data link?

---

## 📋 Next Steps

**Immediate (Next Session):**
1. Decide on solar expansion approach (Option A vs Option B)
2. Clarify 8kW A/C inverter integration requirements
3. Plan Orion S 50/50 installation sequence

**Short Term (Weeks):**
1. Install and configure Orion S 50/50
2. Install and configure Cerbo GX
3. Test unified monitoring system

**Medium Term (Months):**
1. Implement solar expansion (once approach is decided)
2. Install and integrate 8kW A/C inverter
3. Optimize system performance and charging profiles

**Long Term (Future):**
1. Evaluate complete Victron system migration
2. Consider additional battery expansion if cruise range needs increase
3. Explore smart load management and automation

---

## 📚 Related Documentation

- [Main README](README.md) — Original system documentation
- [ABOUT.md](ABOUT.md) — Project scope and components
- [CHANGELOG.md](CHANGELOG.md) — Version history
- [CONTRIBUTING.md](CONTRIBUTING.md) — How to contribute updates

---

**Last Reviewed:** June 21, 2026  
**Next Review:** After Orion S installation complete
