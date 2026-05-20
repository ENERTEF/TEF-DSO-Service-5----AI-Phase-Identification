Phase Identification Service — Technical Manual & Service Specification v1.0
Author: RWTH Aachen University
Version: 1.0
Last updated: 06 Mar 2026
________________________________________
1. Business Context & Definitions
This service focuses on the development and operation of a phase identification analytics service for low-voltage electricity distribution networks. The service determines the electrical phase (A, B, or C) to which each smart meter or consumer connection is physically connected. Accurate phase connectivity information enables improved monitoring, operational planning, and load balancing within the electricity distribution network.
Distribution networks frequently lack accurate phase connectivity records due to infrastructure upgrades, undocumented maintenance activities, or historical inconsistencies in asset documentation. As a result, Distribution System Operators (DSOs) may have incomplete or outdated knowledge of the phase allocation of connected loads.
The Phase Identification Service addresses this problem by analysing time-series measurements from smart meters and network sensors. Using statistical correlation, clustering, and signal analysis techniques, the service identifies patterns consumption data that indicate shared phase connections among meters. Buildings or consumers exhibiting highly correlated voltage variations are likely connected to the same electrical phase.
________________________________________
Key Terms
•	Distribution Network:
The low-voltage electrical infrastructure that distributes electricity from distribution transformers to end users.
•	Phase Connectivity:
The electrical phase (A, B, or C) of a three-phase transformer to which a consumer or smart meter is connected.
•	Smart Meter:
A digital electricity meter capable of recording energy consumption and electrical measurements at regular time intervals.
•	Phase Identification:
The process of determining the phase connection of a meter or consumer using measurement data and analytical methods.
•	Phase Clustering:
A data-driven process grouping meters that exhibit similar voltage or consumption patterns, indicating a shared phase connection.
•	Phase Imbalance:
A condition where electrical loads are unevenly distributed across the three phases of a distribution system.
•	Network Observability:
The ability of a network operator to accurately monitor the operational state and configuration of the distribution network.
________________________________________
1.1 Distribution System Operator (Service User) Context
This service is intended for the Distribution System Operators (DSOs), which manages a portfolio of buildings, public infrastructure, and utility connections supplied through the low-voltage electricity distribution network.
A common operational challenge in distribution networks is the lack of reliable phase connectivity information for consumers and infrastructure assets. Changes in network topology, meter replacements, and undocumented field interventions may lead to inaccurate records of the phase to which a consumer is connected.
In practice, the DSOs reliable phase connectivity information to support infrastructure planning, energy management initiatives, and collaboration with electricity distribution operators.
The Phase Identification Service developed by RWTH Aachen University provides a data-driven approach to infer phase connectivity using available smart meter measurements and network metadata. The service analyses consumption measurements from multiple meters and identifies correlations that indicate common phase connections.
Cluster representatives or phase reference profiles are used as the expected signal patterns for each phase group, while deviations or inconsistencies are used to detect potential mislabelling of phase connections or network configuration issues.
The service boundary is clearly defined: the provider delivers phase assignments, correlation metrics, and quality indicators through a specified API or data interface, with transparent analytical methodology and versioning of models and assumptions. The DSO supplies measurement data from smart meters and available network metadata such as transformer identifiers and connection information.
Operationally, the DSO benefits from regularly refreshed phase identification results (for example, monthly or quarterly), complemented by on-demand analyses when infrastructure changes occur. Each analytical output is timestamped and reproducible based on logged model versions and input datasets.
Performance tracking focuses on technical and operational indicators such as phase identification accuracy, stability of phase assignments over time, data coverage and latency, and operational impact metrics such as improved network observability and phase load balancing.
________________________________________
2. Problem Statement
The goal is to build and operate a production-ready phase identification analytics service that determines the phase connectivity of smart meters and buildings within a DSO’s electricity network.
The service estimates the most probable phase connection for each meter based on time-series measurement data and network metadata. Outputs include phase assignments, confidence scores, and aggregated phase statistics at the transformer or feeder level.
The service must expose a simple, authenticated REST API and remain available and responsive throughout the evaluation period.
Targets of the analysis include energy consumption data, and network metadata associated with each smart meter. Models may use organiser-provided telemetry and contextual information such as transformer identifiers or connection type. Only data available at or before the analysis execution time may be used.
Participants may apply clustering, correlation analysis, or other statistical learning methods to infer phase connectivity, provided strict reproducibility and versioning of models and datasets are ensured.
________________________________________
3. Data Description
3.1 Data Dictionary of Smart Meter Assets
The following table summarises the available features of the dataset describing smart meter assets.
Variable	Variable name	Type	Measurement unit	Description	Allowed values / Examples
meter identifier	meter_id	String	-	Unique identifier of the smart meter	MTR-001
building name	building_name	String	-	Name of the building or consumer associated with the meter	Town Hall
transformer identifier	transformer_id	String	-	Identifier of the supplying distribution transformer	TR-102
connection type	connection_type	String	-	Type of electrical connection	single-phase
installation date	installation_date	Date	YYYY-MM-DD	Date of meter installation	2019-06-01
recorded phase	recorded_phase	String	-	Phase recorded in the asset registry	A
________________________________________
3.2 Data Dictionary of Smart Meter Measurements
The following table summarises the available features of the dataset for smart meter measurement data.
Variable	Variable name	Type	Measurement unit	Description	Allowed values / Examples
meter identifier	meter_id	String	-	Identifier of the smart meter	MTR-001
measurement timestamp	timestamp	Timestamp	YYYY-MM-DD HH:MM	Time of measurement	2023-02-28 13:00
					
