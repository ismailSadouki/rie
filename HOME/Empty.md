

# 🏢 AI-Powered Sustainability Dashboard

This project is a **modular, AI-enhanced dashboard built with Streamlit** for tracking, analyzing, and optimizing sustainability metrics across buildings.

It integrates multiple data sources—**energy, gas, and water consumption**—and uses **machine learning, anomaly detection, and policy simulations** to help organizations:

- Reduce resource consumption 💧🔌🔥  
- Detect inefficiencies and anomalies 🔍  
- Simulate future policies 📊  
- Forecast demand and sustainability impact 📈

Designed for **smart building management**, environmental teams, and decision-makers aiming to integrate AI into their sustainability efforts.

---

### 🧩 Key Modules

This dashboard is divided into multiple intelligent pages, each serving a specific goal:

1. **📉 Consumption Forecast** – Predict future resource consumption  
2. **🔧 Consumption Optimization** – Suggest usage improvements  
3. **⚠️ Anomaly Detection** – Detect unusual usage patterns  
4. **📜 Policy Simulation** – Model policy scenarios  
5. **🌍 Sustainability Impact** – Visualize environmental KPIs  
6. **🔌 Energy**, **🔥 Gas**, and **💧 Water Modules** – Explore consumption details

Each module is built for clarity, interactivity, and action-oriented insights.

---

### 📁 Data Used

The dashboard works with datasets in both `.csv` and `.parquet` formats:

- `building_consumption.csv/parquet/zip`  
- `gas_consumption.csv/parquet/zip`  
- `water_consumption.csv/parquet/zip`

You can also upload your own datasets for real-time analysis.

---

Now that the overview is in place, let’s walk through the pages one by one to generate their content.

Which one do you want to start with?

- `Consumption_Forcast.py`  
- `Consumption_optimazation.py`  
- `Anomalies_Detection.py`  
- `Policy_simulation.py`  
- `Sustainability_Impact.py`  
- `energy_consumption.py`, `gas_consumption.py`, or `water_consumption.py`?

Just name the one to start with and I’ll write a solid explanation + visual placeholder + how it fits into the full dashboard.


---

## 📊 Consumption Forecast Module

This module enables intelligent **time series forecasting** of resource consumption (electricity, water, or gas) using advanced statistical and machine learning models.

### 🔧 Features

- **SARIMAX Forecasting**:  
  Forecasts future consumption using **Seasonal AutoRegressive Integrated Moving Average with eXogenous factors (SARIMAX)**. Suitable for data with seasonality (e.g., daily or monthly cycles).

- **🚨 Anomaly Detection**:  
  Automatically flags anomalous points in the consumption history using residual analysis and confidence interval thresholds.

- **📂 Dynamic Model Selection**:  
  Easily switch between forecasting models using the Streamlit sidebar:
  - SARIMAX
  - ARIMA
  - Prophet (optional future addition)
  - Linear Regression (as a baseline)
  - Exponential Smoothing (if included)

### 📸 Screenshot

![Consumption Forecast Screenshot](assets/forecast_screenshot.png) <!-- Replace with real screenshot -->


![](https://i.imgur.com/5GDj9xo.png)
![](https://i.imgur.com/VzWAX0J.png)
![](https://i.imgur.com/WTrbGAS.png)


--- 
## 🛢️ Gas Consumption Optimization

Optimize gas usage with four intelligent strategies:

- 💡 **Energy Savings** – Detect anomalies via Isolation Forest to reduce waste  
- 💰 **Cost Reduction** – Estimate financial impact of irregular consumption  
- ⏰ **Peak Consumption** – Identify top usage hours to rebalance load  
- 🧠 **Smart Optimization** – Apply linear programming to minimize peak-hour use

Also includes:
- 📈 **Forecasting** – ARIMA-based prediction of future gas demand
- 📊 Interactive visuals and controls for anomaly sensitivity & model tuning

![](https://i.imgur.com/qn6ZrEg.png)
![](https://i.imgur.com/9wtUtkz.png)
![](https://i.imgur.com/S305FMC.png)


---
## 🔌 Energy Consumption

Track and analyze electricity usage across buildings:

- 📊 **30-Day Forecast** – Predict upcoming utility consumption  
- 🏢 **Building-Level Analysis**:
  - Daily total consumption
  - Total by building
  - Monthly trends
  - Avg hourly usage
  - Last 7 days overview
and much more.
Interactive charts help monitor and compare energy patterns.


![](https://i.imgur.com/rLQ4Ahj.png)
![](https://i.imgur.com/32ZZYxm.png)
![](https://i.imgur.com/mBrgRHm.png)

---

## 🔥 Gas Consumption

Analyze gas usage patterns with detailed breakdowns:

- 📈 Daily total consumption  
- 🏢 Total consumption by building  
- 📆 Monthly trends  
- 🕓 Hourly usage patterns  
- ⏰ Peak vs Off-Peak comparison with summary stats

Designed for spotting inefficiencies and usage spikes.

---
## 🌍 Sustainability Impact

Simulate and visualize the environmental impact of policy changes:

- 📊 **Benchmarking**: Efficiency score and average consumption by campus  
- 🌿 **CO₂ Emissions Estimation**: Real vs. policy-adjusted emissions  
- 📉 **Policy Impact Visualization**: Quantifies CO₂ saved by:
  - Heating reduction
  - Water efficiency
  - Solar panels
  - Reduced building hours  
- 🎲 **Monte Carlo Simulation**:
  - Projects total CO₂ emissions over 10–50 years
  - Adjustable policy strength, variability, and emission factors


![](https://i.imgur.com/n1CSvIk.png)
![](https://i.imgur.com/VBQ3doq.png)
![](https://i.imgur.com/mbLs8rX.png)
![](https://i.imgur.com/bkgcKnm.png)




![](https://i.imgur.com/FguhgQl.png)
![](https://i.imgur.com/5LBuIhu.png)
![](https://i.imgur.com/TtyRVNi.png)
![](https://i.imgur.com/QTWzmXN.png)
![](https://i.imgur.com/8pZ2kZD.png)



Thanks a lot for the advice!

I actually built an end-to-end project recently, a full ML dashboard with Streamlit. where I handled data processing, model training, some basic optimization, and deployed it with Docker:  
🔗 [https://github.com/ismailSadouki/ESHAR_DASH](https://github.com/ismailSadouki/ESHAR_DASH)

Right now, I’m more focused on ML and deep learning concepts. I also have some interest in computer vision, but I haven’t decided yet where I want to specialize.

One last question! I saw you did your internship at LRIA, and I’m really interested in the research area. How did you get that opportunity? Was it through your university or is it open to students from other schools too, like mine (École Nationale Supérieure de Statistique)? It looks like a great place for research, and I’d love to try something similar.