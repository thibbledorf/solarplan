# Home Assistant Integration Setup Guide

**Purpose:** Integrate Cerbo GX, Renogy MPPT, and Magnum 2000 into Home Assistant for unified marine power system monitoring.

**System Architecture:**
```
Home Assistant (Fresh Install)
├── Victron Integration → Cerbo GX (192.168.1.171)
│   ├─ SmartShunt 1000A/50mV
│   └─ Orion S 50/50 (when installed)
│
├── Renogy MPPT (Bluetooth)
│   ├─ Renogy 150/60
│   └─ Renogy 40 (when added)
│
└── Magnum 2000 (ME-ARC Remote)
    └─ Via Modbus or custom integration
```

**Status:** Ready for implementation when HA is fully online

---

## Phase 1: Victron Integration (Cerbo GX)

### Prerequisites
- Home Assistant running
- Cerbo GX online at `192.168.1.171`
- HA and Cerbo GX on same network

### Installation Steps

**1. Install Victron Integration**

Go to **Settings** → **Devices & Services** → **Integrations**

**Option A: Built-in Integration (if available)**
1. Click **Create Integration** (+)
2. Search for "Victron"
3. If found, select and follow prompts
4. Enter Cerbo GX details:
   - Host: `192.168.1.171` (or `cerbo-gx.local`)
   - Username: `root`
   - Password: `victron` (or the password you set)

**Option B: HACS Community Integration (if built-in not available)**
1. Go to **HACS** → **Integrations**
2. Search "Victron Energy"
3. Install "Victron Inverters" or similar
4. Restart Home Assistant
5. Then follow Option A steps

### Verification

Once connected, you should see **new devices/entities**:

**Device: Cerbo GX**
- Entities:
  - `sensor.cerbo_gx_battery_voltage` - Battery voltage
  - `sensor.cerbo_gx_battery_current` - Charge/discharge current
  - `sensor.cerbo_gx_battery_soc` - State of charge %
  - `sensor.cerbo_gx_battery_power` - Power (W)

**Device: SmartShunt**
- Same entities as above (direct from shunt)

**When Orion S is installed:**
- `sensor.orion_s_input_voltage` - Engine battery voltage
- `sensor.orion_s_output_voltage` - Solar output voltage
- `sensor.orion_s_current` - Current flowing
- `binary_sensor.orion_s_mode` - Operating mode

**Test:** Go to **Developer Tools** → **States** and search "victron" — you should see all entities listed.

---

## Phase 2: Renogy MPPT Bluetooth Integration

### Prerequisites
- Renogy MPPT (150/60 and/or 40)
- Bluetooth enabled on controller
- Home Assistant with Bluetooth support

### Installation Steps

**1. Enable Bluetooth Discovery in HA**

Go to **Settings** → **Devices & Services** → **Bluetooth**

- Verify Bluetooth is enabled on your HA system
- Should show available Bluetooth devices

**2. Add Renogy MPPT**

