---
title: Safety Procedures and Policies
date: 2025-05-11 
categories: [Computer Network, Introduction to Networking]
tags: [emergency procedures,emergency alert system]
---
# 🌐 Safety Procedures and Policies.

## 🔐 Best Practices for Technician Safety (aka "Alive technician = happy network")

1. **Protect against Electrostatic Discharge (ESD)** – Wear an ESD strap when handling components.
2. **Always power off the system before touching internal parts** – otherwise *you* might become the short circuit.
3. **Liquids + electronics = toxic combo** – Keep your coffee on the table, not on the motherboard ☕
4. **Using ladders?** Use the buddy system. Better to work in pairs than to fall solo.
5. **PPE (Personal Protective Equipment) is essential** – Gloves and goggles whenever required.
6. **Know the location of fire extinguishers** – and also what kind to use for different types of fires.

---

## 📒 Emergency Procedures & Fire Suppression

### 🚨 Emergency Procedures

- First thing: Always know the emergency exit routes – there’s a fire escape map near every entrance/lobby. Check it once, it might save your life!
- Exit signs glow red/green – usually battery-powered. Follow their direction when escaping.
- Help those around you – teamwork = safer exit.

---

### 🧯 Fire Suppression Systems – Levels & Types

1. **🔥 Building Level**
    - **Active protection**: Sprinklers, fire extinguishers
    - **Passive protection**: Fire-rated walls, ceilings, floors – to slow the fire down
    - **Goal**: Protect the entire building and its people.
2. **🔥 Room Level**
    - Needs at least 2 trigger points for the fire suppression system to activate.
    - Data centers usually use **gas-based systems** instead of water (because: electronics!)
    - Example gases: **FM-200** – absorbs heat, leaves no residue, non-toxic = safe for servers.
3. **🔥 Rack Level**
    - Fire is detected *inside* the rack → triggers targeted suppression.
    - Minimizes damage and reset costs.
    - Fastest response system – since fires often start small here.

---

![image.png](images/2025-05-11-Safety-Procedures-and-Policies/image.png)

### 🛑 Extra Fire-Safety Tech Checklist:

- **🚨 Emergency Alert System (EAS)**
    - Default: Sirens + flashing lights
    - Can include text, email, voice alerts (especially in high-security setups)
- **🧯 Class C Fire Extinguisher**
    - For **electrical fires only** – remember “Class C”.
    - NEVER use water-based extinguishers on electronics – risk of explosion 💥
- **⛔ Emergency Power-Off Switch (EPO)**
    - Only press in *extreme* emergencies.
    - Sudden shutdown = possible data loss → safe shutdown is always better.

---

### 📢 Note 1-15 (Special Trivia!)

- The US national EAS can only be activated by the President – within 10 minutes in a national emergency.
- State/local versions are used for AMBER alerts, weather warnings, etc.

---

## 📒 Fail Open vs Fail Close & SDS

🧠 *Flashback*: First time I heard "fail open/close", I thought it was about doors… now I know it’s all about **risk management**.

### 🔓 Fail-Open (Fail-Safe)

- If the system fails, it **allows access**.
- Goal: **Human safety > Data security**
- Examples:
    - In a fire, building doors unlock so people can escape.
    - Firefighters can enter without barriers.
    - Some risk (unauthorized entry), but lives come first.

### 🔒 Fail-Close (Fail-Secure)

- If the system fails, it **denies access**.
- Goal: **Security > Convenience**
- Examples:
    - Data center doors stay locked even if power is out.
    - Manual override keys available (e.g., for firefighters).
    - Firewalls for sensitive data also fail-close – access is cut off until restored.

🧠 *Note to self*:

- Public web servers may use fail-open (for access continuity)
- Financial records = always fail-close
- Failure behavior depends on the **criticality of the system**.

---

### ⚡️ Bonus: Electrical Circuit Analogy

- “Open” = broken circuit = no power = safety
- Circuit breaker trips = like a fail-close to protect from overload.

---

### 📝 Summary Table:

| Situation | Fail Open Example | Fail Close Example |
| --- | --- | --- |
| Fire in building | Exit doors unlock | Data center stays locked |
| Firewall (public site) | Traffic allowed | N/A |
| Firewall (sensitive DB) | N/A | All access denied |
| Electric lock + power outage | Door unlocks | Door stays locked |

