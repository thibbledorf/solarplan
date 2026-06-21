# Cerbo GX Setup Guide

**Purpose:** Configure Victron Cerbo GX as the unified monitoring hub for your marine power system, with integration to Home Assistant and VRM (Victron Remote Management) for cloud access.

**System Role:** Cerbo GX = Source of Truth for all monitoring data  
**Integrations:** VRM Portal (cloud) + Home Assistant (local dashboard) + ME-ARC (supplementary)

---

## Prerequisites

- [ ] Cerbo GX unit (hardware)
- [ ] Power cable (to Victron bus 12V)
- [ ] Ethernet cable (backup, optional for initial setup)
- [ ] WiFi network access with 2.4/5GHz capability
- [ ] VictronConnect app (mobile or desktop)
- [ ] Victron account (for VRM Portal)
- [ ] Home Assistant instance (being restored, will integrate later)

---

## Phase 1: Physical Installation

### Power Connection

**Cerbo GX Power Requirements:**
- Input: 9–32V DC (12V nominal)
- Typical consumption: 1–2A
- Source: Main Victron Bus (via dedicated 5A breaker recommended)

**Steps:**

1. **Breaker/Fuse:** Install 5A inline fuse holder on positive line from Victron bus
2. **Positive (Red):** From fuse to Cerbo GX positive terminal
3. **Negative (Black):** From Victron bus negative to Cerbo GX negative terminal
4. **Cable gauge:** 14 AWG or larger for safety

**Marine Installation Notes:**
- Use tinned copper wire for corrosion resistance
- Keep power leads away from high-current circuits
- Verify polarity before applying power (red=+, black=−)

### Physical Placement

Choose a location that balances:
- **WiFi signal** (avoid metal shielding, keep away from cabin walls)
- **Temperature** (avoid direct sun, keep cool)
- **Accessibility** (easily reachable for status checks)
- **Cable reach** (12V power cable to Victron bus, ethernet backup)

**Recommended:** Electrical panel area or near Victron bus (central hub location)

---

## Phase 2: Network Configuration

### WiFi Connection (Primary Method)

**Steps:**

1. **Power on Cerbo GX**
   - Connect 12V power from Victron bus
   - LED indicators should light up (see manual for LED status meanings)

2. **Access Cerbo GX Interface**
   - Open a web browser from any device on your WiFi network
   - Navigate to: `http://cerbo-gx.local` 
   - Or use the Cerbo GX IP address directly (check your router DHCP list)
   - Default login: User `root`, Password `victron`

3. **Configure WiFi**
   - Go to **Settings** → **System** → **Network**
   - Select **WiFi** as connection method
   - Scan for your onboard WiFi network
   - Enter WiFi password and connect
   - Verify connection by checking signal strength

4. **Network Settings**
   - Hostname: Leave as default or set to `cerbo-gx` (easy to reference)
   - DNS: Auto (recommended) or use `8.8.8.8` / `8.8.4.4`
   - DHCP: Enabled (recommended)

5. **Verify Connectivity**
   - Refresh browser — should stay connected
   - Check that "Network" shows "Connected"

### Ethernet Connection (Backup, If Needed)

If WiFi signal is unstable, switch to Ethernet:

1. Connect standard Ethernet cable from Cerbo GX to shore power/vessel network
2. Web interface automatically detects Ethernet and prioritizes it
3. No additional configuration needed
4. WiFi can remain configured as fallback

---

## Phase 3: VictronConnect Configuration

### Install VictronConnect App

**Mobile (iOS/Android):**
- Search "VictronConnect" in App Store or Google Play
- Install official Victron app

