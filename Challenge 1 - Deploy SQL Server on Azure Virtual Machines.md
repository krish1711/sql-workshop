# Exercises to Deploy SQL Server on Azure Virtual Machines

This is a set of exercises to deploy SQL Server on Azure Virtual Machine. The exercises are designed to be completed in a workshop environment with an instructor; however, you can also complete them on your own if you have the necessary Azure subscription and resources.

In these exercises, you will be given a scenario and set of requirements to deploy SQL Server on Azure Virtual Machine. You will learn new skills unique to Azure as well as using your existing SQL Server skills to achieve a successful deployment.

In these exercises you will:

- Learn how to deploy SQL Server on Azure Virtual Machine using the Azure Portal.
- Learn how to configure storage for SQL Server on Azure Virtual Machine.
- Learn how to connect to the Azure Virtual Machine and prepare for the next module.
- Learn how to look at deployment details of the Azure Virtual Machine.

## Prerequisites

- You must have an Azure subscription with the ability to create Azure Virtual Machines in the Azure region of your choice. For instructor led workshops, check with your instructor on a provided subscription.
- You must have permissions to deploy a SQL Server on Azure Virtual Machine from the Azure Marketplace.
- You will need a client computer that can use the Remote Desktop Protocol(RDP). Some users may not be allowed to use RDP from their client computer. If you are not allowed to use RDP from your client computer you can use a Bastion service in Azure to connect to the Azure Virtual Machine. For instructor led workshops, check with your instructor on a provided Bastion service. Otherwise, later in this module you will get instructions on how to deploy a Bastion service.
- You will need access to the files and scripts associated to the workshop which you can download into your deployed VM from https://aka.ms/cloudsqlworkshopfiles.

## Exercise 3.1 - Study the scenario and requirements

You have a requirement to deploy a new SQL Server on Azure Virtual Machine to migrate a SQL Server database to perform a proof of concept (PoC) test. You are trying to save costs so you want to use deployment options that meet the requirements of your PoC with the *minimal costs possible*. You also need to try and minimize the amount of time it takes to deploy the solution so try to configure as many requirements during deployment.

You must meet the following requirements:

### Software and Virtual Machine requirements

- Deploy SQL Server 2022 with Windows Server 2022. You may use any edition of SQL Server including any free edition.
- The Azure Virtual machine must support at minimum 4 vCores and 32Gb of RAM and use the **E5-series**. So for example, the **E4ds_v5** VM Size could be a good starting point. You must deploy the virtual machine using the Azure Portal. For the purposes of this exercise, use VM sizes with Intel processors.
- The Virtual Machine has these other requirements:
    - Create a new resource group of the name of your choosing. **Note: Your instructor may aleady have resource groups deployed**
    - Choose a VM name of your choosing.
    - Choose any region that is supported for your subscription and RBAC permissions. **Note: For instructor led workshops, check with your instructor.**
    - The VM has No Infrastructure Redundancy requirements
    - The VM can use the Standard security type
    - The VM cannot run with Azure Spot discounts
    - Do not choose the Enable Hibernation option if it is available.
    - You are free to choose any admin and password that meet requirements for complexity. **Note:** Keep track of the admin and password you choose as you will need it later.
    - The public inbound port 3389 for RDP can be left open to the public internet for this PoC.
    - You do have existing Windows Server licenses that you can use for this PoC.
    - There are no special requirements for the OS disk,  Management, Advanced, or Monitoring sections when deploying the VM.
    - For Networking, choose the default virtual network and subnet that is created for you. You do not need to create a new virtual network or subnet. You need to make sure you use a **Basic** NIC networking security group.
        - **Important Notes:** For instructor led labs your instructor may ask you to deploy the Azure Virtual Machine in a *specific virtual network and subnet* that allows it to easily connect to Azure SQL Managed Instance for other modules.
        - **Users who cannot use RDP** may need to deploy a Bastion service deployed with your virtual network in Azure to connect to the Azure Virtual Machine. For instructions to deploy a Bastion service see https://docs.microsoft.com/azure/bastion/bastion-create-host-portal. For instructor led labs your instructor may provide you with a Bastion service to use.
    - You do not have to specify any tags for the VM.
