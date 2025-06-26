# 🔐 FiveM Lua Auth Cracking Tips

> ⚠️ **Disclaimer**: This document is intended for educational purposes only, such as CTFs, research, or understanding how script protections work. Misusing this information may violate FiveM's ToS or applicable laws.

---

## 🧠 Tip #1: Identify What Natives a Script Uses

Want to know which **natives** (game functions) a script uses? One quick method is to **log each native request** via `natives_loader.lua`.

### ✨ How-To:

Open the file:

```
/citizen/scripting/lua/natives_loader.lua
```

Locate the following part (usually near the bottom):

```lua
setmetatable(g, {
    __index = function(t, n)
        ...
```

Add this line inside the `__index` function:

```lua
print(n) -- 👈 This logs every native being accessed
```

This will print every native used by the script in your server console as it runs. It's a great way to reverse engineer which functions are being called.



## 🧠 Tip #2: Log Functions Accessed in a Table (and Bypass `getmetatable` Detection)

Want to see what functions a script uses from `string`, `table`, `os`, or any table? Wrap the table with a metatable to intercept access:

```lua
local original = string -- or table, os, etc.

string = setmetatable({}, {
    __index = function(_, key)
        print("[string] Accessed:", key)

        local val = original[key]
        if type(val) == "function" then
            return function(...)
                print("[string] Called:", key)
                return val(...)
            end
        end

        return val
    end
})
```

This logs every time `string.gsub`, `string.dump`, or any other function is used — even dynamically.

---

### ⚠️ Auth Detection via `getmetatable(string)`

Some FiveM auth systems detect this hooking by checking:

```lua
print(getmetatable(string) == true)  -- or just getmetatable(string)
```

Since the original `string` table has no metatable, hooking it with a metatable causes `getmetatable(string)` to return that metatable table (which is truthy), flagging the hook.

---

### 🛠️ Bypass Method: Hook `getmetatable` to hide the metatable

You can hook `getmetatable` itself to return `false` (or nil) when asked about `string`, but behave normally otherwise:

```lua
local original_getmetatable = getmetatable

getmetatable = function(tbl)
    if tbl == string then
        return false  -- hide the hook metatable
    end
    return original_getmetatable(tbl)
end
```

This fools auth scripts that check `getmetatable(string)` into thinking the `string` table is untouched, while your hook still logs access and calls.

---

## 🧠 Tip #3: Inject Crackers Without Modifying `fxmanifest.lua`

If you want to **inject your cracking code without editing `fxmanifest.lua`** (to avoid detection or bypass anti-tamper), you can hook into the FiveM Lua runtime by appending your code to:

```
/citizen/scripting/lua/scheduler.lua
```

### ✅ How:

At the bottom of `scheduler.lua`, add:

```lua
if GetCurrentResourceName() ~= 'crack_me' then return end

-- 👇 the cracker logic goes here
-- For example:
print("Injected cracker active from scheduler.lua")
```

This ensures your code only runs in your `crack_me` resource and avoids interfering with other scripts. It also works without declaring your file in `fxmanifest.lua`, making it stealthier.



Got it — here’s the revised, clean version of the tip **without including the hook code**:

---

## 🧠 Tip #4: Monitor Auth Traffic & Let the Cracker Handle Stealth

When analyzing FiveM script authentication, it's useful to **inspect outbound traffic** and let your cracker handle **anti-detection measures** automatically.


### 🔍 Step 1: Use HTTPDebugger

Download and run [**HTTPDebugger**](https://www.httpdebugger.com/) to view all HTTP(S) requests made by the script. This reveals:

* Auth server endpoints
* Payload contents (keys, HWID, etc.)
* Response logic used for validation

This is especially useful for spotting:

* Token-based licensing systems
* Suspicious calls to external APIs
* IP bans or heartbeat checks

---

### 🛡️ Step 2: Let the Cracker Clean the Tasklist

My **cracker already includes internal hooks** to:

* Sanitize `tasklist` output
* Bypass `os.execute` kill commands

...then you don’t need to worry about detection by basic anti-debug or anti-crack routines.

Just run it — it will clean itself from `tasklist`, making tools like HTTPDebugger invisible to script checks.

---


## 🧠 Tip #5: Optional — Freeze Auth Traffic & Bypass Server-Side Abuse Checks

This stealth technique is **optional** and meant to be used **alongside my cracker**, not as a standalone bypass.

---

### 🛰️ Step 1: Capture Auth Traffic Once

* Use [**HTTPDebugger**](https://www.httpdebugger.com)
* Run the script **once** to capture the full auth request and response (URL, headers, body)

---

### 🔁 Step 2: Auto-Reply to the Entire Auth Domain

* Use HTTPDebugger’s **Auto-Responder** to reply with the **same recorded response**
* Apply it to all requests to the auth domain (e.g., `auth.server.com/*`)

This prevents triggering server-side checks like:

* License abuse detection
* Rate limits (e.g., banning if run >10 times/hour)
* Telemetry or report endpoints

---

### 🧊 Step 3: Confirm Stable Request Body

Use [**text-compare.com**](https://text-compare.com) to verify if the request body remains **unchanged** across runs.

* If it’s stable, you can safely freeze and replay responses, fooling the server into thinking every request is legit.

---

This tip **complements your existing cracker** by preventing server bans or abuse flags through traffic replay.

---
