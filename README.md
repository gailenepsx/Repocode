The simulator shall support the monthly distribution of alerts for the impacted models FTF, TSD, and EFT only. �
The simulator shall preserve the current detection process and shall not modify the existing detection rules, thresholds, or hit-generation logic. �
The change shall apply only to the alert distribution layer, where daily hits detected within the same calendar month shall be grouped into one monthly alert. �
The month used for grouping shall be determined from the alert_date field. �
The existing ALERT_ID field shall be reused in the output files to represent the monthly alert group. �
ALERT_ID shall be considered a structural grouping key, as it defines how transactions are merged into the same alert. �
Although ALERT_ID shall not be a direct input to the scoring formula, it shall define the alert content submitted to the scoring process. �
As a result, multiple daily hits or transactions belonging to the same month may share the same ALERT_ID when they are part of the same monthly alert. �
The simulator shall add a new field named month to the impacted output files in order to identify the month associated with the alert. �
The simulator shall retain the former daily alert identifier in a separate field named daily_alert_id for traceability, reconciliation, and debugging purposes. �
The global consolidation step shall summarize and consolidate scores based on the monthly ALERT_ID. �
The monthly grouping logic and the new output fields shall be reflected consistently in the Alert files, Alert Notes files, Alert Steps files, and Audit Trail files. �
For countries not yet included in the monthly distribution scope, the simulator shall preserve the current daily flow and the existing daily ALERT_ID behavior. �
To manage this exception, the JSON parameter file shall include a country-level configuration to indicate whether monthly distribution is enabled or disabled for a given country. �
A clearer configuration name than Monthly Excluded Countries would be Monthly Distribution by Country or Monthly Distribution Settings, because it expresses the target behavior directly. �
The recommended configuration structure shall include a country code and a boolean flag such as monthly_distribution_enabled, where true applies the monthly flow and false preserves the current daily flow. �
Suggested wording
If you want a more formal version for direct insertion into the BRD, you can use this sentence before the list: the following functional requirements define the expected behavior of the simulator to support monthly alert distribution while preserving the current detection logic and allowing country-level exceptions.
