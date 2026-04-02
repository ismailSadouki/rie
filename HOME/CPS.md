
# step 1: The dataset
### 📘 About SWaT

- Real water treatment testbed (6 stages, 51 sensors/actuators)
    
- Contains **7 days of normal operation** and **4 days of attack data**
    
- CSV files: `SWaT_Dataset_Normal_v1.csv` and `SWaT_Dataset_Attack_v0.csv`
    
- Each row = 1 second snapshot of all sensors + actuators + labels



## 🚰 What BATADAL Represents

**BATADAL** (Battle of the Attack Detection Algorithms) simulates a **water distribution system (WDS)** called _C-Town_, which has:

- Tanks
    
- Pumps
    
- Valves
    
- Flow and pressure sensors
    

You have **both physical (sensor)** and **control (actuator)** variables — ideal for CPSAD.

It was designed specifically for **attack detection research**, so it’s an _excellent_ dataset for you.

|Dataset|Duration|Contains Attacks?|Labels?|Usage|
|---|---|---|---|---|
|**Training Dataset 1**|1 year|❌ No attacks|✅ All normal|Train your models on normal data only|
|**Training Dataset 2**|~6 months|✅ Yes|⚠️ Partially labeled|Validation / early testing|
|**Test Dataset**|~3 months|✅ Yes|❌ No labels|For evaluating your detector (compare visually or via public evaluation rules)|
## 🧠 How They Fit Into a CPSAD Project

Here’s how we’ll use them step-by-step in your project:

1. **Training Dataset 1** →  
    Train your models (CPAD-style regression, LSTM autoencoder, etc.)  
    Only normal operations → ideal to learn normal physical relationships.
    
2. **Training Dataset 2** →  
    Validate your model and tune thresholds.  
    Use the labeled attacks to test your model’s sensitivity.
    
3. **Test Dataset** →  
    Final evaluation (optional).  
    Contains attacks without labels — useful for demonstrating generalization.

Let’s categorize your columns by **type** (physical sensors vs actuators):


|Category|Prefix|Meaning|Type|
|---|---|---|---|
|**Tank levels**|`L_T1 … L_T7`|Water levels in tanks|**Physical sensors**|
|**Pump flows**|`F_PU1 … F_PU11`|Flow rate through each pump|**Physical sensors**|
|**Pump status**|`S_PU1 … S_PU11`|ON/OFF (1 or 0)|**Actuators**|
|**Valve flows**|`F_V2`|Flow through valve 2|**Physical sensor**|
|**Valve status**|`S_V2`|Valve open/closed|**Actuator**|
|**Junction pressures**|`P_J280 … P_J422`|Pressure at various network nodes|**Physical sensors**|
|**Label**|`ATT_FLAG`|0 = normal, 1 = attack|**Ground truth (for validation)**|
|**Time**|`DATETIME`|Timestamp (hourly)|—|

## 🧩 Step 2 — Why This Dataset Works for CPSAD

You have **both actuator commands** (`S_PU`, `S_V2`)  
and **physical feedback** (`L_T`, `F_PU`, `P_J`).

That’s perfect for a **CPS attack detection** framework like CPSAD, because:

- CPSAD’s principle: learn the _expected_ physical behavior given actuator commands.
    
- If an attack manipulates sensors or actuators → predicted vs actual relationship breaks.
    

Example idea:

> Predict pressure `P_J280` using nearby pump statuses and flows (physical model).  
> During attack, the measured pressure won’t match the prediction → residual spike → alarm.

## 🧱 Step 3 — Data Structure

You’ll use these 3 conceptual groups for your model:

|Group|Variables|Example usage|
|---|---|---|
|Actuators|`S_PU*`, `S_V2`|model inputs|
|Physical|`L_T*`, `F_PU*`, `F_V2`, `P_J*`|model outputs (predicted)|
|Labels|`ATT_FLAG`|for validation|