---

## 🧴 SDS – Safety Data Sheet (formerly MSDS)

🧠 *Flashback*: Cleaning solution got in my eye once – checked the SDS and realized how important it is!

### 📌 What is SDS?

- Comes with or available for every chemical product.
- Tells you:
    - Chemical properties
    - Safe usage instructions
    - First aid steps in case of contact
    - Disposal guidelines
    - Firefighting info
    - Handling and storage rules

⚠️ Cleaning chemicals (like those for tapes/discs):

- Can be flammable or toxic
- Always use gloves + goggles
- Read the SDS before use, or download from the manufacturer’s site (e.g., ehs.com)

📢 **Future-Me Reminder**:

- Ignoring SDS in labs or server rooms = big mistake
- “Don’t use anything you wouldn’t want on your skin, eyes, or lungs!”

✅ **Quick SDS Checklist**:

- 🔍 SDS file easy to find?
- 👓 Wearing PPE (gloves, goggles)?
- 🧯 Know fail-open/fail-close behavior?
- 🔌 Understand access rules during power failure?

---

## 📒 Safety Precautions

🧠 *Flashback*: I worked inside a rack *without* turning off the power… realized then that PPE and lockout aren’t just formalities — they’re survival rules!

### 👷‍♂️ OSHA – The Boss of Workplace Safety

- Website: [osha.gov](https://osha.gov/)
- Federal agency ensuring workplace health & safety
- Working near electrical panels/devices? First:
    - Turn **OFF** the device
    - Apply a **lockout** so no one accidentally turns it back ON

---

### 🔌 Electrical/Power Tools – Golden Rules

✅ **Always wear PPE**

- 👓 Eye protection if there’s dust/fumes
- 🧤 Gloves for grip/safety
- 👂 Hearing protection for noisy environments

✅ **Check your tools before use**

- Look for cracks, frayed wires, loose parts
- Damaged tool = unsafe → don’t use

✅ **Use the right tool for the right job**

- Follow the instruction manual
- Don’t use tools you haven’t been trained on
- Unauthorized use = unsafe use

✅ **Trip Hazards = Silent Killers**

- Don’t leave cords scattered on the floor
- Never leave tools unattended
- Keep walking paths clear – your feet shouldn't trip on anything!

🧾 **Checklist Recap**:

- ⚡️ Power lockout done before working? ✅
- 🧤 PPE worn? ✅
- 🧰 Tools in good condition? ✅
- 📚 Instructions followed? ✅
- 🚫 Trip hazards removed? ✅

---

## 🧍‍♂️ Lifting Heavy Objects — “Back pain ≠ badge of honor”

### 📦 Safe Lifting Rules (or your back will pay!):

- ✅ Plan your lift – know which side is best
- ✅ Stand close, feet shoulder-width apart
- ✅ Keep your back straight, bend knees, grab the load
- ✅ Lift using legs, arms, shoulders — NOT your back!
- ✅ Keep the load close to your body, avoid twisting
- ✅ Bend knees again when placing it down, back stays straight
- 🆘 Feels too heavy? Ask for help. Don’t be a hero.

🛒 **Pro tip**: Use a cart. Printers are meant to roll, not lift!

---

### ⚡️ Protecting Against Static Electricity — “Fried the motherboard… didn’t even feel it 😱”

💥 What’s the deal with static?

- Static = built-up charge
- Just **10 volts** can cause damage (you don’t even feel it!)
- A human touch can release **up to 1,500 volts**

🔥 2 types of damage:

- **Catastrophic**: Entire component dies
- **Upset failure**: Silent errors, reduced lifespan

🛑 To avoid ESD:

- 👨‍🔧 Wear an ESD strap (clip to chassis, strap to wrist)
- ⚠️ No strap? Frequently touch the case’s metal part
- 📦 Store components **inside** antistatic bags (not just on top)
- 🔌 Always shut down + unplug the computer before opening
- 🧩 Know which parts are field-replaceable vs. need to be sent back

📎 *Note*: Grounding = the third prong on a plug → routes to earth = safe path for stray current.

![image.png](images/2025-05-11-Safety-Procedures-and-Policies/image%201.png)