# File Integrity Monitoring with Wazuh - Windows and Ubuntu

### 1. Description

This home lab project focuses on implementing File Integrity Monitoring (FIM) using Wazuh, an open-source security monitoring solution. The FIM will be implemented for Windows 11. FIM is crucial for maintaining the security and integrity of files on systems by detecting unauthorized changes, which could indicate potential security breaches or system tampering.

##  **Why File Integrity Monitoring**
- Unauthorized file modifications
- Malware activity
- Insider threats
- Policy violations

It is commonly used in compliance standards such as PCI-DSS and ISO 27001.



### 2. Objectives

- Set up a Wazuh server to collect and analyze security data.
- Set up and configure agent on target systems (Windows) to monitor specific files and directories for changes.
- Implement alerting mechanisms to notify administrators of any detected file changes.
- Generate reports to track changes over time and assess compliance with security policies.


### 3. Tools and Technologies Used

- **VirtualBox**: Used for creating virtual machines for the lab environment.
- **Windows**: Used as a monitored system;
- **Ubuntu Server 24.04 LTS**: In in Wazuh tool for log management and security monitoring;


### 4. Lab Setup
   - **Network Diagram**:
   
The diagram below illustrates how the components will be interconnected all together, along with their description and IP addresses details.

<p align="center">
<img width="500" alt="network diagram" src=https://github.com/Marinus-Bakara/File-Integrity-Monitoring-with-Wazuh/blob/main/network%20diagram.png>
</p>

   - **Components**:
     - **Wazuh Manager**: Centralized management console.
     - **Agent Node**: Installed on target system (Windows a) to collect logs.

### 5. Installation Steps
   - **5.1. Setting Up VirtualBox for ubuntu  24.04.LTS** setting for my ubuntu where wazuh is instelled in VirtualBox

<img width="500" src=https://github.com/Marinus-Bakara/File-Integrity-Monitoring-with-Wazuh/blob/main/1.png>
<br>
<br>
   
   - **5.2: Setting Up Windows 2022 on VirtualBox** For setting up Windows 2022 on VirtualBox, refer to

 <img width="500" src=https://github.com/Marinus-Bakara/File-Integrity-Monitoring-with-Wazuh/blob/main/setting%20for%20windows.png>
<br>
<br>


   - **5.4: Setting Up Wazuh Manager on Ubuntu Server 24.04 LTS**

     - After logging into the server, update the package manager:
       ```bash
       sudo apt update && sudo apt upgrade -y && sudo reboot
       ```
     - Install Wazuh Manager and all other components:
       ```bash
       curl -sO https://packages.wazuh.com/4.9/wazuh-install.sh && sudo bash ./wazuh-install.sh -a
       ```
<img width="500" src="https://github.com/Marinus-Bakara/File-Integrity-Monitoring-with-Wazuh/blob/main/2.png">

- **5.5: check your ip address** this is the address we are going to use to open our wazuh web interface using our browser copy it and paste it on your browser

  <img width="500" src="https://github.com/Marinus-Bakara/File-Integrity-Monitoring-with-Wazuh/blob/main/4.png">
 
  **5.6: Run the following command to start wazuh**

     <img width="500" src="https://github.com/Marinus-Bakara/File-Integrity-Monitoring-with-Wazuh/blob/main/5.png">
     
     - **accept all the option in other to login,and why is saying we should accept the risk on continue is that we are not using a valid certificate for it.After opening it you enter your credential that is the admin name and password we had earlier** 


<img width="500"  src="https://github.com/Marinus-Bakara/File-Integrity-Monitoring-with-Wazuh/blob/main/merged.png">

 **Deploy new agent**

<img width="500"  src="https://github.com/Marinus-Bakara/File-Integrity-Monitoring-with-Wazuh/blob/main/agent%20deployment.png">

After clicking the **Deploy new agent** button, choose the operating system and fill all the details asked in the following screen as shown in the images below:

<p align="center">
<img width="643" alt="details filled" src="https://github.com/Marinus-Bakara/File-Integrity-Monitoring-with-Wazuh/blob/main/complete%20deployment.png">
</p>

