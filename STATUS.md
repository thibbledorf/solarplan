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

### Phase 1: Solar Array Expansion (✅ DECIDED)

**New Panels:** 2× 200W (additional 400W capacity)

**Configuration Chosen: Dedicated Renogy 40 MPPT**

**Rationale:**
- Current system: 6 panels via concentrator to Renogy 150/60 (peak output ~900W with some clipping)
- Renogy 150/60 max output: 60A (720W @ 12V) — already experiencing ~15A clipping
- New approach: Add 2 panels to dedicated Renogy 40 MPPT to avoid clipping
- Renogy 40 max output: 40A (480W @ 12V) — perfect for 2×200W panels (~300W actual)

**Wiring Configuration:**

Panel 1 (+) → MPPT Input (+)  
Panel 1 (-) ← → Panel 2 (+) (jumper cable with waterproof connector)  
Panel 2 (-) → MPPT Input (-)  

Result: Series connection = ~36V input to Renogy 40 (well within 150V max)

**MPPT Output Connections:**
- Renogy 150/60 output → Victron Bus
- Renogy 40 output → Victron Bus (same location, parallel connection)

**Advantages:**
✅ No clipping on new panels  
✅ Independent MPPT optimization  
✅ Each controller handles appropriate load  
✅ Prepares for multi-MPPT architecture  
✅ Uses equipment on hand (no waiting for new gear)  
✅ Future expansion ready  

**Status:** ✅ **Decision locked. Ready for installation.**

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

### Phase 4: A/C Power Expansion (✅ SPECIFIED)

**Component:** Velair i09 9,000 BTU 12V DC Variable Speed Smart Marine A/C with Reverse-Cycle Heat

**Specifications:**
- Power supply: **12V DC direct** (no inverter needed)
- Max power consumption: **45A** (full cooling)
- Eco mode: **18A**
- Cooling capacity: 2500–9000 BTU/h (variable)
- Reverse-cycle heating capability included
- VSD (Variable Speed Drive) inverter for smooth startup, no power peaks
- Water-cooled condenser: requires 3.2+ GPM seawater circulation
- Smart M7 Touch control panel (26'/8m cable)

**Integration Plan:**
- [ ] Wire 45A circuit from Victron Bus to A/C unit
- [ ] Install appropriate 45A breaker/fuse protection
- [ ] Seawater intake loop (through through-hull fitting)
- [ ] Seawater return loop (drain via through-hull)
- [ ] Mount control panel in cabin (standard 503-sized box)
- [ ] Test VSD startup behavior with battery bank

**Key Advantage:** Direct 12V DC load — no AC inverter needed. Simple integration to Victron Bus as another DC load.

**Estimated Timeline:** Post-Orion S + Cerbo GX installation (to finalize bus capacity planning)

**Status:** ✅ **Specifications confirmed. Ready for circuit design phase.**

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

These need resolution before proceeding with remaining phases:

**Cerbo GX Integration:**
1. Will it be the primary monitoring interface, or supplement ME-ARC?
2. Internet connectivity desired (for remote monitoring via VRM portal)?
3. Integration with Magnum 2000 — direct connection or via separate data link?

**System Capacity Planning:**
1. With Velair i09 pulling max 45A, Orion S potentially pulling up to 50A, and loads in port — what's the realistic simultaneous draw on the 800Ah bank?
2. Do you anticipate needing a shunt monitor (Victron SmartShunt) for precise state-of-charge tracking?
3. Any plans for load shedding or priority management during low-battery conditions?

**Installation Sequencing:**
1. Which component should we tackle first? (Orion S config, Renogy 40 panel wiring, Cerbo GX setup, A/C circuit planning)

---

## 📋 Next Steps

**Decisions Made:**
✅ Solar expansion via dedicated Renogy 40 MPPT  
✅ A/C unit is Velair i09 (12V DC direct)  
✅ Both MPPT outputs to single Victron Bus  

**Immediate (Ready Now):**
1. ⏳ Choose installation sequence (see options below)
2. ⏳ Clarify Cerbo GX integration requirements
3. ⏳ Plan system capacity/load management strategy

**Installation Sequencing Options:**

**Option 1: Orion S First**
- Benefit: Addresses engine battery ↔ solar bridge (foundational)
- Config via VictronConnect
- Test with existing system before adding complexity

**Option 2: Renogy 40 Panel Wiring First**
- Benefit: Quick win, expands solar capacity immediately
- Physical installation (series panel wiring, Victron Bus connections)
- No new equipment to configure

**Option 3: Cerbo GX First**
- Benefit: Unified monitoring for all components going forward
- Setup before Orion S so you can monitor it during config
- Network/integration planning needed

**Short Term (Weeks):**
1. Install and configure chosen component
2. Test and verify operation
3. Install remaining two components
4. Test unified system with Cerbo GX monitoring

**Medium Term (Months):**
1. Install A/C seawater loop and electrical circuit
2. Commission Velair i09 and test VSD operation
3. Optimize system performance and battery management profiles
4. Monitor real-world power consumption patterns

**Long Term (Future):**
1. Evaluate complete Victron system migration (replace Renogy 150/60)
2. Plan additional solar panels and multi-MPPT architecture
3. Consider battery expansion if extended offshore cruising needed
4. Implement smart load shedding and automation

---

## 📚 Related Documentation

- [Main README](README.md) — Original system documentation
- [ABOUT.md](ABOUT.md) — Project scope and components
- [CHANGELOG.md](CHANGELOG.md) — Version history
- [CONTRIBUTING.md](CONTRIBUTING.md) — How to contribute updates

---

**Last Reviewed:** June 21, 2026  
**Next Review:** After Orion S installation complete
