# 💥 FiveM Script Auth Cracker

> ⚠️ **Disclaimer:**  
> This project is for **educational and research purposes only**.  
> Using Custom auth system **violates FiveM Terms of Service** and may be **illegal**.  
> Use this tool only on systems you own or have permission to test or against fivem in first place.
> We do not take any responsibility for any damage, legal issues, or consequences resulting from the use of this tool.

---

## 🚀 What is this?

A lightweight tool that **logs and replays function calls** used by authentication systems in non escrow-protected FiveM scripts. This enables bypassing checks **after a single successful run**, effectively tricking the script into thinking it's authorized.

---

## ✅ Confirmed Working On:

- 🟢 Waveshield (latest)
- 🟢 ChocoHax (latest)
- 🟢 FileSafety v2
- 🟢 DNZ v3
- 🟢 Reaper v4
- ...and many more

---

## 🧠 How It Works

1. Hooks and logs common Lua and FiveM native calls.
2. Stores return values in `data.json`.
3. On second run, **replays the stored responses** instead of calling the real functions.
4. Completely skips the authentication logic on future executions.

---

## 🔧 Features

🧠 One-run bypass using JSON replay system

- Hooks: debug.getinfo, PerformHttpRequestInternal, Citizen.InvokeNative, and more

- Seamless drop-in integration

- Offline-capable: doesn’t need access to remote auth servers after first run

---

## 📌 Notes
- data.json is automatically created once you execute `konan save` when you see successful authenticated message on the console.
- This script is not universal; some highly obfuscated scripts may require manual patching.
---

## 👥 Community
Want to share techniques or test new methods?
contact me on Discord: kkonann