**Option A: Auto-Discovery**
1. Power on Renogy MPPT (or ensure it's powered)
2. HA should discover it automatically (may take 1-2 minutes)
3. Click **Create** when you see "Renogy" device
4. Pair when prompted

**Option B: Manual Add**
1. Go to **Settings** → **Devices & Services**
2. Click **Create Integration** (+)
3. Search "Renogy"
4. If available, follow integration prompts
5. Select your MPPT from list
6. Complete pairing

### Configuration

**Once paired, you'll see entities:**

**Device: Renogy MPPT 150/60**
- `sensor.renogy_150_60_pv_voltage` - Panel voltage
- `sensor.renogy_150_60_pv_current` - Panel current
- `sensor.renogy_150_60_pv_power` - Solar power (W)
- `sensor.renogy_150_60_battery_voltage` - Battery voltage
- `sensor.renogy_150_60_battery_current` - Charge current
- `sensor.renogy_150_60_charging_status` - Status (MPPT, bulk, float, etc.)

**Repeat for Renogy 40** once it's installed and powered.

### Troubleshooting

**MPPT not discovered:**
- Ensure Bluetooth is enabled on controller
- Restart MPPT
- Restart HA
- Check Bluetooth range (Renogy has ~10m range)

---

## Phase 3: Magnum 2000 Integration

### Prerequisites
- Magnum 2000 inverter/charger
- ME-ARC remote (you have this)
- Network connection to Magnum (if available)

### Option A: Modbus TCP (Preferred if supported)

Some Magnum units support Modbus TCP protocol. Check your model documentation.

**If supported:**

1. Get Magnum's IP address or network interface details
2. Go to **Settings** → **Devices & Services** → **Create Integration**
3. Search "Modbus"
4. Configure:
   - Host: Magnum IP address
   - Port: 502 (typical Modbus TCP)
   - Type: TCP

**Entities available:**
- Inverter mode (on/off/search)
- AC voltage in/out
- Battery voltage
- Current draw
- Temperature

### Option B: ME-ARC Remote Integration (Manual)

Since ME-ARC doesn't have native HA integration, monitor via:

1. **Template Sensors** — Create manual sensors based on ME-ARC display
2. **Notes in HA** — Document Magnum status separately
3. **Automations** — Alert if Magnum goes offline

**Create Template Sensor:**

Go to **Settings** → **Automations & Scenes** → **Create Automation**

Or edit `configuration.yaml`:
```yaml
template:
  - sensor:
      - name: "Magnum 2000 Mode"
        unique_id: magnum_mode
        state: "Search"  # Update manually or via automation
      - name: "Magnum AC Output"
        unique_id: magnum_ac_output
        unit_of_measurement: "W"
        state: "0"  # Update based on ME-ARC readings
```

### Option C: Community Custom Integration

Search Home Assistant community forums for "Magnum" integrations:
- HACS might have community-created Magnum integrations
- Some users have created Modbus-based solutions
- Check GitHub for recent community projects

---

## Phase 4: Create Unified Marine Dashboard

### Create New Dashboard

1. Go to **Dashboards** (sidebar)
2. Click **Create Dashboard** (+)
3. Name it: "Marine Power System"
4. Click **Create**

### Add Cards

**Card 1: Battery Status**
```
Type: Vertical Stack Card

Content:
├─ Gauge: SmartShunt Voltage (12-15V range)
├─ Gauge: SmartShunt SOC (0-100%)
├─ History Graph: Battery Current (last 24h)
└─ History Graph: Battery Power (last 24h)
```

**Card 2: Solar Generation**
```
Type: Vertical Stack Card

Content:
├─ Gauge: Renogy 150/60 Power
├─ Gauge: Renogy 40 Power
├─ Entity: Renogy 150/60 PV Voltage
├─ Entity: Renogy 40 PV Voltage
└─ History Graph: Total Solar Power (last 24h)
```

**Card 3: System Status**
```
Type: Horizontal Stack Card

Content:
├─ Magnum 2000 Mode (from template or manual)
├─ Magnum AC Output (W)
├─ SmartShunt Current (A)
└─ System Efficiency (%)
```

**Card 4: Alerts & Status**
```
Type: Conditional Card

Show if: Battery SOC < 30%
Content: "⚠️ Low Battery - SOC below 30%"

Show if: Magnum offline
Content: "⚠️ Magnum Offline"
```

**Card 5: Data Export**
```
Type: Statistic Card

Metrics:
├─ Total Solar Energy (kWh) - last 24h
├─ Total DC Load Energy (kWh)
├─ Efficiency (%)
└─ Battery Cycles
```

### Example YAML for Custom Dashboard

Edit `configuration.yaml` or use Dashboard UI to add:

```yaml
automation:
  - alias: "Low Battery Alert"
    trigger:
      platform: numeric_state
      entity_id: sensor.smartshunt_battery_soc
      below: 30
    action:
      service: persistent_notification.create
      data:
        message: "Battery SOC is below 30%"
        title: "⚠️ Low Battery Alert"
        notification_id: "low_battery"

  - alias: "High Temperature Alert"
    trigger:
      platform: numeric_state
      entity_id: sensor.smartshunt_temperature
      above: 50
    action:
      service: persistent_notification.create
      data:
        message: "Battery temperature high"
        title: "⚠️ Temperature Alert"
```

---

## Phase 5: Initial Verification Checklist

After setup, verify all integrations:

- [ ] **Victron**
  - [ ] Cerbo GX connection shows "Connected" in Integration settings
  - [ ] SmartShunt entities appear in Developer Tools → States
  - [ ] Battery voltage entity shows current value (should be ~13V)
  - [ ] SOC entity shows percentage

- [ ] **Renogy MPPT (150/60)**
  - [ ] Device appears in Devices & Services
  - [ ] PV voltage entity shows > 0V (in sunlight)
  - [ ] Power entity updates in real-time

- [ ] **Renogy MPPT (40 - when added)**
  - [ ] Discovered and paired
  - [ ] Entities appear alongside 150/60

- [ ] **Magnum 2000**
  - [ ] Modbus connection established (if available)
  - [ ] OR manual template sensors created
  - [ ] Dashboard shows Magnum status

- [ ] **Dashboard**
  - [ ] All cards load without errors
  - [ ] Data updates in real-time
  - [ ] Graphs display historical data

---

## Common Issues & Solutions

### Victron Integration Won't Connect

**Problem:** "Connection refused" or "Failed to connect"

**Solutions:**
1. Verify Cerbo GX IP is correct: `ping 192.168.1.171`
2. Check Cerbo GX is powered and online
3. Verify username/password (default: `root`/`victron`)
4. Restart Cerbo GX: Power off 30 sec, power back on
5. Restart HA: Settings → System → Restart

### Renogy MPPT Not Discovered

**Problem:** Bluetooth device not showing up

**Solutions:**
1. Ensure MPPT is powered on
2. Check MPPT Bluetooth is enabled (manual check required)
3. Move HA device closer to MPPT (Bluetooth range ~10m)
4. Restart HA Bluetooth: Settings → System → Bluetooth restart
5. Forget device and re-pair

### Entities Not Updating

**Problem:** Values show stale data or "unavailable"

**Solutions:**
1. Check integration connection status
2. Verify devices are powered and online
3. Force refresh: Developer Tools → States, manually refresh
4. Restart integration: Settings → Devices & Services → ⋮ → Reload

### Magnum 2000 Data Missing

**Problem:** No Magnum data appearing in HA

**Solutions:**
1. Confirm Modbus TCP is available on your Magnum model
2. Check ME-ARC shows current status (verify Magnum is operating)
3. Create manual template sensors as workaround
4. Document readings from ME-ARC manually
5. Search community for custom Magnum solutions

---

## Advanced: Custom Templates & Automations

### Battery Health Calculation

```yaml
template:
  - sensor:
      - name: "Battery Health Score"
        unique_id: battery_health
        unit_of_measurement: "%"
        state: >
          {% if states('sensor.smartshunt_battery_soc') | float(0) > 80
             and states('sensor.smartshunt_battery_voltage') | float(0) > 13.0 %}
            85
          {% elif states('sensor.smartshunt_battery_soc') | float(0) > 50 %}
            75
          {% else %}
            50
          {% endif %}
```

### Daily Energy Summary

```yaml
automation:
  - alias: "Daily Energy Report"
    trigger:
      platform: time
      at: "06:00:00"
    action:
      service: persistent_notification.create
      data:
        title: "Daily Energy Summary"
        message: |
          Solar Generated: {{ states('sensor.renogy_daily_energy') }} kWh
          Loads Used: {{ states('sensor.smartshunt_daily_energy') }} kWh
          Efficiency: {{ states('sensor.battery_health') }}%
```

---

## Next Steps

1. **Test all integrations** after HA is fully online
2. **Create custom automations** for alerts and actions
3. **Build dashboard** with preferred metrics
4. **Document Magnum workaround** (if Modbus unavailable)
5. **Set up notification channels** (mobile, email, etc.)

---

## Support & Resources

- **Victron Integration:** https://www.victronenergy.com/live/
- **Home Assistant Docs:** https://www.home-assistant.io/
- **Renogy Support:** https://www.renogy.com/support
- **Magnum Community:** Home Assistant forums / GitHub

---

**Version:** 1.0  
**Last Updated:** June 24, 2026  
**Status:** Ready for Implementation
