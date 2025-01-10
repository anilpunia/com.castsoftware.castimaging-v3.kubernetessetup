**RDS requirements:**

Use a Postgres 15 release (I have used 15.7-R3)
RDS must have been configured with "Self managed" credentials
master username = postgres 
Define a password for postgres user
Create a custom-pg15 Parameter Group in order to customize this parameter:
rds.force_ssl = 0
Once the RDS instance is created, apply the custom-pg15 Parameter Group to it and reboot it.

**Before deploying Imaging, connect to RDS with "postgres" superuser and execute below script:**

        CREATE USER operator WITH PASSWORD 'CastAIP';

    GRANT rds_superuser TO operator;
        CREATE USER guest WITH PASSWORD 'WelcomeToAIP';
        GRANT ALL PRIVILEGES ON DATABASE postgres TO operator;
        CREATE USER keycloak WITH PASSWORD 'keycloak';
        CREATE DATABASE keycloak;
        GRANT ALL PRIVILEGES ON DATABASE keycloak TO keycloak;

**Regarding network connectivity between the EKS Cluster and the RDS: **

If the RDS and the EKS cluster are placed in the same VPC, the EKS cluster will be able to communicate with the RDS with no further network configuration.

In the case where the RDS and the EKS cluster are placed in separate VPCs, advanced network configuration will be required to allow communication between the 2 respective VPCs. You will need to rely on customer's AWS administrator for setting this up as it implies security aspects and compliance to customer policies.

**Imaging Helm chart updates:**

You will need to make replacements in the yaml config files to reference the RDS hostname ant port number:

1) Globally replace (in all yaml file): "console-postgres.{{ .Release.Namespace }}.svc.cluster.local" with the RDS Endpoint (get it from AWS Console - see screenshot below)

2) In templates\console-ssoservice-deployment.yaml, replace KC_DB_PASSWORD variable value with the actual postgres password

            - name: KC_DB_PASSWORD

              value: replace-with-postgres-user-password

3) Globally replace (in all yaml file): "2285" with RDS port "5432"
