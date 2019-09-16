In this module you learned about the Query Store feature, introduced in SQL Server 2016. You learned how it offers data collection that enables you to monitor query performance continually. The Query Store can be enabled on any database on an instance of SQL Server 2016 or higher, whatever the database compatibility level. Furthermore, it's ideal for A/B testing where you monitor the effect of making one change, such as adding or altering an index.

You also learned about the cardinality estimator algorithm, and how this algorithm was rewritten in SQL Server 2014. As a result, when the compatibility level of a migrated database is moved, the new cardinality estimator is then used. This change in estimator might cause performance issues, which the Query Store, along with the Query Tuning Assistant (QTA), helps you resolve. You learned that the QTA finds regressing queries and experiments to find the best performance fix. You then use the fixes to create plan guides.

The combination of the Query Store and QTA is ideal for monitoring database performance after an upgrade. You learned that you should migrate a database to SQL Server 2017 and leave the compatibility level in place. Then you enable the Query Store to collect baseline data. When enough statistics have been gathered, the database compatibility can be changed. The Query Store then continues to gather data on the upgraded database, and the QTA compares the before and after to find and fix regressing queries.