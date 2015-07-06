# flux-capacitor
Without using any costly database, this solution complements AWS Kinesis with the following capabilities:
* Long-term archival of records.
* Making current streams and their archives accessible to SQL-based exploration and analysis.
* Replay of archived records:
  * which supports key-value compaction so only the last record for a key is replayed.
  * which supports bounded replay (one needn’t replay the full archive).
  * which supports filtered replay (only replay records matching some criteria).
  * which supports annotating records as they are replayed in order to alter consumer behavior, such as to force overwrite.
  * which, when combined with consumers that cooperate in this endeavor, provides some definition of eventual consistency with respect to records that arrive on a stream concurrently with a replay operation, without requiring this solution to mediate the flow of the stream.

## Assumptions and Applicability Constraints
* This is mostly an integration project, light on actual software.
* This will probably be more of an ephemeral tool than a service, but the archival portion will have to run at least daily in order to not miss any records.
* The initial implementation might only support JSON records, but further contributions should be able to remove that as a requirement.
* The initial implementation might only support a single Kinesis stream, but further contributions should be able to remove that as a requirement.
* Data security is left to the user.

## Technical Goals
* Configure and launch a process (TBD, there are many options) to archive blocks of AWS Kinesis records to AWS S3 before they expire, possibly via EMRFS.
* Launch an AWS EMR cluster including the Hive application.
* Deploy Apache Drill to the cluster.
* Configure Apache Drill to read archived records from AWS S3, possibly via EMRFS.
* Configure AWS EMR Hive to expose an AWS Kinesis stream as an externally-stored table.
* Configure the AWS EMR Hive Metastore for consumption by Apache Drill.
* Configure Apache Drill to read from AWS Kinesis via AWS EMR Hive.
* To the greatest extent possible without storing another copy of the data, provide a unified and de-duplicated view spanning current and archived AWS Kinesis records.
* (TBD) Provide a basic UI or API to initiate search and replay operations, and monitor progress.
