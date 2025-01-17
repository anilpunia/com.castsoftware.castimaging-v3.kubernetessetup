# Instructions to deploy Imaging V3.1.1 on EKS with RDS

RDS as the database is an option instead of using the Postgres EKS pod. Follow these instructions to update the helm charts to use RDS.

## Prerequisites
1. Postgres 15
Use Postgres 15. This has been tested with v.15.7-R3 and that is recommeded version at this time.
2. RDS *must* be configured with self-managed credentials.
3. Setup **master** username as *postgres* and setup a password (be sure to note the password down, as it will be needed later).
4. SSL connections are not supported at this time. Follow these instructions to turn it off:
  * Create a Parameter Group named, say, *custom-pg15* and apply this setting:
    rds.force_ssl = 0
  * Once the instance has been created, apply this (*custom-pg15*) Parameter Group to it and reboot the RDS instance.

## Additional database users and settings:
Before deploying Imaging, connect to RDS with "postgres" superuser and execute below script:

```
CREATE USER operator WITH PASSWORD 'CastAIP';
GRANT rds_superuser TO operator;

```
