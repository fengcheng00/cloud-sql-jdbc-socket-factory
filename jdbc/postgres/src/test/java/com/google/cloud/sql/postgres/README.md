## How to connect to GCP PostgreSQL
### Step 1: Login gcp
Open an command terminal and run  the command to login gcp
``
$ gcloud auth application-default login
``

### Step 2: Configure GOOGLE_APPLICATION_CREDENTIALS
After logged into gcp, a default application_default_credentials.json file will be created. Set the full path file
to GOOGLE_APPLICATION_CREDENTIALS variable.

Below is the example of the json file:
```json
{
  "client_id": "764086051850-6qr4p6gpi6hn506pt8ejuq83di341hur.apps.googleusercontent.com",
  "client_secret": "d-FL95Q19q7MQmFpd7hHD0Ty",
  "quota_project_id": "usa-cisionone-dev-001",
  "refresh_token": "1//04Jfmx8VK3KA5CgYIARAAGAQSNwF-L9Irf1SI53y-5e8i-hlqPgDHvvt8Wtc6zeMhQMYVtcZxc1zA9d3pb9tksq-cqDt2G0CjfJM",
  "type": "authorized_user"
}
```

``
$ export GOOGLE_APPLICATION_CREDENTIALS="/Users/feng.cheng/.config/gcloud/application_default_credentials.json"
``
it will be used inside the connector.

### Step 3: Configure the postgres connection properties in java code.
https://github.com/GoogleCloudPlatform/cloud-sql-jdbc-socket-factory/blob/main/docs/jdbc-postgres.md#iam-authentication

```java
// Set up URL parameters
    String jdbcURL = String.format("jdbc:postgresql:///%s", DB_NAME);
    Properties connProps = new Properties();
    connProps.setProperty("user", "postgres-iam-user@gmail.com");
    connProps.setProperty("password", "password");
    connProps.setProperty("sslmode", "disable");
    connProps.setProperty("socketFactory", "com.google.cloud.sql.postgres.SocketFactory");
    connProps.setProperty("cloudSqlInstance", "project:region:instance");
    connProps.setProperty("enableIamAuth", "true");

    // Initialize connection pool
    HikariConfig config = new HikariConfig();
    config.setJdbcUrl(jdbcURL);
    config.setDataSourceProperties(connProps);
    config.setConnectionTimeout(10000); // 10s

    HikariDataSource connectionPool = new HikariDataSource(config);

```

> **_NOTE:_** Step 1 provides OAuth2 client-credentials for java connector to authenticate the workflow.

> **_NOTE:_** We can try using service account to get the credential json file for step 2, need to verify.