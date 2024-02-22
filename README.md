
This is the GitHub Repo created by Team Kata Loggers for the 2024 Architectural Katas challenge.

## Overview
StayHealthy, Inc. is a large and highly successful medical software company located in San Francisco, California, US. They currently have 2 popular cloud-based SAAS products.
StayHealthy, Inc. is now expanding into the medical monitoring market, and is in need of a new medical patient monitoring system for hospitals that monitors a patients vital signs using proprietary medical monitoring devices built by StayHealthy, Inc.
## Business Requirements
* MonitorMe reads data from eight different patient-monitoring equipment vital sign input sources: heart rate, blood pressure, oxygen level, blood sugar, respiration rate, electrocardiogram (ECG), body temperature, and sleep status (sleep or awake). It then sends the data to a consolidated monitoring screen (per nurses station) with an average response time of 1 second or less. The consolidated monitoring screen displays each patients vital signs, rotating between patients every 5 seconds. There is a maximum of 20 patients per nurses station.
* Vital sign data analyzed and recorded through MonitorMe must be as accurate as possible. 
* For each vital sign, MonitorMe must record and store the past 24 hours of all vital sign readings. A medical professional can review this history, filtering on time range as well as vital sign.
* In addition to recording raw monitoring data, the MonitorMe software must also analyze each patient’s vital signs and alert a medical professional if it detects an issue (e.g., decrease in oxygen level) or reaches a preset threshold (e.g., temperature has reached 104 degrees F).
* Some trend and threshold analysis is dependent on whether the patient is awake or asleep. For example, if the blood pressure drops, the system should notice that the patient is asleep and adjust its alerts accordingly. The same is true with the respiration rate and heart rate. For example, all of these vital signs are reduced when the patient is asleep, but if awake something might be wrong.
* Medical professionals receive alert push notifications of a potential problem based on raw data analysis to a StayHeathy mobile app on their smart phone as well as the consolidated monitoring screen in each nurses station.
* If any of vital sign device (or software) fails, MonitorMe must still function for other vital sign monitoring (monitor, record, analyze, and alert).
* Medical staff can generate holistic snapshots from a patients consolidated vital signs at any time. Medical staff can then upload the patient snapshot to MyMedicalData. The upload functionality is within the scope of the MonitorMe functionality and is done through a secure HTTP API call within MyMedicalData.
* Each patient monitoring device transmits vital sign readings at a different rate: Heart rate: every 500ms Blood pressure: every hour Oxygen level: every 5 seconds Blood sugar: every 2 minutes Respiration: every second ECG: every second Body temperature: every 5 minutes Sleep status: every 2 minutes
* MonitorMe will be deployed as an on-premises system. Each physical hospital location will have its own installation of the complete MonitorMe system (including the recorded raw monitoring data).
* Maximum number of patients per physical MonitorMe instance: 500
* StayHealthy. Inc. will be providing a comprehensive hardware and software for this system. The platform, data stores, databases, and other technical tools and products are unspecified at this time and will be based on your on-prem architectural solution.

## Architectural Characteristics
* **Availability** and **Reliability**- As this system works with critical metrics related to a patient health, it has to be accessibble and operational at all times, without any downtimes or interruptions. The system has to be designed with component redundancy. The system has to work accurately and consistently all the time and keep error rates as low as possible
* **Security** - Patient personal data has to be protected via encryption or access controll in all situations.
* ***Performance*** - MonitorMe has to have an average response time of 1 sec or less for sending the data to the monitoring screens.
* **Extensibility** - MonitorMe has to be designed in a way to be able to add new pieces into the software easily at any later stages - *StayHealthy, Inc. is looking towards adding more vital sign monitoring devices for MonitorMe in the future.*
## Other Considerations
* Make use of **Agile Software Development** methodologies - *As this is a new line of business for StayHealthy, they expect a lot of change as they learn more about this new market.* 

## ADRs
### ADR 1. On Premise Setup
* Status: Proposed
* Context: The application has to have a high availability and high performance.
* Decision: The complete MonitorMe infrastructure will be deployed separately into every hospital as a standalone on premise setup. This will ensure that the system will be independent from any external vendor or internet issues. The system will have a high level of responsivity and low response time, due to all components will be in the same local network.
* Consequences: This will result in a higher installation  and maintenance cost of the overall MonitorMe infrastructure..

### ADR 2. Use of a Time Series Database
* Status: Proposed
* Context: The system has to save 24 hours of monitoring data with their respective timestamps.
* Decision:Time Series Databases are optimized for efficiently storing and retrieving timestamped data. So they can easily organize the data chronologically and making timebased querying a lot faster and efficient compared to Relational databases. As only time based data needs to be stored, it will reduce maintenance complexity of the database system.
* Consequences:More complex inital setup than relational databases. 
### ADR 3. Message Broker and Data Collector
* Status: Proposed
* Context: MonitorMe has to collect analytical data from vital sign input sources
* Decision: A separate Message Broker and Data Collector component is needed inbetween MonitorMe and the Vital Sign Input Devices. Based on the type of the Vital Sign Inpit Device the data sending method can be PUSH or PULL type. The Message Broker and Data Collector has the ability to work with any kind of device interface and receive PUSH type of messages from them, or query an input source with a predefined time sequence then send this retrieved data towards MonitorMe via a Secure REST API.
* Consequences: Additional development and future maintenance cost, adds complexity to the system, data retrieval times has to be configured.
* ### ADR 4. Backup Instances
* Status: Proposed
* Context: MonitorMe still has to work if any of the components fails
* Decision: A high availability failsafe setup is needed because of the critical alerting functionality of the system. 2 instance of MonitorMe and Message Broker is needed in every setup. The backup instances are monitoring the primary instance via heartbeat messages and in case of any system failure automatically takes over the tasks of the primary instances and sends an alert to the IT Operation Team
* Consequences: Additional implementation and future operation cost, adds complexity to the system.

## Architecture Diagram
![architecture](https://github.com/krjuhasz/Kata-Loggers/blob/main/images/architecture2.jpg?raw=true)