**Desktop (Mac/Windows/Linux):**
- Download from [Victron Energy](https://www.victronenergy.com/app/victronconnect)

### Connect to Cerbo GX via VictronConnect

**Initial Setup:**

1. **Open VictronConnect**
2. **Scan for Devices**
   - App will auto-discover Cerbo GX on local network
   - Should show: "Cerbo GX" with IP address
3. **Connect to Cerbo GX**
   - Tap/click the Cerbo GX entry
   - Confirm connection (may ask for default password: `victron`)
4. **Change Default Password** (RECOMMENDED)
   - Go to **Settings** → **Users**
   - Change root password to something secure
   - Document it securely (password manager)

### Configure VRM (Cloud Access)

VRM enables remote monitoring from anywhere via Victron Portal.

**Steps:**

1. In VictronConnect, go to **Settings** → **VRM Portal Settings**
2. Enable **VRM Portal** (toggle ON)
3. Sign in with Victron account:
   - If you don't have one, create at [VRM Portal](https://vrm.victronenergy.com)
   - Use email address and create password
4. **VRM Instance ID** will be assigned automatically
5. Note the ID: `vrm.victronenergy.com/install/{YOUR_ID}`
6. Verify VRM connection: Status should show "Connected"

**What VRM Enables:**
- Remote monitoring from web browser or mobile
- Historical data logging (power generation, consumption)
- Alerts and notifications (low battery, high temperature, etc.)
- Access from anywhere with internet

---

## Phase 4: Device Discovery & Configuration

### Discover Connected Devices

Once Cerbo GX is online, it will auto-discover devices on the Victron network.

**Current System Devices:**

**Active Now:**
- [ ] Victron Bus (internal device)
- [ ] Renogy 150/60 MPPT (may require integration if not auto-discovered)

**Pending Installation:**
- [ ] Orion S 50/50 DC-DC (will discover once powered & connected)
- [ ] Renogy 40 MPPT (may require manual integration)

**Status in VictronConnect:**

1. **Go to Settings** → **Devices** or **System**
2. View list of discovered devices
3. For each device, note:
   - Device name
   - Firmware version
   - Last communication time
   - Status (OK / Warning / Error)

### Manual Device Integration (If Needed)

If Renogy MPPT controllers don't auto-discover:

**Option A: USB Integration (Renogy controllers)**
1. Some Renogy models support USB-to-Cerbo connection
2. Check Renogy controller manual for USB port
3. Connect USB to Cerbo GX USB input
4. Cerbo GX should auto-detect and configure

**Option B: Network Integration (If available)**
1. Some newer Renogy models support Ethernet/WiFi reporting
2. Configure MPPT controller's network settings
3. Set same network as Cerbo GX
4. May require Renogy app configuration first

**Note:** Current Renogy 150/60 may have limited integration — document what's available and plan for future Victron MPPT migration.

---

## Phase 5: Home Assistant Integration (When HA is Restored)

**Timeline:** Complete after HA backup restoration  
**Purpose:** Create local dashboard with all system monitoring data

### Install Victron Integration in Home Assistant

**Steps:**

1. **Home Assistant** → **Settings** → **Devices & Services** → **Integrations**
2. **Create Integration** → Search for "Victron"
3. If not available, install HACS (Home Assistant Community Store):
   - Go to HACS → Integrations
   - Search "Victron Energy"
   - Install "Victron Inverters"
4. **Configure Integration**
   - Hostname/IP: `cerbo-gx.local` (or IP address)
   - Username: `root`
   - Password: (the one you set in Phase 3)
5. **Test Connection**
   - HA should discover Cerbo GX
   - Entities should appear (battery voltage, current, SOC, etc.)

### Create Monitoring Dashboard

Once integration is active in HA:

**Recommended Dashboard Layout:**

```
┌─────────────────────────────────────────┐
│      MARINE POWER SYSTEM DASHBOARD      │
├─────────────────────────────────────────┤
│  BATTERY STATUS                         │
│  ├─ Voltage: 13.2V                      │
│  ├─ Current: +45A (charging)            │
│  ├─ State of Charge: 78%                │
│  └─ Power: 594W in                      │
│                                         │
│  SOLAR GENERATION                       │
│  ├─ Renogy 150/60: 650W                 │
│  ├─ Renogy 40: 180W                     │
│  └─ Total: 830W                         │
│                                         │
│  LOADS & INVERTER                       │
│  ├─ DC Loads: 120A                      │
│  ├─ Magnum 2000: 1200W AC output        │
│  └─ System Efficiency: 94%              │
│                                         │
│  SYSTEM HEALTH                          │
│  ├─ Orion S: Connected, OK              │
│  ├─ Cerbo GX: Online                    │
│  └─ Network: WiFi (RSSI -65dBm)         │
└─────────────────────────────────────────┘
```

**Key Entities to Monitor:**

| Entity | Purpose |
|--------|---------|
| `sensor.battery_voltage` | Main bank voltage |
| `sensor.battery_current` | Charge/discharge current |
| `sensor.battery_soc` | State of charge % |
| `sensor.solar_power` | Total solar generation |
| `sensor.dc_load_current` | DC load draw |
| `sensor.inverter_ac_power` | Magnum 2000 output |

**Automation Ideas (Post-Setup):**
- Alert when battery SOC drops below 30%
- Log hourly solar generation to database
- Trigger load shedding when voltage drops below 12.8V
- Notify when Cerbo GX loses network connection

---

## Phase 6: Monitoring Checklist

### Initial Verification (After Power On)

- [ ] Cerbo GX LED indicators show normal status
- [ ] Web interface accessible at `cerbo-gx.local`
- [ ] WiFi connection stable (signal strength > -70 dBm)
- [ ] VictronConnect app can discover Cerbo GX
- [ ] VRM Portal connection active
- [ ] Devices list shows expected components
- [ ] Voltage readings match Victron bus (should be ~13.2V when not discharging)
- [ ] Current shows 0–2A (just Cerbo GX consumption)

### Weekly Checks

- [ ] VRM Portal accessible from remote location
- [ ] HA integration receiving updated data
- [ ] No error messages in VictronConnect
- [ ] WiFi signal strength stable
- [ ] No unexpected high temperature warnings

### Monthly Reviews

- [ ] Export VRM data to review power generation trends
- [ ] Check HA automations are working as expected
- [ ] Document any firmware updates available
- [ ] Verify backups are current (data, configuration)

---

## Configuration Parameters Reference

### Network Settings (VictronConnect Settings → System → Network)

| Setting | Value | Notes |
|---------|-------|-------|
| Connection Type | WiFi | Primary; Ethernet as backup |
| Hostname | cerbo-gx | For local network discovery |
| DHCP | Enabled | Assigns IP automatically |
| WiFi SSID | [Your SSID] | Your onboard network |
| WiFi Password | [Secure] | Document securely |

### VRM Settings (VictronConnect Settings → VRM Portal Settings)

| Setting | Value | Notes |
|---------|-------|-------|
| VRM Portal | Enabled | Cloud monitoring active |
| VRM Instance | [Auto] | Auto-assigned by Victron |
| Data Update Interval | 1 min | Real-time monitoring |

### User Management (VictronConnect Settings → Users)

| User | Default Password | Action |
|------|------------------|--------|
| root | victron | **CHANGE IMMEDIATELY** |

---

## Troubleshooting

### WiFi Connection Issues

**Problem:** Cerbo GX won't connect to WiFi

**Solutions:**
1. Check WiFi password is correct (case-sensitive)
2. Verify 2.4GHz band is available (Cerbo GX may not support 5GHz)
3. Move Cerbo GX closer to router (reduce interference)
4. Restart Cerbo GX: Power off 30 seconds, power back on
5. Fall back to Ethernet cable for testing

### Device Not Discovered

**Problem:** Orion S or MPPT not showing in device list

**Solutions:**
1. Verify device is powered on
2. Check Victron bus connections are secure
3. Restart Cerbo GX and device
4. Check device firmware is current
5. Manual integration may be needed (see Phase 4)

### VRM Connection Fails

**Problem:** VRM Portal shows "Not Connected"

**Solutions:**
1. Verify WiFi internet connection is working
2. Check Victron account login is correct
3. Verify VRM credentials in VictronConnect
4. Test from browser: `vrm.victronenergy.com`
5. Check firewall isn't blocking Victron servers

### Home Assistant Integration Not Working

**Problem:** HA can't connect to Cerbo GX

**Solutions:**
1. Verify HA and Cerbo GX are on same WiFi network
2. Ping Cerbo GX from HA: `ping cerbo-gx.local`
3. Verify root password is correct (check what was set in Phase 3)
4. Restart HA integration: Disable → Enable
5. Check HA logs for detailed error messages

---

## Next Steps

**After Cerbo GX is Stable (Week 1):**
1. ✅ Verify all three phases (Power, Network, VictronConnect) working
2. ✅ Document VRM Portal access details
3. ✅ Test from remote location (phone/computer away from boat)

**When Home Assistant is Restored (Week 2):**
1. ⏳ Install Victron integration
2. ⏳ Create monitoring dashboard
3. ⏳ Set up automations and alerts

**Before Orion S Installation (Week 3):**
1. ⏳ Verify Cerbo GX can discover new devices
2. ⏳ Plan device discovery test
3. ⏳ Prepare to monitor Orion S startup via HA

---

## Support & Documentation

- **Victron Knowledge Base:** https://www.victronenergy.com/support-and-downloads/technical-support
- **Cerbo GX Manual:** https://www.victronenergy.com/live/cerbo-gx:overview
- **VRM Portal Guide:** https://vrm.victronenergy.com
- **Home Assistant Docs:** https://www.home-assistant.io/

---

**Version:** 1.0  
**Last Updated:** June 21, 2026  
**Status:** Ready for Implementation