active energy consumption	active_energy	Float	kWh	Energy consumption during measurement interval	1.2
reactive energy consumption	reactive_energy	Float	kVArh	Reactive energy recorded during interval	0.2
________________________________________
3.3 Data Dictionary of Substation or Transformer Measurements
The following table summarises the available features of the dataset for transformer-level measurements.
Variable	Variable name	Type	Measurement unit	Description	Allowed values / Examples
transformer identifier	transformer_id	String	-	Distribution transformer identifier	TR-102
phase label	phase	String	-	Reference phase measurement	L1
timestamp	timestamp	Timestamp	YYYY-MM-DD HH:MM	Measurement time	2023-02-28 13:00
phase active energy	active_power	Float	kW	Active Power measured at transformer phase	100
Phase reactive energy	reactive_power	Float	kVAr	Reactive Power measured at transformer phase	50
________________________________________
4. Analytics, Scope & Update Frequency
•	Temporal scope: Analytics are computed over rolling historical windows (e.g. last one to three months) with updates triggered by new measurement data availability or detected network configuration changes.
•	Update frequency: Results are refreshed monthly, with on-demand recalculation following infrastructure modifications or meter installations.
•	Output format: For each meter or building, the service returns a structured set of indicators including:
1.	Inferred phase assignment (A, B, or C)
2.	Confidence score of the phase classification
3.	Correlation score with reference phase signals
4.	Phase load contribution indicator
5.	Data quality or observability indicator
________________________________________
5. Evaluation Protocols & Metrics
The purpose of the evaluation is to verify that the service operates reliably and delivers consistent and accurate phase identification results when applied to a DSO’s electricity infrastructure.
Evaluation focuses on methodological correctness, output quality, and operational performance during service provision.
________________________________________
5.1 Data Usage & Analytical Protocol
• The service shall compute analytics using a rolling historical data window of up to twelve (12) months ending at the time of execution.
• Only data available at the time of execution may be used.
• The analytical window may be adjusted for meters with shorter measurement histories, provided this adjustment is documented.
• All outputs shall be reproducible based on documented model versions, configuration parameters, and input datasets.
________________________________________
5.2 Data Gaps and Exceptions
•	Periods with missing or invalid measurements shall be excluded from analytical processing.
•	Meters with sustained data quality issues may be temporarily excluded from analytics, subject to notification and documentation.
________________________________________
5.3 Service Evaluation Metrics & KPIs
The service shall be evaluated using the following quantitative metrics:
•	Phase Identification Accuracy (PIA): Percentage of meters for which the inferred phase matches validated phase connectivity information.
•	Phase Assignment Stability (PAS): Degree to which phase assignments remain stable across consecutive analytical periods.
•	Phase Load Balance Indicator (PLBI): Measurement of the distribution of electrical loads across phases within each transformer.
________________________________________
6. Deliverables & Submissions
The selected provider shall deliver three (3) reports, aligned with the lifecycle of the service, together with the required technical specifications and deployment artefacts.
________________________________________
6.1 Deliverable Reports
Three delivery reports should be submitted for this service.
1. Pre-Service Deliverable – Service Design & Setup Report
Submitted prior to service start, this report shall describe the proposed analytical approach, phase identification methodology, data requirements, system architecture, security measures, and integration plan. It shall also define the operational schedule and procedures for service execution.
2. Intermediate Deliverable – Interim Performance & Operations Report
Submitted at an agreed midpoint of the service period, this report shall summarise service operation to date, data coverage, preliminary analytical results, and performance against the defined metrics and KPIs. Any issues, adaptations, or refinements to the methodology shall be documented.
3. Final Deliverable – Final Evaluation & Recommendations Report
Submitted at the end of the service period, this report shall present final performance results, identified inconsistencies in phase connectivity records, recommendations for network balancing or operational improvements, and lessons learned. The report shall also include guidance for future service continuation or scaling.
________________________________________
6.2 Technical Specifications & Submissions
Service Interface Documentation:
Full documentation of APIs, data formats, authentication procedures, and access controls.
Deployment Artefacts:
The provider shall specify the deployment approach. Where containerisation is used, a Dockerfile or equivalent container specification shall be delivered to support reproducible deployment.
Configuration, Versioning & Handover:
Documentation of configuration parameters, model and data versioning, and operational handover procedures.
Security & Data Protection Documentation:
Description of data handling procedures, access control mechanisms, and compliance with applicable data protection requirements.

