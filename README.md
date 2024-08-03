# Maria_GCP

A secure Google Cloud SQL instance with MariaDB.

### **Google Cloud SQL with MariaDB Security Setup**

#### **1. Provisioning a Google Cloud SQL for MariaDB Instance**

**1.1 Create a Cloud SQL Instance:**
```bash
gcloud sql instances create my-mariadb-instance --database-version=MARIADB_10_6 --tier=db-f1-micro --region=us-central1
```

**1.2 Create a Database:**
```bash
gcloud sql databases create mydatabase --instance=my-mariadb-instance
```

**1.3 Create a User:**
```bash
gcloud sql users create myuser --instance=my-mariadb-instance --host=% --password=MySecurePassword
```

#### **2. Security Configurations**

**2.1 Enable SSL/TLS Connections:**
- **Generate Client Certificates:**
  ```bash
  gcloud sql instances describe my-mariadb-instance
  ```

- **Download SSL Certificates:**
  Download the client certificates from the Google Cloud Console and configure your MariaDB client to use them.

- **Enforce SSL/TLS in Connection String:**
  Ensure your connection string includes SSL/TLS options:
  ```plaintext
  ssl-ca=/path/to/ca-cert.pem
  ssl-cert=/path/to/client-cert.pem
  ssl-key=/path/to/client-key.pem
  ```

**2.2 Set Up Authorized Networks:**
- **Add an Authorized Network:**
  ```bash
  gcloud sql instances patch my-mariadb-instance --authorized-networks=192.168.1.0/24
  ```
  Replace `192.168.1.0/24` with the IP range that should have access.

**2.3 Configure IAM Database Authentication:**
- **Assign IAM Roles:**
  ```bash
  gcloud projects add-iam-policy-binding my-project-id --member="user:example@example.com" --role="roles/cloudsql.client"
  ```

**2.4 Enable Cloud SQL Insights:**
- **Enable Query Insights:**
  ```bash
  gcloud sql instances patch my-mariadb-instance --database-flags=log_min_duration_statement=0
  ```

**2.5 Enable Automatic Backups:**
- **Configure Backups:**
  ```bash
  gcloud sql instances patch my-mariadb-instance --backup-start-time=03:00
  ```

**2.6 Use VPC Service Controls:**
- **Create and Configure VPC Service Controls:**
  ```bash
  gcloud services vpc-access connectors create my-connector --region us-central1 --network default --range 10.8.0.0/28
  ```

**2.7 Use Cloud KMS for Encryption:**
- **Create a Key Ring and Key:**
  ```bash
  gcloud kms keyrings create my-keyring --location global
  gcloud kms keys create my-key --location global --keyring my-keyring --purpose encryption
  ```

- **Associate Key with Cloud SQL:**
  Configure Cloud SQL to use customer-managed encryption keys via Google Cloud Console.

**2.8 Implement SQL Injection Protection:**
- Ensure your application uses prepared statements and parameterized queries to protect against SQL injection attacks.

**2.9 Regularly Review and Update Access Permissions:**
- Periodically review IAM roles and user permissions to ensure only necessary access is granted.

**2.10 Configure Alerts and Monitoring:**
- **Set Up Monitoring Alerts:**
  ```bash
  gcloud monitoring alert-policies create --display-name "High CPU Usage Alert" --conditions '...'
  ```

- **Enable Logging:**
  Ensure that logs are configured to be stored in Google Cloud Logging for audit and monitoring purposes.

### **Summary**

This setup guide for Google Cloud SQL with MariaDB covers the following:

- **Provisioning:** Creating a MariaDB instance and database, and setting up a user.
- **Security Configurations:**
  - **SSL/TLS Connections:** Enforcing encrypted connections.
  - **Authorized Networks:** Restricting IP ranges that can access the instance.
  - **IAM Authentication:** Managing access with IAM roles.
  - **Cloud SQL Insights:** Enabling performance monitoring.
  - **Automatic Backups:** Configuring regular backups.
  - **VPC Service Controls:** Securing access within a VPC.
  - **Cloud KMS:** Managing encryption keys.
  - **SQL Injection Protection:** Using secure coding practices.
  - **Access Permissions:** Regular reviews and updates.
  - **Alerts and Monitoring:** Setting up alerts and logging.

Implementing these measures ensures that your Google Cloud SQL instance with MariaDB is secure, compliant, and resilient to various security threats. Regular updates and reviews of security configurations are essential for maintaining a strong security posture.