- The SQL Server instance should be configured as follows:
    - MAXDOP for the server = # of cores from the VM
    - Lock Pages in Memory enabled.
    - Instant File Initialization enabled.
    - All other instance configuration choices can be left to their defaults.
    
### Storage requirements

The database, system databases, transaction log, and tempdb must meet the following requirements:

**Requirements for database files**

- Database files must be stored on a disk other than the OS disk, can use the default drive letter and path from the Azure Portal, and require Premium SSD. Do not store user database data or log files on temporary or ephemeral disks.
- The database files only require **512Gb** storage to account for growth. (Note: the database used to verify the scenario is only 1Gb in size but is only used a test).
- I/O performance requirements for database files is **3000 to 4000 IOPS**, and a max of **200MB throughput**.
- System databases (other than tempdb) should be configured to be on the same disk as database files.

**Requirements for the transaction log**

- Must be stored on a separate disk from database files, can use the default drive letter and path from the Azure Portal, and require Premium SSD.
- The transaction log requires **128GB** storage, **500 IOPS**, and **100MB** max throughput.

**Requirements for tempdb**

- Tempdb must be stored on the local SSD drive for Azure VM so you must choose an Azure VM size that supports local SSD storage. Tempdb *could grow to 128Gb* in size so the VM size you choose must support that.
- Configure the number of tempdb files to match vCores for your chosen VM size. 
- Use other best practices to make tempdb data and transaction log files an initial size of 8MB with autogrow set to 64MB.

You do not need to use any Tags.

## Exercise 3.2 - Deploy the Virtual Machine

In this exercise, you will go through the process of deploying SQL Server on an Azure Virtual Machine using the Azure Portal.

1. Use the Azure Portal (https://portal.azure.com) to deploy a new Azure Virtual Machine that meets the requirements above.

2. To help you get started in the Azure Portal type in Azure SQL in the search box and choose **Azure SQL** and then choose **Azure SQL** from the Marketplace section.

3. You should see three choices for Azure SQL. Select the Image dropdown for SQL Virtual Machines. Based on the requirements choose the least expensive option that meets the requirements and click Create. You do not need the High Availability option.

1. You are now in a user experience to **Create a virtual machine** with several tabs starting with **Basics**.

1. On the **Basics** tab, fill in all the information that meet the requirements from this exercise. To help you get started, use the **E4ds_v5** Azure VM size.

1. *Per the requirements* listed earlier in this module configure Disks, Networking, Management, Monitoring, and Advanced tabs.

2. Use the **SQL Server settings** blade to configure SQL Server instance settings and storage *per the requirements* listed earlier in this module. Use the defaults for Security and Networking section.

    1. Use the *Change configuration* link to use the Storage configuration assistant. Make choices based on the requirements for the scenario. Take your time and carefully review all options.

    **Tips:** You may need to provision more storage than you need to meet the IOPS and throughput requirements. You may also need to choose a different VM size to meet the IOPS and throughput requirements. Look for any warnings on the Configure Storage screen. Go directly back to the Basics tab to change the VM size if necessary.

    The portal alone may not show you all the information you need so you may need to consult these documentation pages: https://learn.microsoft.com/azure/virtual-machines/ebdsv5-ebsv5-series and https://learn.microsoft.com/azure/virtual-machines/edv5-edsv5-series. Use the **Max uncached Premium SSD disk throughput: IOPS/MBps column** to help you choose the right VM size. You may also need to consult this documentation page: https://learn.microsoft.com/azure/virtual-machines/disks-types#premium-ssd.

    1. Click on the *Change SQL instance settings* to change SQL Server instance settings per the requirements.

    1. Leave all other settings to their defaults.

1. When you are ready, click on **Review + Create** and then **Create** to start the deployment. Monitor the deployment until it is successful. It should finish in around 8-10 minutes (the exact time can vary).