After running all the given commands on your windows machine, click the **Close** button and check for the connectivity between the manager and the agent, the screen should look like the image below.

<p align="center">
<img width="643" alt="Agent showing in the manager" src="https://github.com/Marinus-Bakara/File-Integrity-Monitoring-with-Wazuh/blob/main/active.png">
</p>

If not showing as **active** wait for some minutes and refresh the page.

   - **5.6: Configure the agent to monitor the files or folders you intend to moniotr**
Now that we already deployed the agent, let configure it. The configurations will be made on the file **ossec.conf** located at **C:\Program Files (x86)\ossec-agent**. Before we change any configuration on it, it is a best practice to make a backup of the file, so copy and rename the file so that anything going wrong we can be able to revert to our functional version. For this demonstration we will be monitoring the folder **C:\Users\Public**, but you are free to choose another directory. So locate the section with **File integrity monitoring** and add the content below inside of that:

       ``` bash
       <directories realtime="yes">C:\Users\Public</directories>
       ```

After editing the configuration file, save it and restart the agent. Go to **seacrh bar**, type **services** and hit **ENTER**. Locate the service called **wazuh**, right click on it and choose **restart**, or on powerShell run the below commands:

```
       NET STOP WazuhSvc
       NET START WazuhSvc
```


After that, check if is there any event on the manager, following the steps shown in the image below, then go to **Events**.

<p align="center">
<img width="643" alt="Check events" src="https://github.com/Marinus-Bakara/File-Integrity-Monitoring-with-Wazuh/blob/main/file%20int%20complete.png">
</p>

If you followed everything from the beginning, you will notice that there is no event triggered, that because we didn't make any modification yet. Let us create a file inside the folder **C:\Users\Public** and check if we will have any event.
Inside, we created one folder and one file, called **Test Folder** and **Test file**, as shown below. Go and see again the **Events** under **File Integrity Monitoring**.

<p align="center">
<img width="421" alt="File and Folder created" src="https://github.com/Marinus-Bakara/File-Integrity-Monitoring-with-Wazuh/blob/main/folder%20side.png">
</p>


Now you can see the changes, you can see alert when a file is added.

<p align="center">
<img width="643" src="https://github.com/Marinus-Bakara/File-Integrity-Monitoring-with-Wazuh/blob/main/details%20add.png">
</p>

Now you can see the changes, you can see alert when a file is deleted

<p align="center">
<img width="457" alt="Details about whodata" src="https://github.com/Marinus-Bakara/File-Integrity-Monitoring-with-Wazuh/blob/main/details.png">
</p>

Let now make some changes on the file and see if the wazuh will trigger them.
So, open the file and type any text, in this case the example will be ***This is an example of adding some text on the file***


<p align="center">
<img width="457" alt="File modification example" src="https://github.com/Marinus-Bakara/File-Integrity-Monitoring-with-Wazuh/blob/main/num%20char.png">
</p>

As can be seen in the image above. number 4 - the amount of characters after the modification and number 5 - the amount of characters before the modification.


### 6. **Conclusion**
   - This project successfully demonstrated:
<p>
-- The deployment of wazuh server, along with manager, indexer and dashboard, on Ubuntu 24.04 ;
</p>
<p>
-- The deployment of wazuh agent on Windows 10 2022;
</p>
<p>
-- The File Integrity Monitoring on Windows.
</p>

## 7. **Results**

The system successfully detected:
- File creation events
- File deletion events
- File modification events

Each event included:
- Timestamp
- File path
- Type of change
- User (whodata)

This confirms that Wazuh FIM is functioning correctly.


## 8. **Challenges Faced**

- SSL certificate warning in browser
- Agent not showing as active immediately
- Configuration file path issues

## 9. **Solutions**

- Accepted self-signed certificate temporarily
- Waited and refreshed dashboard
- Verified ossec.conf configuration

  ## 10. **Future Improvements**

- Monitor multiple directories
- Integrate email alerting
- Deploy in a cloud environment (AWS/Azure)
- Extend to Linux agents


### 11. **Contact Information**
   - **Email**: bakaramarinus3@gmail.com
  


