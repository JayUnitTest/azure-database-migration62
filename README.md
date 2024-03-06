# Azure Database Migration 

## Table of Contents 
- [Azure Database Migration](#azure-database-migration)
  - [Table of Contents](#table-of-contents)
  - [Project Description](#project-description)
    - [Project Architecture](#project-architecture)
  - [Environment Setup](#environment-setup)
    - [Prerequisites:](#prerequisites)
  - [Migrating to Azure SQL Database](#migrating-to-azure-sql-database)
  - [Data Backup and Restore](#data-backup-and-restore)
  - [Disaster Recovery Simulation](#disaster-recovery-simulation)
  - [Geo Replication and Failover](#geo-replication-and-failover)
  - [Failover and Tailback](#failover-and-tailback)
  - [Microsoft Entra Directory Integration](#microsoft-entra-directory-integration)
    - [What is Microsoft Entra?](#what-is-microsoft-entra)
    - [***Conclusion***](#conclusion)

## Project Description
This project is dedicated to architecting and implementing a robust, cloud-based database system on Microsoft Azure, showcasing expertise in cloud engineering. It encompasses the setup of a production environment, migration to Azure SQL Database, disaster recovery simulations, data backup and restoration, automated scheduling, geo-replication, failover configurations and the integration of Microsoft Entra ID. The comprehensive approach aims to demonstrate proficiency in cloud-based database management, ensuring data integrity, availability, and security throughout various scenarios and configurations on the Azure platform.

### Project Architecture

![project-diagram](<Azure Project Diagram.jpeg>)

## Environment Setup

### Prerequisites:
- Microsoft Azure account
- Azure virtual machine (VM)
- SQL Server and SQL Server Management Studio (SSMS)
- Microsoft Remote Desktop
- Database file (.bak file) for restoration (i.e., AdventureWorks by Microsoft)

_To establish the foundation for the cloud-based database system on Azure, we need to provision a Windows virtual machine, serving as the cornerstone of the production environment. We will then install SQL Server and SQL Server Management Studio on the Azure VM._

1. **Login to Azure Account:**
    - Access your Azure account to begin the setup process.

2. **Create an Azure Virtual Machine (Placed into a Resource Group):**
    - Provision an Azure VM (Windows) by navigating the Azure portal, select create a resource. Virtual Machine should appear under Popular Azure services. Click create to start the process of creating the VM.
    - Configure the VM with the necessary credentials and firewall rules.
    
    _The mandatory fields to complete here are:_

- Virtual machine name

- Region: Pick the region geographically closest to you

- Image: This represents the operating system of your virtual machine. To provision a Windows VM you will need to select a Windows image such as Windows 11 Pro.

- Size: Select the appropriate size of the virtual machine based on the resource requirements of your workload. To change the size you can click on See all sizes under the Size pane. Find the B2ms general purpose VM, a VM that is good for many workloads, and then press Select
  
![vm-creation](assets/create-vm-screenshot.png)

**Enabling RDP Access for Windows VMs**

For Windows VMs, Remote Desktop Protocol (RDP) is used to enable remote access. The Administrator username and password you were prompted to set up will be used for RDP access to the VM. To avoid any issues connecting to the VM later, ensure that the RDP port (port 3389) is allowed in the NSG inbound rules for the VM. In most cases, the default rule is automatically created, allowing traffic on port 3389. However, if it is missing or modified, you might need to add or adjust the rule to permit RDP traffic.

 - Connect to the VM by downloading the Remote Desktop Protocol (RDP) file and importing it into Microsoft Remote Desktop.

 To open the RDP connection using Microsoft Remote Desktop, simply drag and drop the .rdp file you downloaded from Azure in Microsoft Remote Desktop. This will add the machine to the PCs present in Microsoft Remote Desktop.

 To sign in you will have to use the username and password you have setup during the VM provisioning. Once you have successfully connected you will have to go through setting up your new Windows machine.

 ![rdp-download](assets/RDP-SCreenshot.png)

1. **Install SQL Server on the Azure Virtual Machine:**
    - Install SQL Server on the provisioned Azure VM.

![](assets/install-sql-server.png)

2. **Install SQL Server Management Studio on the Azure Virtual Machine:**
    - Set up SQL Server Management Studio on the Azure VM for effective database management. You should see this as an option at the end of installing SQL Server. Follow the link it takes you to and download & install the executable.

        ![](assets/install-ssms-screenshot.png)

3. **Create Database:**
- Utilize SSMS to create the production database on the VM.
    - We are greeted by this window on first open, click connect.
  ![](assets/smss-first-open.png)
    - Restore the database using a backup file, ensuring the data integrity and consistency. 
    > **_NOTE:_**  Restore will look in a specific MSSQL folder for backup .bak files. Move the backup to this folder.

  ![](assets/restore-db.png)

## Migrating to Azure SQL Database
_Transitioning database to Azure's cloud ecosystem_

As businesses increasingly embrace cloud computing to drive innovation and scalability, the migration of databases from on-premise environments to the cloud has become a strategic imperative. To achieve a seamless transition, a powerful and user-friendly database management tool is essential. Enter Azure Data Studio, a cutting-edge cross-platform solution developed by Microsoft to simplify database management and streamline the migration process.

1. **Create Azure SQL Database:**
    - Create an Azure SQL Server and Database through the Azure portal. This will serve as the target for migrating your on-premise database.
    - Use SQL login for the associated SQL Server as the chosen authentication method.
    - Ensure to allow the VM's IP address in firewall rules to prevent issues.

2. **Azure Data Studio:**
    - Install Azure Data Studio on the VM. This tool will be used to establish a connection to the existing on-premise database.

3. **Connect to Azure SQL Database:**
    - Through Azure Data Studio, establish a connection to the newly created Azure SQL Database. This connection acts as a conduit for the schema and data migration between the two databases.
    - Enable Trust Server Certificate. This will allow Azure Data Studio to establish a secure connection with the server, and any data sent or received during your interactions with the database remains encrypted and protected from unauthorized access.
  
   ![](assets/azure-data-studio-first-connect.png)

4. **Schema Migration:**
    - Schema of the local database will be migrated to the azure-hosted database.
    - Install SQL Server Schema Compare extension. This simplifies the comparison and synchronization of the database schemas, allowing for seamless database migration.
    ![](assets/schema-compare-install.png)   
    - Once installed, right-click the local database and choose Schema Compare to create a new migration project. 
    ![](assets/schema-compare.png)
    - Within the schema compared dialog, configure the source connection to your local SQL Server database and the target connection to your Azure SQL Database. 
    - Compare and migrate the schema. 
    - Refresh Table nodes to ensure successful migration of the schema. 
    ![](assets/schema-complete.png)
    > **_NOTE:_** We have **only migrated the schema** of the database so far and thus the tables themselves **will contain no data** at this point.

5. **Data Migration:**
    - Install Azure SQL Migration extension. This is a powerful tool designed to streamline and simplify the migration of data between these environments. 
    ![](assets/sql-migration-ext-install.png)
    - right-click on the server, select manage and under general Azure SQL Migration is available.
    - Click Migrate to Azure SQL and follow the Wizard to assess if the database can be migrated to Azure. 
    - step 1:
    ![step 1](assets/step-1-migrate.png)
    - step 2:
    ![step 2](assets/step-2-migrate.png)
    - step 3: 
    ![step 3](assets/step-3-migration.png)
    - Step 4 of the wizard will require you to download integration runtime in order to self-host a integration runtime node. Use the either of the keys provided to register your integration runtime and click refresh to check the connection between Azure Database Migration Service and integration runtime. 
    ![](assets/migrate-to-azure.png)
    ![](assets/migration-service-screen.png)
    - Ensure to select all the tables to migrate from source to target. 
    ![](assets/selecting-tables.png)
    - Run Validation to validate the migration settings before continuing. 
    ![](assets/validation-migration.png)
    - Start migration. 
    ![](assets/migration-inprocess.png)
    - Success!
    ![](assets/successfully-migrated.png)

6. **Validate Migration Success:**
    - Once completed, to validate the success of the database migration process, carry out comprehensive validation and thoroughly inspect the data, schema and any configurations of the migrated database. This ensure the migration adheres to principles of data integrity. 
    - e.g. compare Top 1000 results between tables 
    ![](assets/top1000-results.png)

## Data Backup and Restore

_Provisioning a development environment alongside production._

Companies often have two types of databases. One for storing real customer data (production database) and another for testing and experimenting (development database). By provisioning a development database, you'll be able to test and experiment without worrying about affecting your main production data. This separation is crucial for making changes and improvements while maintaining the integrity of your live data.

1. **On-premise database backup:**
    - Create a full backup of the production database on the production VM. 
      - beginning the process
        ![](assets/start-backup-process.png)
    - Store backup file in a safe location 
        ![](assets/backup-complete.png)
    - Configure Azure Blob Storage account 
        ![](assets/create-storage-acc.png)

2. **Upload Backup to Blob Storage:**

    - Upload the database backup file to the blob storage container. 
        ![](assets/create-container.png)
    - This step provides an extra layer of backup protection through the presence of a redundant copy stored remotely. 
        ![](assets/upload-to-blob.png) 

3. **Restore Database on Development environment:**
    - Provision a new Windows VM that mirrors the previous setup 
      - created the new db server and db
        ![](assets/creating-dev-dbs.png)  
    - install SQL Server on this VM to mimic the necessary database infrastructure 
    - Restore the database backup onto the new environment. This allows for you to safely explore and experiment with new concepts, while main production data remains unaffected. 
      - back up folder 
            ![](assets/backup-folder-in-vm.png)
        - restoring... 
            ![](assets/restoring-process.png) 
            - success!
                ![](assets/restored-successfully.png) 

4. **Automate backups for development database:**
    - Utilize SSMS to establish a management task that automates regular backups of the development database. 
      - start SQL Server Agent
        ![](assets/start-sql-agent.png) 
            - create a new query - credentials to connect azure.
                ![](assets/new-query.png) 
                - get access key information such as storage account name and key (keep this private)
                ![](assets/get-access-key.png) 
                    - create credentials
                    ![](assets/creating-backup-creds.png) 
    - Configure a weekly backup schedule for consistent protection.
    ![](assets/maintainance-plan-wiz.png) 
        - Full database back up
        ![](assets/back-up-database-full-wiz.png) 
        - back up to URL 
        ![](assets/backup-to-url.png) 
        - finishing plan...
        ![](assets/define-wiz.png) 

          - complete  
            ![](assets/maintainance-plan-wiz.png) 
    - weekly database back up 
    ![](assets/its-weekly-auto.png) 

    - Executing the plan 
    ![](assets/execute-plan.png)
        - Back up uploaded to blob
        ![](assets/backup-in-container.png) 

## Disaster Recovery Simulation

Simulating data loss in the production environment to perform a disaster recovery simulation:

1. **Mimic Data Loss in Production Environment:**
    - Remove critical data from the production database to replicate a scenario where data integrity is compromised.
    ![](assets/deleting-fields.png) 
    - Confirm data loss through Azure Data Studio.

2. **Restore Database from Azure SQL Database Backup:**
    - Through the Azure portal, restore the production database to a point before the simulated data loss.
    - Append '-restored' to the new database for differentiation.
    - Deploy the newly restored database.
    ![](assets/restore-database-to-prev.png) 

3. **Validation:**
    - Inspect the restored database through Azure Data Studio.
    ![](assets/connect-to-azuire-restored.png) 
    - Confirm the restoration of affected data.
      - lost fields (100 fields deleted) 19872 total
      ![](assets/lost-fields-db.png) 
        - restored db fields 19972 total
        ![](assets/restored-db-fields.png) 
    - The restored database becomes the new production database.
    - Delete the database that suffered data loss in the Azure portal.

## Geo Replication and Failover

Geo-replication enhances data protection by establishing a synchronized copy of your production Azure SQL Database in a secondary region. This strategic redundancy ensures continuous data availability and minimizes potential downtime during unforeseen disruptions. In addition, a planned failover to the secondary region allows you to assess the availability and consistency of the secondary database. 

key words: 
- Primary Database: The primary database is the original database that servers your application and handles read and write operations.

- Secondary Database: The secondary database is a read-only copy of the primary database located in a different Azure region. It is synchronized asynchronously with the primary database to minimize any performance impact on the primary.

1. **Set up Geo-Replication for Azure SQL Database:**
    - Navigate Azure portal and from the dashboard, select the primary database you want to replicate. We will use the restored production database here.
    - 'Replicas' can be found under the Data Management tab on the left hand side.
    - Create a replica to begin the process. In the Geo Replica menu we will have to create a new SQL server under the server pane. This server should be located in a region geographically distant from the primary region to ensure data redundancy. This will represent the secondary region where the database will be replicated. The replicated server in this case is located in (US) EAST US.
     ![](assets/create-replica.png)
    - Use SQL authentication as the authentication method and provision the SQL log in credentials for this server. 
    - Review + Create. Azure will start the initial data synchronization between the primary and secondary databases. 
    - Visit the newly provisioned resource and on the resource page you should see information indicating that this database is a replica. i.e. Replica type.
    - A Geo Replica of the primary database has now be created.


## Failover and Tailback

Failover is the process of switching the workload from the primary region to the secondary region in a geo-replicated environment.

It is typically performed during planned maintenance or in response to a disaster in the primary region. Failover ensures high availability and business continuity by allowing applications to continue running from the secondary region.

Tailback is the process of reverting the workload back to the primary region after a successful failover.

Once the primary region is restored, the workload is switched back from the secondary region to the primary region, ensuring that the original production environment is reinstated.

1. **Test Failover and Tailback:**
    - Navigate to the Azure portal and form the Azure SQL Database dashboard, select the server associated with the primary database that you want to failover. (currently located in the UK region.)
    - Select Failover groups under the data management pane. Select Add group to create a new failover group. 
    - Enter a name for the failover group and select your secondary server, not the server on which the primary db is currently residing. 
    - Create.
    ![](assets/failover-group-created.png) 
    - Navigate to the SQL server in which your secondary/replicated database resides in. 
    - Access the failover group page. The page will indicate the two servers in the failover group and which is primary and secondary. 
    - To initiate a planned failover, select failover from the task pane. You may receive a warning about switching the secondary db to a primary role. Click yes to continue here.
    ![](assets/begin-fail-over.png)
    - Wait for the failover to complete and review which server is primary and which is secondary. Upon successful failover, the two server should have swapped roles. 
        - in process:
            ![](assets/failover-inprocess.png)  
            - success: 
                ![](assets/failover-success.png) 
    - You can use the connection details of the secondary server to connect to the new primary database and run tests to validate the database's functionality in the secondary region.
    - Run a few queries in Azure Data Studio to confirm all is in working order. For example, check the top 1000 results from different tables, run a few custom sql queries just to see you are getting the results you should be. 
    - Select Failover again to fail the servers back to their original roles.
        - tailing back... 
        ![](assets/tailingback.png)
            - tailback complete
            ![](assets/tailback-complete.png)   

## Microsoft Entra Directory Integration

Integrating Microsoft Entra Directory with your Azure SQL Database introduces a more organized way to manage who can access your data. 

### What is Microsoft Entra? 

Microsoft Entra ID is Microsoft's cloud-based identity and access management service, designed to help organizations manage and secure their users, applications, and data. When it comes to Azure resources, including Azure SQL Database, traditional username/password-based authentication may not provide the level of security and convenience required by modern cloud-based applications. That's where Microsoft Entra ID integration comes in.

Integrating Microsoft Entra ID with Azure SQL Database offers a range of advantages that enhance security, simplify user management, and enable a more seamless user experience. This integration leverages the power of identity-based authentication and access control, providing a robust and scalable solution for managing database access in a multi-tenant cloud environment.

1. **Configure Microsoft Entra ID for Azure SQL database:**
    - Navigate to the Azure portal and open the SQL Server that hosts your primary database.
    - Under settings, select Microsoft Entra. 
    - Click on Set admin to assign a Microsoft Entra user or group as the admin for the server. This admin will have elevated privileges to mange the server and its databases. 
    - Save the changes. 
    ![](assets/set-admin.png) 
    - Test by connecting to the primary database through Azure Data Studio (if already connected, disconnect). This time instead of SQL Login as authentication type, we will use Microsoft Entra ID - Universal with MFA support. 
      - disconnect: 
      ![](assets/disconnect.png)  
    - Add your account under the account field. 
        - reconnecting: 
      ![](assets/reconnect.png)    
    - Click connect to establish connection to the database. Once connected, explore the database, run queries and manage database objects. As the admin account, you will have the necessary privileges to perform administrative tasks on the database. 
        ![](assets/permissions-still-available.png) 



In addition to the admin account, you can grant access to Azure SQL Database for specific Microsoft Entra users or groups. Microsoft Entra users can have varying levels of permissions, depending on their roles and responsibilities within your organization.

***Common Roles for Microsoft Entra Users in Azure SQL Database include:***
- _db_datareader_: This role grants read-only access to the database, allowing users to view data but not modify or alter it
- _db_datawriter_: This role grants write access to the database, allowing users to modify data but not perform certain administrative tasks
- _db_owner_: This role provides full control over the database, including the ability to modify schema, data, and grant permissions to other users


1. **Create DB Reader user:**
    - Navigate to the Azure portal and go to the Microsoft Entra ID homepage. Create a new user that will ultimately be assigned the db_datareader role.
    - Open Azure Data Studio and connect to the desired database (primary) using the Microsoft Entra Admin credentials. 
    - Right-click the server connection and select New Query to open query editor.
    - Run the following SQL Query to grant the db_datareader role to the DB_Reader user: 


```
CREATE USER [DB_Reader@yourdomain.com] FROM EXTERNAL PROVIDER;

ALTER ROLE db_datareader ADD MEMBER [DB_Reader@yourdomain.com];
```

[Db_Reader] represents the name of the Microsoft Entra user you just created. 

You can find the name of your domain from the Microsoft Entra Directory user page, under User principal name. The domain it's what comes after @.

3. **Test DB Reader's User Access:**
    - Re-connect to the Azure SQL Server in Azure Data Studio using the new user credentials. To do this, right click the server and edit connection. 
    - This will open the connection page. Add an account and follow the login process. Use the principal name from above as the email address to log in with. 
    - First time login will demand a new password be setup. Once done, press connect to connect to the database using the new user. 
    - With this user, you should now have read-only access to the database and be able to view its data. Try and check the top 1000 results for a table.
    - Now try rn a query to delete an entry. You should meet an error, 'DELETE permission was denied on the object...' This indicates the user doesn't have access to modify the data, but read-only as expected. 


### ***Conclusion*** 
Throughout this project, we have architected and implemented a cloud-based database system on Microsoft Azure, showcasing hands-on expertise in cloud-engineering. We established a production environment database which subsequently underwent migration to Azure SQL Database and in addition, focussed on crucial aspects like data backup, restoration and automated scheduling. 

A pivotal phase of this project involved simulating a disaster recovery scenario with potential data loss and understanding how to recover from that. Furthermore, exploring the complexities of geo-replication and failover configurations to ensure data availability even under challenging conditions. 

To further enhance security, Microsoft Entra ID integration was employed to define access roles, adding an extra layer of control and protection. 