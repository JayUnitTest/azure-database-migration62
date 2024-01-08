# Azure Database Migration 

This project is dedicated to the implementation of a robust, cloud-based database system utilizing Microsoft Azure, showcasing expertise in cloud engineering.

## Environment Setup

### Prerequisites:
- Microsoft Azure account
- Azure virtual machine (VM)
- SQL Server and SQL Server Management Studio (SSMS)
- Microsoft Remote Desktop
- Database file (.bak file) for restoration (i.e., AdventureWorks by Microsoft)

To establish the foundation for the cloud-based database system on Azure, we need to provision a Windows virtual machine, serving as the cornerstone of the production environment. We will then install SQL Server and SQL Server Management Studio on the Azure VM. 

1. **Login to Azure Account:**
    - Access your Azure account to begin the setup process.

2. **Create an Azure Virtual Machine (Placed into a Resource Group):**
    - Provision an Azure VM.
    - Configure the VM with the necessary credentials and firewall rules.
    - Connect to the VM by downloading the Remote Desktop Protocol (RDP) file and importing it into Microsoft Remote Desktop.

3. **Install SQL Server on the Azure Virtual Machine:**
    - Install SQL Server on the provisioned Azure VM.

4. **Install SQL Server Management Studio on the Azure Virtual Machine:**
    - Set up SQL Server Management Studio on the Azure VM for effective database management.

5. **Create Database:**
    - Utilize SSMS to create the production database on the VM.
    - Restore the database using a backup file, ensuring the data integrity and consistency.

## Migrating to Azure SQL Database

Transitioning database to Azure's cloud ecosystem

1. **Create Azure SQL Database:**
    - Create an Azure SQL Database through the Azure portal. This will serve as the target for migrating your on-premise database.
    - Use SQL login for the associated SQL Server as the chosen authentication method.
    - Ensure to allow the VM's IP address in firewall rules to prevent issues.

2. **Azure Data Studio:**
    - Install Azure Data Studio on the VM. This tool will be used to establish a connection to the existing on-premise database.

3. **Connect to Azure SQL Database:**
    - Through Azure Data Studio, establish a connection to the newly created Azure SQL Database. This connection acts as a conduit for the schema and data migration between the two databases.
    - Enable Trust Server Certificate. This will allow Azure Data Studio to establish a secure connection with the server, and any data sent or received during your interactions with the database remains encrypted and protected from unauthorized access.

4. **Schema Migration:**
    - Schema of the local database will be migrated to the azure-hosted database.
    - Install SQL Server Schema Compare extension. This simplifies the comparison and synchronization of the database schemas, allowing for seamless database migration. 
    - Once installed, right-click the local database and choose Schema Compare to create a new migration project. 
    - Within the schema compared dialog, configure the source connection to your local SQL Server database and the target connection to your Azure SQL Database. 
    - Compare and migrate the schema. 
    - Refresh Table nodes to ensure successful migration of the schema. 
    
    We have only migrated the schema of the database so far and thus the tables themselves will contain no data at this point.

5. **Data Migration:**
    - Install Azure SQL Migration extension. This is a powerful tool designed to streamline and simplify the migration of data between these environments. 
    - right-click on the server, select manage and under general Azure SQL Migration is available.
    - Click Migrate to Azure SQL and follow the Wizard to assess if the database can be migrated to Azure. 
    - Step 4 of the wizard will require you to download integration runtime in order to self-host a integration runtime node. Use the either of the keys provided to register your integration runtime and click refresh to check the connection between Azure Database Migration Service and integration runtime. 
    - Ensure to select all the tables to migrate from source to target. 
    - Run Validation to validate the migration settings before continuing. 
    - Start migration. 

5. **Validate Migration Success:**
    - Once completed, to validate the success of the database migration process, carry out comprehensive validation and thoroughly inspect the data, schema and any configurations of the migrated database. This ensure the migration adheres to principles of data integrity. 
    - e.g. compare Top 1000 results between tables 

## Data Backup and Restore

Provisioning a development environment alongside production. 

1. **On-premise database backup:**
    - Create a full backup of the production database on the production VM. 
    - Store backup file in a safe location 
    - Configure Azure Blob Storage account 

2. **Upload Backup to Blob Storage:**

    - Upload the database backup file to the blob storage container. 
    - This step provides an extra layer of backup protection through the presence of a redundant copy stored remotely. 

3. **Restore Database on Development environment:**
    - Provision a new Windows VM that mirrors the previous setup 
    - install SQL Server on this VM to mimic the necessary database infrastructure 
    - Restore the database backup onto the new environment. This allows for you to safely explore and experiment with new concepts, while main production data remains unaffected. 

4. **Automate backups for development database:**
    - Utilize SSMS to establish a management task that automates regular backups of the development database. 
    - Configure a weekly backup schedule for consistent protection.

## Disaster Recovery Simulation

Simulating data loss in the production environment to perform a disaster recovery simulation:

1. **Mimic Data Loss in Production Environment:**
    - Remove critical data from the production database to replicate a scenario where data integrity is compromised.
    - Confirm data loss through Azure Data Studio.

2. **Restore Database from Azure SQL Database Backup:**
    - Through the Azure portal, restore the production database to a point before the simulated data loss.
    - Append '-restored' to the new database for differentiation.
    - Deploy the newly restored database.

3. **Validation:**
    - Inspect the restored database through Azure Data Studio.
    - Confirm the restoration of affected data.
    - The restored database becomes the new production database.
    - Delete the database that suffered data loss in the Azure portal.
