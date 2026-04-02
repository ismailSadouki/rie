




This section sets out some simple steps which can be taken at property level to get started.


## Phase 1: Discussing the Basics Criteria
**Evaluate Current Practices** – Conduct an audit of energy, water, waste, and carbon footprint.
**Set Sustainability Goals** – Defining key focus areas

#### Research on the Criteria and Associated Best Practices, Approaches, and Resources


we should first Find out what the hotel is already doing and determine which issues are the most important and relevant to the hotel, and easiest to address. This includes ensuring that staff involved understand well the rationale for each criteria

## Phase 2: Data Collection & Measurement

#### Identifying and Quantifying Energy and Water Use
We should understand how to Obtain and record the energy and water consumption data. 
#### Identifying and Quantifying Waste and Carbon.
understanding the measurement and reduction of waste and carbon. 



## **Phase 3: Action Plan Development (Month 5-6)**

Select and prioritize sustainability actions that are most relevant and feasible for the hotel and plan out the implementation process, which should include clear plan for the project, and a review and follow up process for continuous improvement.
#### Monitor and Review Progress During Hotel Team Meetings.
Meet at least quarterly to monitor and discuss progress on each
criterion that is being addressed by the hotel

### Phase 4: Implementation & Guest Engagement

## Phase 5: Monitoring & Continuous Improvement

Obtain feedback on the project from stakeholders (e.g. survey staff, guests, and suppliers) 







This section sets out some simple steps that can be taken at the property level to get started.

## **Phase 1: Discussing the Basic Criteria**

### **Research on the Criteria and Associated Best Practices, Approaches, and Resources**

We should also find out what the hotel is already doing and determine which issues are the most important, relevant, and easiest to address. This includes ensuring that staff involved understand the rationale behind each criterion.

---

## **Phase 2: Data Collection**

### **Identifying and Quantifying Energy and Water Use**

We should understand how to obtain and record energy and water consumption data.

### **Identifying and Quantifying Waste and Carbon**

We should understand how to collect and analyze data on waste and carbon emissions.


Integrating data from multiple sources (IoT sensors, utility bills, manual logs).
---

## **Phase 3: Action Plan Development 

Select and prioritize sustainability actions that are most relevant and feasible for the hotel, and plan the implementation process. This should include a clear project plan, along with a review and follow-up process for continuous improvement.

### **also Monitoring and Reviewing Progress During Hotel Team Meetings**

we should Meet at least quarterly to monitor and discuss progress on each sustainability criterion being addressed by the hotel.

---

## **Phase 4: Implementation & Guest Engagement**
Implement sustainability measures and encourage guest participation.

---

## **Phase 5: Monitoring & Continuous Improvement**

Obtain feedback on the project from stakeholders (e.g., survey staff, guests, and suppliers).








About the Dahs developmnet We still couldn't find a good open-source platform that fits our requirements.
choosing between using open-source tools or   **custom solution** for Developing a dashboard comes with several challenges:
**Data Integration Issues** – Our Data may come from multiple sources (sensors, logs, databases) with different formats.
**Real-Time vs. Batch Processing** – Some metrics (e.g., energy consumption) require real-time updates, while others (e.g., waste reports) may be periodic.
✅ **User Experience & Accessibility** – The dashboard should be intuitive for non-technical users

At first, open-source tools offer ready-made dashboards,  quick deployment. But they also come with **limitations**—customization is restricted. and also they often lack simplicity. and simplicity is the ultimate sofistication -- dafinshi said

On the other hand, building a **custom solution** with **React for the front end and Flask/FastAPI for the back end** gives us **full control**. We can design the user experience, optimize performance, and ensure seamless integration with our data sources.and  also **Seamless AI/ML Integration** – Models can be built and integrated without limitations. However, this requires **more development time and effort**.

In the end, we had to weigh **speed vs. flexibility**. Should we prioritize a quick setup with open-source tools, or invest time in a fully customized solution that fits our exact needs?











Open-source platforms are designed for **data visualization and reporting**, not **AI-driven decision-making**.
difficult to deploy **custom ML models**open-source platforms **cannot efficiently run and serve ML models** without major modifications.

🔹 Open-source tools focus on **historical data analysis**, not **real-time AI-driven insights**.
**Self-hosting AI models** with these platforms introduces **data privacy risks**.
dashboard handling **sensitive data**, **custom-built security measures** are necessary, which open-source tools may lack.


**Full AI integration** – Direct control over machine learning models.  
✅ **Real-time insights** – Optimized for fast AI predictions.  
✅ **Flexible & scalable** – No platform restrictions.  
✅ **Better security** – Custom-built to protect sensitive data.





























### 2. **Cost Analysis**

- **Cost Estimates**: Add the ability to calculate the cost of each utility consumption (gas, electricity) based on rates (if available). You can provide a cost breakdown and analyze the cost over time or by campus.
    
- **Cost Prediction**: Use historical data to predict future costs of utilities using linear regression or machine learning models, and display them on the dashboard.
### 6. **Time Series Forecasting**

- **Energy Consumption Prediction**: Use machine learning models such as ARIMA or LSTM (Long Short-Term Memory) for predicting future consumption based on historical trends.
    
- **Trend Analysis**: Add trend lines to visualize long-term patterns in the consumption data, and calculate seasonal trends (e.g., higher consumption during winter months for heating).

- **Building Optimization**: Track the performance of individual buildings to suggest energy-saving solutions, such as adjusting heating or cooling in less frequently used areas.