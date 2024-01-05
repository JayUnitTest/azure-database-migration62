# Azure Database Migration - AICore

This project is dedicated to the implementation of a robust, cloud-based database system utilizing Microsoft Azure, showcasing expertise in cloud engineering.

## Environment Setup

### Prerequisites:
- Microsoft Azure account
- Azure virtual machine (VM)
- SQL Server and SQL Server Management Studio (SSMS)
- Microsoft Remote Desktop
- Database file (.bak file) for restoration (i.e., AdventureWorks by Microsoft)

To establish the foundation for the cloud-based database system on Azure, we need to provision a Windows virtual machine, serving as the cornerstone of the production environment. We will then install SQL Server and SQL Server Management Studio on the Azure VM. 

### Steps:

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
