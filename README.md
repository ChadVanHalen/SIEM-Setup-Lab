<h1>SIEM Setup Lab</h1>

<h2>Description</h2>
This project consists of getting hands-on experience of the installation of Splunk on a Linux VM, having a separate Linux VM transmit data to the Splunk server, all via CLI. I then demonstrate dealing with said data in the SIEM's GUI interface, showing basic searching functionalities and displaying certain parameters of a simulated attack via a graph.<br/>
<br />


<h2>Utilities Used</h2>
 
- <b>Splunk Enterprise</b>
- <b>Splunk Universal Forwarder</b>
- <b>SSH</b>

<h2>Environments Used </h2>

- <b>Ubuntu Server 22.04.1 LTS</b>

<h2>Project walk-through:</h2>

<p align="center">
My first step is downloading Splunk Enterprise onto the Ubuntu Server itself. Here I'm using 'wget' to download the .deb file to Linux, and installing it onto the Linux VM. <br/>
I also do some basic configuration; like setting Splunk to start on boot, as well as setting the Splunk user as the owner of the Splunk directories, instead of the root. This will help mitigate any root access an attacker could get if they found a vulnerbaility in the web interface. <br/>
You can see the difference in ownership between pictures 4 and 8 below:<br/>
<img src="https://i.postimg.cc/Pxvk41HT/1-Ubuntu-VM-Splunk.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/yxqCrW69/2-Splunk-boot-on-start.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/3w6sdWv6/4-Start-Splunk-log-in.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/Mps2J9Kz/5-Check-permissions-on-Splunk-securitylibrary-step.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/8Cj8pLjM/6-Set-permissions-for-Splunk-user.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/pXJMq2wh/7-Starting-Splunk-as-Splunk-user.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/tRPjndDz/8-Setting-Splunk-user-as-default-user.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/GhC1FB5n/9-Confirming-all-Splunk-process-are-owned-by-Splunk-user-instead-of-root.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
After Splunk is setup on the main Splunk VM (chadvanhalen@splunkmachine) I now focus on setting up the secondary system, who will be sending its telemetry to Splunk via the Universal Forwarder (user@forwarder):  <br/>
<img src="https://i.postimg.cc/4yGF9cN8/10-Installing-Universal-Forwarder-on-2nd-Linux-VM.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
With the Forwarder machine set up, now I create a .conf file, using terminal commands like touch to create the file and nano to edit the file.  <br/>
These configuration files are defining which ports are being used to ingest the logs, and which are being used to output to logs. In this example I'm using the defauls of 9997 on input and 8089 on the output, as found in the Splunk documentation: <br/>
<img src="https://i.postimg.cc/dVQPfbFW/11-Creating-conf-file-in-CLI.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/2yNg7JT7/12-Editing-conf-file-within-nano.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
In the Splunk GUI I then create a test index to pull data from the forwarder.  <br/>
In an actual deployment this test index would be used to make sure everything was working as intended before making it a more permanent index. But for this demonstration, I will keep the "test" index: <br/>
<img src="https://i.postimg.cc/522Tg85T/13-Creating-test-index-in-Splunk.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
Here I'm using cat to look at the auth.log to make sure this is the type of log I want to test with, then creating an input and output now on the Forwarder.<br/>
I then define to use the 'monitor' function on auth.log. Looking at the documentation this seemed to be the most straight forward to set up of the many, many functions Splunk offers:  <br/>
<img src="https://i.postimg.cc/9fvkcK3H/14-Focusing-on-forwarder-VM-auth-log-using-cat-to-read-logpng.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/wvmSJH5B/15-Cretaing-inputs-and-outputs-conf.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/fy7p2Krn/16-Add-monitor-of-auth-log-to-test-index-within-inputs-conf.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/Wp0B1NbW/17-Nano-outputs-conf.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
Enabling the firewall, allowing in ports for SSH as well as the Splunk ports; 8000, 8089 and 9997:  <br/>
<img src="https://i.postimg.cc/rFQYDK6J/18-Starting-firewall-setting-ACL-allowing-Splunk-ports-and-SSH-ports.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
Seeing my success as data starts to populate when searching in the GUI on my Windows machine:  <br/>
<img src="https://i.postimg.cc/cCckMJvd/19-Searching-index-on-Splunk-before-full-data-population.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
Running a few attempts that should get caught by the log, one through failed login on the VM itself, one through failed login via SSH:  <br/>
<img src="https://i.postimg.cc/dtBgG5CG/20-Incorrect-login-on-Linux.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/c4ZVKpvs/21-Fake-SSH-attempt.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
Here I'm doing my best Thomas Edison impression and finding all the wrong ways to create dashboards and alerts:  <br/>
<img src="https://i.postimg.cc/N0yVqYzx/22-Built-failed-SSH-attempt-as-dashboard-incorrectly.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/4dVSyPZ7/23-Bad-SSH-alert-incorrectly.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
I discovered my mistake, the number I based my search off of [4106] in the last step wasn't a PID like I had thought, it was more of an event number that goes up sequentially, rather than defining a specific funtion or event.<br/>
Luckily I caught it early when other failed SSHs weren't showing up in any search or alert.<br/>
I changed the parameters to instead look for "sshd" and "failed", which worked on catching all my future failed SSHs:  <br/>
<img src="https://i.postimg.cc/137Tj4Jt/24-SSH-attempts-after-alerts-and-reports.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br/>
<br/>
I then took this parameter of failed SSH attempts and was able to visualize the data in a graph showing attempts over time:  <br/>
<img src="https://i.postimg.cc/tRdcHB28/25-Converting-searches-into-charts-for-analysis.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/jdsFqy12/26-Adding-the-graph-to-my-dashboard-after-a-few-more-attempts.png" height="80%" width="80%" alt="SOC Analyst Lab"/>




<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
