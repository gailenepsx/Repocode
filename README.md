The simulator shall continue to execute the current detection process for FTF, TSD, and EFT without changing the underlying detection rules, thresholds, or hit-generation logic. �
The simulator shall group daily hits belonging to the same calendar month into a single monthly alert based on the relevant alert_date. �
The existing ALERT_ID field in the output files shall be reused to represent the monthly alert group. �
Multiple transactions identified within the same month for the same alert group shall therefore share the same ALERT_ID. �
The simulator shall create a new field named month in the output alert files to identify the month associated with the relevant alert. �
The simulator shall retain the previous daily alert identifier in a separate field named daily_alert_id for traceability, tracking, and debugging purposes. �
The simulator shall apply this new monthly grouping logic consistently for the impacted models FTF, TSD, and EFT only. �
The simulator shall ensure that the output remains readable and traceable by preserving the link between the monthly alert group and the underlying daily alert occurrences through daily_alert_id.
