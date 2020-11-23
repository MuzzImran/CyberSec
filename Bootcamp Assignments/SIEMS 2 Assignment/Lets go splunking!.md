## Unit 18 Homework: Lets go Splunking!

### Scenario

You have just been hired as an SOC Analyst by Vandalay Industries, an importing and exporting company.
 
- Vandalay Industries uses Splunk for their security monitoring and have been experiencing a variety of security issues against their online systems over the past few months. 
 
- You are tasked with installing new Splunk apps and developing searches, custom reports and alerts to monitor Vandalay's security environment in order to protect them from future attacks.


### System Requirements 

You will be using the Splunk app located in the Ubuntu VM.


### Your Objective 

Utilize your Splunk skills to design a powerful monitoring solution to protect Vandaly from security attacks.

After you complete the assignment you are asked to provide the following:

- Screen shots where indicated.
- Custom report results where indicated.

### Topics Covered in This Assignment

- Researching and adding new apps
- Installing new apps
- Uploading files
- Splunk searching
- Using fields
- Custom reports
- Custom alerts

Let's get started!

---

## Vandalay Industries Monitoring Activity Instructions

### Step 1: Monitoring Vandalay's Web Servers

**Background:**  As the worldwide leader of importing and exporting, Vandalay Industries has been the target of many adversaries attempting to disrupt their online business. Recently, Vandaly has been  experiencing DDOS attacks against their web servers.

**Task:** Install and configure a new Splunk App called **Network Toolkit** to monitor Vandaly's web servers `198.153.194.1` and `198.153.194.2`.  As a note, **Network Toolkit**  has many networking capabilities such as ping, traceroute, and speedtests.

1. In the Splunk app on your Ubuntu VM, find and install the add-on called **Network Toolkit** .
   
2. After a successful install, select the new **Network Toolkit**  app from your list of available apps.

   ![network toolkit install](images/Step1-NtwkTk-install.png)
3. Select **Ping** >> and **Execute Ping** (leave the other fields as they are)

   - Enter in the following hosts one at a time:  `198.153.194.1` and `198.153.194.2` then click **Execute Ping**
 
4. Let the monitoring run and view the **results** page
   
5. Provide a screenshot of the results screen that displays the state of your web servers
   ![Ping Results](images/Step1-Ping.png)

Bonus: Run the speedtest and provide a screenshot of those results. 
   ![Speed Test](images/Step1-Bonus-SpeedTest.png)


### Step 2: The Need for Speed 

**Background**: Not only were web servers taken offline by a DDOS attack, but upload and download speed were also significantly impacted after the outage. Your networking team provided results of a network speed run around the time of the latest DDOS attack.

**Task:** Create a report to determine the impact that the DDOS attack had on download and upload speed. Additionally, create an additional field to calculate the ratio of the upload speed to the download speed.


1.  Upload the following file of the system speeds around the time of the attack.
    - [Speed Test File](resources/server_speedtest.csv)

2. Using the `eval` command, create a field called `ratio` that shows the ratio between the upload and download speeds.
   
  Search command: source="server_speedtest.csv" host="server_speedtest" sourcetype="csv" | eval ratio= UPLOAD_MEGABITS/DOWNLOAD_MEGABITS

   ![Eval command](images/Step2-2_eval-a.png)

   As see by the image below, a field 'ratio' is created under Intresting Fields. 
   ![Ratio field](images/Step2-2_eval-b.png)
   
      
3. Create a report using the Splunk's `table` command to display the following fields in a statistics report:
    - `_time`
    - `ip_address`
    - `download_megabits`
    - `upload_megabits`
    - `ratio`
  
   Search Command: source="server_speedtest.csv" host="server_speedtest" sourcetype="csv" | eval ratio = UPLOAD_MEGABITS/DOWNLOAD_MEGABITS | table _time IP_ADDRESS UPLOAD_MEGABITS DOWNLOAD_MEGABITS ratio

   ![Ratio field](images/Step2-3a.png)

   Report is named "Impact of DDos attack on network Speed" 

    ![Report](images/Step2-3b.png)

4. Answer the following questions:

    - Based on the report created, what is the approximate date and time of the attack?: **Approx between 2300 hrs on 2020-02-22 to 1400 hrs on 2020-02-23**
   - How long did it take your systems to recover?: **9 hours** 

     This is illustrated by the following screenshot;
      ![Time chart](images/Step2-3c.png)
 
### Step 3: Are We Vulnerable? 

**Background:**  Due to the frequency of attacks, your manager needs to be sure that sensitive customer data on their servers is not vulnerable. Since Vandalay uses Nessus vulnerability scanners, you have pulled the last 24 hours of scans to see if there are any critical vulnerabilities.

  - For more information on Nessus, read the following link: https://www.tenable.com/products/nessus

**Task:** Create a report determining how many critical vulnerabilities exist on the customer data server. Then, build an alert to notify your team if a critical vulnerability reappears on this server.

1. Upload the following file from the Nessus vulnerability scan.
   - [Nessus Scan Results](resources/nessus_logs.csv)

2. Create a report that shows the `count` of critical vulnerabilities from the customer database server.
   - The database server IP is `10.11.36.23`.
   - The field that identifies the level of vulnerabilities is `severity`.
   
   Report is named "Count of Critical Vulnerabilities", with the count 49. 
      ![Report](images/Step3-2.png)

3. Build an alert that monitors every day to see if this server has any critical vulnerabilities. If a vulnerability exists, have an alert emailed to `soc@vandalay.com`.

Search Command for Alert: source="nessus_logs.csv" host="Nessus_Logs" sourcetype="csv" dest_ip="10.11.36.23"  severity="critical" |  top dest_ip severity | fields - percent
![Save as Alert](images/Step3-3a.png)
![Save as Alert Trigger Actions](images/Step3-3b.png)

Alert is created, named Critical Vulnerbilities

![Alert](images/Step3-3c.png)



### Step 4: Drawing the (base)line

**Background:**  A Vandaly server is also experiencing brute force attacks into their administrator account. Management would like you to set up monitoring to notify the SOC team if a brute force attack occurs again.


**Task:** Analyze administrator logs that document a brute force attack. Then, create a baseline of the ordinary amount of administrator bad logins and determine a threshold to indicate if a brute force attack is occurring.

1. Upload the administrator login logs.
   - [Admin Logins](resources/Administrator_logs.csv)

2. When did the brute force attack occur?
   Between 0800 hrs and 1400 hrs on 2020/02/21

![Eval of Bruteforce](images/Step4-2.png)
      
3. Determine a baseline of normal activity and a threshold that would alert if a brute force attack is occurring.  

A normal activity baseline could be 25 events per hour, so a threshold of above 25 events per hour for every continuos hour may be a potential bruteforce attack.

As shown by the image below the activity numbers for "account failed to log on" increased Between 0800 hrs and 1400 hrs on 2020/02/21, above the 25 event baseline to 34, then 124, 101, 121, 95, 123, 34 and then back below 25. 

![Baseline ](images/Step4-2b.png)

4. Design an alert to check the threshold every hour and email the SOC team at SOC@vandalay.com if triggered. 
Search command for alert: source="Administrator_logs.csv" host="Admin_logs" sourcetype="csv" | eval bruteforce= if('name'="An account failed to log on", "Potential Bruteforce Attack", "Not Bruteforce Attack") | search bruteforce= "Potential Bruteforce Attack"

![Alert Bruteforce](images/Step4-4.png)
 

---

© 2020 Trilogy Education Services, a 2U, Inc. brand. All Rights Reserved.
