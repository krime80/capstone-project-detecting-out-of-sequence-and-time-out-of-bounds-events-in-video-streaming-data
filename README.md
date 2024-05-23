# Capstone Project: Detecting Out-of-Sequence and Out-of-Bounds Events in Video Streaming Data

## Author: Krista Rime

## Executive Summary
This project focuses on developing GRU models to monitor event sequences critical to the Total Minutes Viewed (TVM) ETL process. The models aim to detect and predict out-of-sequence events, significantly impacting partner and product reporting if left undetected. By leveraging these models, we can proactively identify potential issues in the ETL process and take corrective actions to ensure data integrity and accuracy.

## Rationale
In the video streaming industry, reporting accuracy and reliability are paramount for stakeholders, including content partners and product teams. Any disruptions or discrepancies in the event sequences during the ETL process can lead to inaccuracies in reporting, affecting business decisions and partnerships. Therefore, it is essential to implement robust monitoring mechanisms to promptly detect and rectify out-of-sequence events.

## Research Question
How can GRU models monitor event sequences in the client event emitting process and detect out-of-sequence events?

## Data Sources
The data used in this project consists of client behavioral event logs. The events enter the data pipeline and proceed through a TVM ETL. These logs contain information about events specific to the TVM calculation, timestamps, and other relevant attributes.

## Methodology
The methodology involves the following steps:

1. **Exploring the Data**: Understanding the structure and characteristics of the event log data, identifying key features, and gaining insights into the underlying patterns.
2. **Data Preprocessing**: Cleaning and preparing the event log data for model training, including handling missing values, encoding categorical variables, and scaling numerical features.
3. **Model Development**: Building GRU models to learn from the sequential nature of the event data and detect out-of-sequence events.
4. **Model Training**: Training the GRU models on historical event sequences to learn patterns and relationships between events.
5. **Model Evaluation**: Assessing the performance of the GRU models in detecting out-of-sequence events using appropriate evaluation metrics.

## Models

### GRU Model for Out-of-Sequence Watch Events
This model focuses on detecting anomalies in the order of events, such as clipStart and clipEnd, cmPodBegin and cmPodEnd. Ensuring these events come in order is crucial for maintaining accurate ETL processes and reporting.

### GRU Model for Out-of-Bounds Heartbeat Events
This model targets the detection of heartbeat events that occur outside the expected time bounds (19-21 seconds). Accurate timing of heartbeat events is essential for reliable reporting and partner payouts.

## Results

### Watch Events Out-of-Sequence GRU Model:
- **Test Loss**: 0.3351
- **Test Accuracy**: 0.8955

### Heartbeat Events Out-of-Bounds Timing GRU Model:
- **Test Loss**: 0.1149
- **Test Accuracy**: 1.0

The models demonstrated strong performance in detecting out-of-sequence and out-of-bounds events. However, the Heartbeat Events model is likely overfitted, as indicated by its perfect accuracy. Further model refinement is needed, including the integration of more data and the development of additional features.

## Key Findings

### Watch Events Out-of-Sequence GRU Model:
- **High Accuracy**: The model achieved a test accuracy of 0.8955, suggesting it effectively learned to detect out-of-sequence events.
- **Consistency in Event Intervals**: Events typically occurred at regular intervals, and deviations were effectively identified by the model.
- **Controlled Environment**: Most categorical features had limited unique values, indicating a controlled environment (e.g., a single app name "roku").
- **Summary Overview**:
  - **Total Count of Events Out of Sequence**: 150
  - **Percentage of Events Out of Sequence**: 11.21%
  - **Total Count of Events Out of Sequence by App**:
    - `roku`: 150
  - **Total Count of Events Out of Sequence by App Version**:
    - `5.33.5-d1bde25c896954a4820cb95376a0eed925b5a4d9`: 100
    - `5.32.3-f150a6d10`: 50
  - **Total Count of Session IDs with Out-of-Sequence Events**: 30
  - **Percentage of Session IDs with Out-of-Sequence Events**: 62.5%
  - **Total Count of Client IDs with Out-of-Sequence Events**: 20
  - **Percentage of Client IDs with Out-of-Sequence Events**: 50%

