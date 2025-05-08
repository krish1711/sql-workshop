### Answers for the exercises

The requirements and steps should give you enough information to complete the exercise but choosing the right storage and VM size to meet the requirements can be tricky. Here are some answers to help you should you get stuck or want to verify your work.

#### Savings costs

- Choose SQL Server Developer Edition to save on licensing costs.
- On the Basics tab choose to use an existing Windows license.

#### Storage configuration

Storage is usually what will be most difficult to configure. Consider the following answers to help you if you get stuck or want to verify your work.

- If you first start with a 512GB disk you will see that the IOPS and throughput are not enough. You will need to choose a larger disk size. But when you go to 1TB you will see the IOPS and throughput are enough but you will see warnings about the VM size capping IOPS and throughput.
- Change the transaction log to 128GB. The IOPS and throughput are enough for that disk.
- The warning still exists for the VM size but not for IOPS anymore but for throughput. We need a VM size that supports our throughput requirements for both disks.
- So you need to cancel out of this and go back and choose a different VM size. **Note:** If you stayed with this choice you would be capped on IOPS and throughput that is less than what is required.
- Our app only needs 4 vCores so we don't want to have to overpay for cores to get the I/O performance we need. So the **E4bds_v5** becomes a new choice that meets all of our requirements but is still cost effective. Change to this VM size then go back to the storage configuration assistant. You will see that there are no more warnings. You now have the storage performance you need for the workload

## Exercise 3.3 - Post deployment steps

After your virtual machine is deployed, let's first look at Azure Resource Manager deployment details, make sure we can connect to the VM, and then prepare for the next module.

### Looking at your deployment

Learn how to look at more details on your deployment of the virtual machine using the Azure Portal.

1. First let's look at the deployment from the perspective of the resource group. If you are looking at your virtual machine in the Azure Portal you can click on your resource group or it may be listed in Resources on your home page. You can also search for your resource group in the search box at the top of the Azure Portal by typing in your resource group name and selecting it.
1. On the left-hand menu select **Deployments**.
1. The deployment name should start with "CreateVm....". If you scroll to the right you can see the Duration of the deployment which is approximately the amount of time it took to deploy the virtual machine, build a virtual network, provision storage, and run software in the VM to deploy SQL Server.
1. Click on the "CreateVm..." under Deployment Name. Click on the "V" option next to Deployment details. This will list all the detailed steps of how the virtual machine was deployed.
1. Click on **Go to Resource**. You are now in the context of the virtual machine resource.
1. On the left-hand menu click on **Activity Log**. This is a continuous log of all the activities that have happened to the resource. You can see the deployment of the virtual machine and the deployment of the SQL Server IaaS Agent extension. You can also see the deployment of the SQL Server instance. You can click on any of these activities to see more details.

### Connecting to the VM and preparing for the next module

Use the following steps to connect to the VM with RDP and prepare for the next module.

1. Use Remote Desktop to connect into the Virtual Machine. Users who cannot use RDP and have deployed a Bastion service can connect to the VM. Read these instructions on how to connect to a VM with a Bastion service: https://docs.microsoft.com/azure/bastion/bastion-connect-vm-rdp.
    1. In the Azure Portal for your virtual machine select **Connect** from the left-hand menu.
    1. Under Native RDP click on **Select**.
    1. Scroll down to **Download and open the RDP file** and click on Download RDP file.
    1. Select the RDP file to open it and click on **Connect**.
    1. When you first connect into the VM you can select No for discovering your device on networks.
1. Inside the virtual machine, download the **tpch.bak** SQL Server backup file and **Source code** zip file from https://aka.ms/cloudsqlworkshopfiles. Move the tpch.bak file into the f:\data folder.
1. *Extract* out the Source code zip file which will put the files into the **`<user>`\Downloads\cloudsqlworkshop-1.0-release** folder.
1. From the extracted workshop source files load the **restore_tpch.sql** script from the **`<user>`\Downloads\cloudsqlworkshop-1.0-release\cloudsqlworkshop-1.0-release\cloudsqlworkshop\03_Deploy_SQL_AzureVM** folder into SSMS to restore the database. This should only take about 10-15 seconds to restore. You can connect with SSMS using the local server with Windows Authentication as the admin you configured during deployment is automatically setup as a sysadmin SQL Server login. 

**Note:** The default for SSMS is now Mandatory Encryption so you will need to check the Trust server certificate box when connecting.

## Next Steps

In the next Module, you will perform various steps to verify the deployment meets the requirements of the scenario as defined in this module. You will also learn about how to explore capabilities of the Azure Virtual Machine provided by the SQL Server IaaS Agent extension.
