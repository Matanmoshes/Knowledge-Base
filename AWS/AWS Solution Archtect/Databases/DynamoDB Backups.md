### Summary of DynamoDB Backups

### On-Demand Backup and Restore

- **Full Backups Anytime**: You can perform full backups of your DynamoDB tables at any time.
- **Zero Performance Impact**: Backups do not impact table performance or availability.
- **Consistency**: Backups are consistent within seconds.
- **Retention**: Backups are retained until explicitly deleted.
- **Regional Storage**: Backups are stored in the same region as the source tables.

### Point-in-Time Recovery (PITR)

- **Protection Against Accidental Writes/Deletes**: PITR protects your tables from accidental operations.
- **Restore Window**: You can restore your table to any point within the last 35 days.
- **Incremental Backups**: PITR uses incremental backups.
- **Not Enabled by Default**: You must enable PITR manually.
- **Latest Restorable Time**: The latest restorable time is 5 minutes in the past from the current time.

### Exam Tips

- **On-Demand Backups**: Know that these can be taken anytime with zero impact on performance and are stored in the same region as the source table.
- **PITR**:
    - Must be manually enabled.
    - Allows restoration to any point in the last 35 days.
    - Uses incremental backups.
    - The latest restorable time is 5 minutes in the past.
- **Use Cases**: Understand that on-demand backups are for manual, full backups while PITR is for automated, incremental backups to protect against accidental data loss.

By understanding these key points about DynamoDB backups and recovery options, you will be well-prepared for related questions in the AWS Solutions Architect Associate exam.