### Heartbeat Events Out-of-Bounds Timing GRU Model:
- **High Accuracy**: The model achieved a perfect test accuracy of 1.0, indicating it effectively detected out-of-bounds heartbeat events.
- **Overfitting**: The perfect accuracy suggests potential overfitting.
- **Need for More Data**: Further model refinement is necessary, including the integration of additional data such as device name, model, and other metadata associated with the events to develop more robust features.
- **Summary Overview**:
  - **Total Count of Events Out of Sequence**: 80
  - **Percentage of Events Out of Sequence**: 5.98%
  - **Total Count of Events Out of Sequence by App**:
    - `roku`: 80
  - **Total Count of Events Out of Sequence by App Version**:
    - `5.33.5-d1bde25c896954a4820cb95376a0eed925b5a4d9`: 50
    - `5.32.3-f150a6d10`: 30
  - **Total Count of Session IDs with Out-of-Sequence Events**: 20
  - **Percentage of Session IDs with Out-of-Sequence Events**: 40%
  - **Total Count of Client IDs with Out-of-Sequence Events**: 10
  - **Percentage of Client IDs with Out-of-Sequence Events**: 25%

### Impact on TVM Logic
The TVM logic calculates the total minutes viewed as the time between clipStart and clipEnd plus cmPodBegin and cmPodEnd events. In the event there is no "end" event in the last pair, the timestamp of the last heartbeat is used. Out-of-sequence events can disrupt this logic, leading to inaccuracies in reporting:
- **Watch Events**: Out-of-sequence watch events (clipStart, clipEnd, cmPodBegin, cmPodEnd) can cause incorrect TVM calculations, leading to inaccurate partner reporting and payouts and misguided product enhancements.
- **Heartbeat Events**: Out-of-bounds heartbeat events can also affect the TVM calculations by causing inaccuracies in the final time calculations when "end" events are missing.

#### Hypothetical Scenario
Consider a streaming service that partners with multiple content providers. The service calculates payouts based on the total minutes viewed (TVM) for each provider's content. If the event sequences are disrupted, the TVM calculations will be incorrect. 

For instance, suppose a partner's content has many out-of-sequence watch events:
- **Scenario**: The GRU model detects that 11.21% of events are out of sequence for a specific content provider.
- **Impact**: This disruption results in an underreporting of TVM by 15% due to missed or inaccurately recorded clipEnd and cmPodEnd events.
- **Result**: The provider receives 15% less payout than deserved, leading to potential disputes and loss of trust.

Similarly, for heartbeat events:
- **Scenario**: The GRU model identifies that 5.98% of heartbeat events are out of bounds.
- **Impact**: These anomalies result in overreporting TVM by 10% due to inaccurately using the last heartbeat as the end event.
- **Result**: The provider receives 10% more payout than warranted, leading to financial discrepancies and potential legal issues.

## Next Steps
- **Feature Engineering**: Bring in more metadata such as device name, model, and other relevant attributes to make the models more accurate.
- **Productionalize Models**: Implement the models into a real-time monitoring system to start the monitoring process.
- **Develop More Monitoring Models**: Include monitoring for additional sequences such as app launch, channel change, and transport functionality.

## Conclusion
The models developed in this project effectively identify out-of-sequence and out-of-bounds events, providing valuable insights into event regularity and anomalies. Future work includes refining features, conducting rigorous validation, and implementing real-time monitoring systems.

## Repository Structure
- `data-discovery.ipynb`: Jupyter notebook for data inspection and preprocessing.
- `heart-beat-events-out-of-time-bounds.ipynb`: Jupyter notebook for the GRU model focusing on heartbeat events.
- `watch-events-out-of-event-squece-gru.ipynb`: Jupyter notebook for the GRU model focusing on general out-of-sequence events.
- `capstone_project.csv`: Event data file.

## Instructions
1. Clone the repository.
2. Ensure all necessary libraries are installed.
3. Run the notebooks sequentially.

## Contact and Further Information
For inquiries or additional information, please contact kristarime@gmail.com
