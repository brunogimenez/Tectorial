---
title: "[Tectorial From Zero to Hero] Firepower SDWAN"
author:
  - Bruno Fagioli (bgimenez@cisco)
revision: 0
---
# (Firepower SD-WAN) Tectorial from Zero to Hero

| **Note** | This is not a Firepower training, it expects people to know how to configure the basics including, but not limited to: interfaces, static routing, dynamic routing, rules and NAT |
|--|--|
## Agenda
- Lab Topologies
- Overall configuration
- Interface configuration
- Routing and ECMP configuration
- Direct Internet Access (DIA) configuration
- SDWAN configuration
- SDWAN and WAN status/summary
## Lab Topologies

![Pasted image 20240313182116.png](img/Pasted image 20240313182116.png)
![Pasted image 20240313182127.png](img/Pasted image 20240313182127.png)
![Pasted image 20240313182137.png](img/Pasted image 20240313182137.png)
![Pasted image 20240313182148.png](img/Pasted image 20240313182148.png)

## Overall configuration

The following screenshots will guide you how to configure the interfaces, static routings and ECMP alongside with the topology drawings. 

Firewalls that will be using during this lab
![Pasted image 20240313132301.png](img/Pasted image 20240313132301.png)

### HUB - NGFW1
1. Interfaces
![Pasted image 20240315113853.png](img/Pasted image 20240315113853.png)

2. Static Route - HUB has a default route to internet and static routes to PE router to reach branches external IPs
![Pasted image 20240313133447.png](img/Pasted image 20240313133447.png)

3. ECMP to reach spokes, since HUB has 2 links
![Pasted image 20240313133619.png](img/Pasted image 20240313133619.png)

### Branch1

1. Interfaces
![Pasted image 20240401002428.png](img/Pasted image 20240401002428.png)

2. Static route (vrf ext)
![Pasted image 20240313145914.png](img/Pasted image 20240313145914.png)

3. ECMP (vrf ext)
![Pasted image 20240313145409.png](img/Pasted image 20240313145409.png)

### Branch2

1. Interfaces
![Pasted image 20240401002750.png](img/Pasted image 20240401002750.png)

2. Static route
![Pasted image 20240313145747.png](img/Pasted image 20240313145747.png)

3. ECMP
![Pasted image 20240401003015.png](img/Pasted image 20240401003015.png)

### Branch3

1. Interfaces
![Pasted image 20240401003119.png](img/Pasted image 20240401003119.png)

2. Static route
![Pasted image 20240313150200.png](img/Pasted image 20240313150200.png)

3. ECMP - N/A because Branch3 has only 1 WAN interface

## Interface configuration

### Hub (NGFW1) - Configuring two DVTI interfaces to use with two WAN links

| **Note:** | HUB will contain two loopback interfaces, one per topology. SDWAN topology is, in simple words, created per WAN links at the HUB. It can also be created for other reasons |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

Hub will have two loopbacks because it has 2 links. They will be addressed as CIDR /29 to accommodate 1 hub and 3 spokes inside the same network. Their addresses will be: 
- Loop10: 169.254.100.0/29 where the HUB is the first valid IP inside the block
- Loop11: 169.254.100.8/29 where the HUB is the first valid IP inside the block

#### Connecting to the lab
1. Open **jumpbox** by clicking on WEB_RDP link inside the section **How to Connect to the Lab Environment**

![Pasted image 20240407121811.png](img/Pasted image 20240407121811.png)

2. You will be presented with the **Quick Launch** page as soon as you are logged in. In case you are not presented with this page or you accidentally close during your lab, go to **Desktop** and double-click on this icon  
![Pasted image 20240407121516.png](img/Pasted image 20240407121516.png)

3. ​You will be presented with the following page  that contains most of the access you will need to complete this lab
![Pasted image 20240407121529.png](img/Pasted image 20240407121529.png)

#### VPN1 - Interface outside - using Loop10 169.254.100.0/29.

1. Go to **Devices > Device Management**. Find **NGFW1** and click on the pencil to **edit** it  
2. Inside **Interfaces** tab, click on **Add Interfaces > Loopback Interface**
<br>![Pasted image 20240401185132 1.png](img/Pasted image 20240401185132 1.png)

3. Fill out name and Loopback ID as per image under **General** tab. 
<br>![Pasted image 20240402112819.png](img/Pasted image 20240402112819.png)

4. Go to **IPv4** tab and fill out IP Address. Click **Ok** to finish.
<br>![Pasted image 20240402113017.png](img/Pasted image 20240402113017.png)

5. Create a **Dynamic** Virtual Tunnel Interface (DVTI) using Loop10 IP address we just created. 
	- Create a separate Security Zone for Static/Dynamic VTIs. We will be using Zone **VTI** here. If you don't have it, you need to create under this page
	- Tunnel source was left unchecked because it will be chosen when creating the SDWAN topology, so no need to do this here
	- Use meaningful names so you know which physical and which loopback you are using on your DVTIs or SVTIs
6. Go to **Add Interfaces** again and select **Virtual Tunnel Interface**
<br>![Pasted image 20240401185132 1.png](img/Pasted image 20240401185132 1.png)

7. There are some fields to fill out:
	1. Tunnel Type: **Dynamic**. This is the default when configuring the HUB. Branches will be of Static type
	2. Name: **DVTI_outside_Loop10**
	3. Security Zone: **VTI**.
	4. Template ID: select a number to represent this DVTI. We will use **10** to match our loopback number
	5. Tunnel Source: leave it blank, we will select the source while creating the topology
	6. IP address: we will use **IP unnumbered** with the IP of the Loopback **Loop10** we created before

![Pasted image 20240402114356.png](img/Pasted image 20240402114356.png)

| Note: | If you do not have VTI Security Zone, you can create it from here                        |
| ----- | ---------------------------------------------------------------------------------------- |
|       | ![Pasted image 20240401190107 1.png](img/Pasted image 20240401190107 1.png)<br><br>![Pasted image 20240401190153 1.png](img/Pasted image 20240401190107 1.png)<br><br>![Pasted image 20240401190153 1.png](img/Pasted image 20240401190153 1.png)<br> |

<br>![Pasted image 20240402114608.png](img/Pasted image 20240402114608.png)

![Pasted image 20240402114620.png](img/Pasted image 20240402114620.png)

8. Click **Ok** to create the DVTI interface

#### VPN2 - Interface in10 - using Loop11 169.254.100.8/29.

1. Go to **Devices > Device Management**. Find **NGFW1** and click on the pencil to **edit** it  
2. Inside **Interfaces** tab, click on **Add Interfaces > Loopback Interface**
<br>![Pasted image 20240401185132 1.png](img/Pasted image 20240401185132 1.png)

3. Fill out name and Loopback ID as per image under **General** tab. 
<br>![Pasted image 20240402115526.png](img/Pasted image 20240402115526.png)

4. Go to **IPv4** tab and fill out IP Address. Click **Ok** to finish.
<br>![Pasted image 20240402115019.png](img/Pasted image 20240402115019.png)

5. Create a **Dynamic** Virtual Tunnel Interface (DVTI) using Loop11 IP address we just created. 
	- Since we have already created the **VTI** Security Zone before, then we will be using it.
	- Tunnel source was left unchecked because it will be chosen when creating the SDWAN topology, so no need to do this here
	- Use meaningful names so you know which physical and which loopback you are using on your DVTIs or SVTIs
6. Go to **Add Interfaces** again and select **Virtual Tunnel Interface**
<br>![Pasted image 20240401185132 1.png](img/Pasted image 20240401185132 1.png)

7. There are some fields to fill out:
	1. Tunnel Type: **Dynamic**. This is the default when configuring the HUB. Branches will be of Static type
	2. Name: **DVTI_in10_Loop11**
	3. Security Zone: **VTI**.
	4. Template ID: select a number to represent this DVTI. We will use **11** to match our loopback number
	5. Tunnel Source: leave it blank, we will select the source while creating the topology
	6. IP address: we will use **IP unnumbered** with the IP of the Loopback **Loop11** we created before

![Pasted image 20240402115237.png](img/Pasted image 20240402115237.png)

![Pasted image 20240402115319.png](img/Pasted image 20240402115319.png)

![Pasted image 20240402120348.png](img/Pasted image 20240402120348.png)

8. Click **Ok** to create the DVTI interface
9. Click **Save**
<br>![Pasted image 20240402120519.png](img/Pasted image 20240402120519.png)

Interfaces overview:
![Pasted image 20240402120639.png](img/Pasted image 20240402120639.png)

### Branch3 - Configuring two SVTI interfaces to connect with the two HUB links

1. Go to **Devices > Device Management**. Find **Branch3** and click on the pencil to **edit** it  
2. Inside **Interfaces** tab, click on **Add Interfaces** **> Loopback Interface**
<br>![Pasted image 20240401185132 1.png](img/Pasted image 20240401185132 1.png)

3. Fill out name and Loopback ID as per image under **General** tab. 
<br>![Pasted image 20240402121319.png](img/Pasted image 20240402121319.png)

4. Go to **IPv4** tab and fill out IP Address. Click **Ok** to finish.
<br>![Pasted image 20240402121342.png](img/Pasted image 20240402121342.png)

5. Create the second Loopback interface, use the same steps as before
<br>![Pasted image 20240402122705.png](img/Pasted image 20240402122705.png)

![Pasted image 20240402122723.png](img/Pasted image 20240402122723.png)

6. Create a **Static** Virtual Tunnel Interface (SVTI) using Loop10 IP address we just created. 
	- Since we have already created the **VTI** Security Zone before, then we don't need to create again.
	- Use meaningful names so you know which physical and which loopback you are using on your DVTIs or SVTIs
7. Go to **Add Interfaces** again and select **Virtual Tunnel Interface**
<br>![Pasted image 20240401185132 1.png](img/Pasted image 20240401185132 1.png)

8. There are some fields to fill out:
	1. Tunnel Type: **Static**. This is the default when configuring the Branches.
	2. Name: **SVTI_outside20_Loop10**
	3. Security Zone: **VTI**
	4. Tunnel ID: select a number to represent this SVTI. We will use **10** to match our loopback number
	5. Tunnel Source: select the outside interface as it will be the only one reach out to HUB. Branch3 has only one WAN interface, which is **outside_20**
	6. IP address: we will use **IP unnumbered** with the IP of the Loopback **Loop10** we created before

![Pasted image 20240402122025.png](img/Pasted image 20240402122025.png)

![Pasted image 20240402122058.png](img/Pasted image 20240402122058.png)

9. Go to **Add Interfaces** again and select **Virtual Tunnel Interface**
<br>![Pasted image 20240401185132 1.png](img/Pasted image 20240401185132 1.png)

10. There are some fields to fill out:
	1. Tunnel Type: **Static**. This is the default when configuring the Branches.
	2. Name: **SVTI_outside20_Loop11**
	3. Security Zone: **VTI**
	4. Tunnel ID: select a number to represent this SVTI. We will use **11** to match our loopback number
	5. Tunnel Source: select the outside interface as it will be the only one reach out to HUB. Branch3 has only one WAN interface, which is **outside_20**
	6. IP address: we will use **IP unnumbered** with the IP of the Loopback **Loop11** we created before

![Pasted image 20240402123042.png](img/Pasted image 20240402123042.png)

![Pasted image 20240402123110.png](img/Pasted image 20240402123110.png)

11. Click **Ok** and then Save the configuration
<br>![Pasted image 20240402122136.png](img/Pasted image 20240402122136.png)

Interfaces overview:
![Pasted image 20240402123150.png](img/Pasted image 20240402123150.png)

### Branch2 - Configuring two SVTI interfaces to connect with the two HUB links

1. Go to **Devices > Device Management**. Find **Branch2** and click on the pencil to **edit** it  
2. Inside **Interfaces** tab, click on **Add Interfaces** **> Loopback Interface**
<br>![Pasted image 20240401185132 1.png](img/Pasted image 20240401185132 1.png)

3. Fill out name and Loopback ID as per image under **General** tab. 
<br>![Pasted image 20240402121319.png](img/Pasted image 20240402121319.png)

4. Go to **IPv4** tab and fill out IP Address. Click **Ok** to finish.
<br>![Pasted image 20240402123355.png](img/Pasted image 20240402123355.png)

5. Create the second Loopback interface, use the same steps as before
<br>![Pasted image 20240402122705.png](img/Pasted image 20240402122705.png)

![Pasted image 20240402124245.png](img/Pasted image 20240402124245.png)

6. Create a **Static** Virtual Tunnel Interface (SVTI) using Loop10 IP address we just created. 
	- Since we have already created the **VTI** Security Zone before, then we will be using it.
	- Use meaningful names so you know which physical and which loopback you are using on your DVTIs or SVTIs
7. Go to **Add Interfaces** again and select **Virtual Tunnel Interface**
<br>![Pasted image 20240401185132 1.png](img/Pasted image 20240401185132 1.png)

8. There are some fields to fill out:
	1. Tunnel Type: **Static**. This is the default when configuring the Branches.
	2. Name: **SVTI_outside_gi3_40_Loop10**
	3. Security Zone: **VTI**
	4. Tunnel ID: select a number to represent this SVTI. We will use **10** to match our loopback number
	5. Tunnel Source: select Gi0/3 (**outside_gi3_40**) as it will be the first interface we will be using to reach out to HUB. Branch2 has two WAN interfaces, we will create one SVTI per WAN interface in this lab.
	6. IP address: we will use **IP unnumbered** with the IP of the Loopback **Loop10** we created before

![Pasted image 20240402124048.png](img/Pasted image 20240402124048.png)

![Pasted image 20240402124829.png](img/Pasted image 20240402124829.png)

9. Go to **Add Interfaces** again and select **Virtual Tunnel Interface**
<br>![Pasted image 20240401185132 1.png](img/Pasted image 20240401185132 1.png)

10. There are some fields to fill out:
	1. Tunnel Type: **Static**. This is the default when configuring the Branches.
	2. Name: **SVTI_outside_gi4_50_Loop11**
	3. Security Zone: **VTI**
	4. Tunnel ID: select a number to represent this SVTI. We will use **11** to match our loopback number
	5. Tunnel Source: select Gi0/4 (**outside_gi4_50**) as it will be the second interface we will be using to reach out to HUB. Branch2 has two WAN interfaces, we will create one SVTI per WAN interface in this lab.
	6. IP address: we will use **IP unnumbered** with the IP of the Loopback **Loop11** we created before

![Pasted image 20240402124606.png](img/Pasted image 20240402124606.png)

![Pasted image 20240402124623.png](img/Pasted image 20240402124623.png)

11. Click **Ok** and then Save the configuration
<br>![Pasted image 20240402122136.png](img/Pasted image 20240402122136.png)

Interfaces overview:
![Pasted image 20240402130647.png](img/Pasted image 20240402130647.png)

### Branch1 - Configuring two SVTI interfaces to connect with the two HUB links

1. Go to **Devices > Device Management**. Find **Branch1** and click on the pencil to **edit** it  
2. Inside **Interfaces** tab, click on **Add Interfaces** **> Loopback Interface**
<br>![Pasted image 20240401185132 1.png](img/Pasted image 20240401185132 1.png)

3. Fill out name and Loopback ID as per image under **General** tab. 
<br>![Pasted image 20240402121319.png](img/Pasted image 20240402121319.png)

4. Go to **IPv4** tab and fill out IP Address. Click **Ok** to finish.
<br>![Pasted image 20240402125156.png](img/Pasted image 20240402125156.png)

5. Create the second Loopback interface, use the same steps as before
<br>![Pasted image 20240402122705.png](img/Pasted image 20240402122705.png)

![Pasted image 20240402125232.png](img/Pasted image 20240402125232.png)

6. Create a **Static** Virtual Tunnel Interface (SVTI) using Loop10 IP address we just created. 
	- Since we have already created the **VTI** Security Zone before, then we will be using it.
	- Use meaningful names so you know which physical and which loopback you are using on your DVTIs or SVTIs
7. Go to **Add Interfaces** again and select **Virtual Tunnel Interface**
<br>![Pasted image 20240401185132 1.png](img/Pasted image 20240401185132 1.png)

8. There are some fields to fill out:
	1. Tunnel Type: **Static**. This is the default when configuring the Branches.
	2. Name: **SVTI_outside_gi3_40_Loop10**
	3. Security Zone: **VTI**
	4. Tunnel ID: select a number to represent this SVTI. We will use **10** to match our loopback number
	5. Tunnel Source: select Gi0/3 (**outside_gi3_40**) as it will be the first interface we will be using to reach out to HUB. Branch1 has two WAN interfaces, we will create one SVTI per WAN interface in this lab.
	6. IP address: we will use **IP unnumbered** with the IP of the Loopback **Loop10** we created before

![Pasted image 20240402124048.png](img/Pasted image 20240402124048.png)

![Pasted image 20240402125414.png](img/Pasted image 20240402125414.png)

9. Go to **Add Interfaces** again and select **Virtual Tunnel Interface**
<br>![Pasted image 20240401185132 1.png](img/Pasted image 20240401185132 1.png)

10. There are some fields to fill out:
	1. Tunnel Type: **Static**. This is the default when configuring the Branches.
	2. Name: **SVTI_outside_gi4_30_Loop11**
	3. Security Zone: **VTI**
	4. Tunnel ID: select a number to represent this SVTI. We will use **11** to match our loopback number
	5. Tunnel Source: select Gi0/4 (**outside_gi4_30**) as it will be the second interface we will be using to reach out to HUB. Branch1 has two WAN interfaces, we will create one SVTI per WAN interface in this lab.
	6. IP address: we will use **IP unnumbered** with the IP of the Loopback **Loop11** we created before

![Pasted image 20240402125822.png](img/Pasted image 20240402125822.png)

![Pasted image 20240402125957.png](img/Pasted image 20240402125957.png)

11. Click **Ok**
12. Click **Ok** and then Save the configuration
<br>![Pasted image 20240402122136.png](img/Pasted image 20240402122136.png)

Interfaces overview:
![Pasted image 20240402130718.png](img/Pasted image 20240402130718.png)

## Routing and ECMP configuration

Based on the following topology, configure the routing protocols on the HUB and Branches.
![Pasted image 20240313182137.png](img/Pasted image 20240313182137.png)

### HUB and Branch3 - OSPF

This will be a basic OSPF configuration where HUB will announce the network **Corporate4** (198.19.40.0/24) and Branch3 will announce **198.60.30.0/24**

#### HUB (NGFW1) configuration

1. Go to **Devices > Device Management**. Find **NGFW1** and click on the pencil to **edit** it  
2. Click on **Routing** tab and then click on **OSPF** option
<br>![Pasted image 20240402145354.png](img/Pasted image 20240402145354.png)

3. We need to enable OSPF process, click on **Process 1**
![Pasted image 20240402145504.png](img/Pasted image 20240402145504.png)

4. Under **Area** tab, click **Add** to add a new Area
<br>![Pasted image 20240402145556.png](img/Pasted image 20240402145556.png)

5. When configuring the area, we will choose **Area ID** 100
<br>![Pasted image 20240402145725.png](img/Pasted image 20240402145725.png)

6. Now we will select the networks that are part of OSPF. Since we do not have the objects yet, we will create them as follows: **Corporate4** (198.19.40.0/24) and **net_Loop10** (169.254.100.0/29) and **net_Loop11** (169.254.100.8/29). Click on the + (plus) sign to create the new objects.
	- Click Save and then click on the + (plus) sign again to create other objects
![Pasted image 20240402145948.png](img/Pasted image 20240402145948.png)

![Pasted image 20240403004346.png](img/Pasted image 20240403004346.png)

![Pasted image 20240402150528.png](img/Pasted image 20240402150528.png)

![Pasted image 20240402152053.png](img/Pasted image 20240402152053.png)

7. After creating the objects, they can be searched in the **Available Network** field. Search for **Corporate4**,  **net_Loop10**, and **net_Loop11**. Then click **Add** to add them to **Selected Network**. Click **Ok** to finish.
![Pasted image 20240402150834.png](img/Pasted image 20240402150834.png)

![Pasted image 20240402153014.png](img/Pasted image 20240402153014.png)

![Pasted image 20240402153150.png](img/Pasted image 20240402153150.png)

8. Click Ok and then Save
<br>![Pasted image 20240402151013.png](img/Pasted image 20240402151013.png)

#### Branch3 configuration

9. Let's jump to **Branch3** and do the same. 
10. Go to **Devices > Device Management**. Find **Branch3** and click on the pencil to **edit** it  
11. Click on **Routing** tab and then click on **OSPF** option
<br>![Pasted image 20240402151514.png](img/Pasted image 20240402151514.png)

12. We need to enable OSPF process, click on **Process 1**
![Pasted image 20240402145504.png](img/Pasted image 20240402145504.png)

13. Under **Area** tab, click **Add** to add a new Area
<br>![Pasted image 20240402145556.png](img/Pasted image 20240402145556.png)

14. When configuring the area, we will choose **Area ID** 100
<br>![Pasted image 20240402145725.png](img/Pasted image 20240402145725.png)

15. Now we will select the networks that are part of OSPF. Since we do not have the object for Branch3 network yet, we will create it as follows: **net_Branch3** (198.60.30.0/24). Click on the + (plus) sign to create a new object.
	- Click Save to create the object
![Pasted image 20240402145948.png](img/Pasted image 20240402145948.png)

![Pasted image 20240402151755.png](img/Pasted image 20240402151755.png)

16. After creating the object, they can be searched in the **Available Network** field. Search for **net_Branch3**,  **net_Loop10**, and **net_Loop11**. Then click **Add** to add them to **Selected Network**. Click **Ok** to finish.
![Pasted image 20240402150834.png](img/Pasted image 20240402150834.png)

![Pasted image 20240402153014.png](img/Pasted image 20240402153014.png)

![Pasted image 20240402153031.png](img/Pasted image 20240402153031.png)

17. Click Ok
18. Still under **Routing** tab, click on **ECMP** menu
<br>![Pasted image 20240402233120.png](img/Pasted image 20240402233120.png)

19. Click **Add** to add a new ECMP zone.
20. Name the zone **ECMP-VTI**, select both SVTI and add them.
![Pasted image 20240402233251.png](img/Pasted image 20240402233251.png)

21. Click Ok and then Save
<br>![Pasted image 20240402151013.png](img/Pasted image 20240402151013.png)

### HUB and Branch2 - EIGRP

#### HUB (NGFW1) configuration

1. Go to **Devices > Device Management**. Find **NGFW1** and click on the pencil to **edit** it  
2. Click on **Routing** tab and then click on **EIGRP** option
<br>![Pasted image 20240402172930.png](img/Pasted image 20240402172930.png)

3. We need to enable EIGRP process, click on **Enable** and pick an AS Number. We will use AS Number **10**
![Pasted image 20240402173039.png](img/Pasted image 20240402173039.png)

4. Now we will select the networks that are part of EIGRP. Since we do not have the object yet, we will create them as follows: **Corporate3** (198.19.30.0/24). For Loop10 and Loop11 we have already created the objects in the previous task, when we configured OSFP. Click on the + (plus) sign to create the new objects.
	- Click Save
![Pasted image 20240402145948.png](img/Pasted image 20240402145948.png)

![Pasted image 20240402173341.png](img/Pasted image 20240402173341.png)

5. After creating the objects, they can be searched in the **Available Network** field. Search for **Corporate3**,  **net_Loop10**, and **net_Loop11**. Then click **Add** to add them to **Selected Network**. Click **Ok** to finish.
![Pasted image 20240407150922.png](img/Pasted image 20240407150922.png)

6. Click Ok and then Save
<br>![Pasted image 20240402151013.png](img/Pasted image 20240402151013.png)

#### Branch2 configuration

7. Let's jump to **Branch2** and do the same. 
8. Go to **Devices > Device Management**. Find **Branch2** and click on the pencil to **edit** it  
9. Click on **Routing** tab and then click on **EIGRP** option
<br>![Pasted image 20240402173622.png](img/Pasted image 20240402173622.png)

10. We need to enable EIGRP process, click on **Enable** and pick an AS Number. We will use AS Number **10**
![Pasted image 20240402173039.png](img/Pasted image 20240402173039.png)

11. Now we will select the networks that are part of EIGRP. Since we do not have the Branch2 object yet, we will create it as follows: **net_Branch2** (198.60.20/24). The objects for Loop10 and Loop11 have been already created in the previous task, when we configured OSFP. Click on the + (plus) sign to create the new object.
	- Click Save
![Pasted image 20240402145948.png](img/Pasted image 20240402145948.png)

![Pasted image 20240402173907.png](img/Pasted image 20240402173907.png)

12. After creating the object, they can be searched in the **Available Network** field. Search for **net_Branch2**,  **net_Loop10**, and **net_Loop11**. Then click **Add** to add them to **Selected Network**. Click **Ok** to finish.
<br>![Pasted image 20240402180015.png](img/Pasted image 20240402180015.png)

13. Click Ok
14. Still under **Routing** tab, click on **ECMP** menu
<br>![Pasted image 20240402233441.png](img/Pasted image 20240402233441.png)

15. Click **Add** to add a new ECMP zone.
16. Name the zone **ECMP-VTI**, select both SVTI and add them.
![Pasted image 20240402233543.png](img/Pasted image 20240402233543.png)

17. Click Ok and then Save
<br>![Pasted image 20240402151013.png](img/Pasted image 20240402151013.png)

### HUB and Branch1 - BGP

Branch1 will be able to receive a default route from BGP because it has been configured with a **front-door VRF**. You can check this configuration under **Routing** tab
![Pasted image 20240402183253.png](img/Pasted image 20240402183253.png)

The VRF is called **ext** and it has the two WAN links. When we select the VRF, we can see the two default routes going to the local ISP
![Pasted image 20240402183406.png](img/Pasted image 20240402183406.png)

So, in order to avoid routing problem where Firepower has default routes to ISPs and also receives default routes via BGP, we need to create a front-door VRF. Gladly, Firepower doesn't require you to know how to leak routes, it does everything for you.

#### HUB (NGFW1) configuration

1. Go to **Devices > Device Management**. Find **NGFW1** and click on the pencil to **edit** it
2. Click on **Routing** tab, click on **BGP** under General Settings at the bottom of the right hand side.
<br>![Pasted image 20240402180739.png](img/Pasted image 20240402180739.png)

4. Click on **Enable BGP** checkbox and type the AS Number. Private AS starts at 65000, so this will be our choice
![Pasted image 20240402181521.png](img/Pasted image 20240402181521.png)

4. Click on **BGP > IPv4** on the left hand side
<br>![Pasted image 20240402181707.png](img/Pasted image 20240402181707.png)

5. Click on **Enable IPv4** option
<br>![Pasted image 20240402181750.png](img/Pasted image 20240402181750.png)

6. Under **General** tab, edit the options below
	1. Forward Packets Over Multiple Paths
		- Number of Paths: 2
		- iBGP number of paths: 2
<br>![Pasted image 20240402181945.png](img/Pasted image 20240402181945.png)

7. Under **Neighbor** tab, click **Add** to add a new neighbor
![Pasted image 20240402145556.png](img/Pasted image 20240402145556.png)

8. We will add two neighbors, one per Branch1 WAN interface. Since each WAN will have a SVTI, we can become neighbor in each interface. We will fill out the following fields
	1. IP Address: 169.254.100.6 (Branch1 SVTI over gi3)
	2. Remote AS: 65000 (same for both)
	3. Check the option **Enable address**
	4. Under **Routes** tab, check the option **Generate default routes**
	5. Under **Advanced** tab, check the option **Use itself as next hop for this neighbor**
	6. Click **Ok**
![Pasted image 20240402182610.png](img/Pasted image 20240402182610.png)

![Pasted image 20240402182620.png](img/Pasted image 20240402182620.png)

![Pasted image 20240402182636.png](img/Pasted image 20240402182636.png)

9. Now we will do the same steps again for the other SVTI to Branch1.
	1. IP Address: 169.254.100.14 (Branch1 SVTI over gi4)
	2. Remote AS: 65000 (same for both)
	3. Check the option **Enable address**
	4. Under **Routes** tab, check the option **Generate default routes**
	5. Under **Advanced** tab, check the option **Use itself as next hop for this neighbor**
	6. Click **Ok**
<br>![Pasted image 20240402182828.png](img/Pasted image 20240402182828.png)

![Pasted image 20240402182836.png](img/Pasted image 20240402182836.png)

![Pasted image 20240402182851.png](img/Pasted image 20240402182851.png)

10. Click Ok and then Save
<br>![Pasted image 20240402151013.png](img/Pasted image 20240402151013.png)

#### Branch1 configuration

1. Go to **Devices > Device Management**. Find **Branch1** and click on the pencil to **edit** it
2. Click on **Routing** tab, click on **BGP** under General Settings at the bottom of the right hand side. Make sure you are NOT in the VRF but in the **Global** table. You can easily spot this looking at under **Manage Virtual Routers** where it says "Global"
<br>![Pasted image 20240402183812.png](img/Pasted image 20240402183812.png)

4. Click on **Enable BGP** checkbox and type the AS Number. Private AS starts at 65000, so this will be our choice
![Pasted image 20240402181521.png](img/Pasted image 20240402181521.png)

4. Click on **BGP > IPv4** on the left hand side
<br>![Pasted image 20240402183851.png](img/Pasted image 20240402183851.png)

5. Click on **Enable IPv4** option
<br>![Pasted image 20240402181750.png](img/Pasted image 20240402181750.png)

6. Under **General** tab, edit the options below
	1. Forward Packets Over Multiple Paths
		- Number of Paths: 2
		- iBGP number of paths: 2
<br>![Pasted image 20240402181945.png](img/Pasted image 20240402181945.png)

7. Under **Neighbor** tab, click **Add** to add a new neighbor
![Pasted image 20240402145556.png](img/Pasted image 20240402145556.png)

8. We will add two neighbors, one per HUB WAN interface. Since each Branch1 WAN has a SVTI, we can become neighbor in each interface. We will fill out the following fields
	1. IP Address: 169.254.100.1 (HUB DVTI over outside interface)
	2. Remote AS: 65000 (same for both)
	3. Check the option **Enable address**
	4. Click **Ok**

![Pasted image 20240402184204.png](img/Pasted image 20240402184204.png)

9. Now we will do the same steps again for the other SVTI to HUB.
	1. IP Address: 169.254.100.9 (HUB DVTI over in10)
	2. Remote AS: 65000 (same for both)
	3. Check the option **Enable address**
	4. Click **Ok**
<br>![Pasted image 20240402184316.png](img/Pasted image 20240402184316.png)

10. Under **Networks** tab, click the + (plus) sign to add a network to be advertised
11. Click on the drop down menu and select the object **Branch-LAN**
<br>![Pasted image 20240402225815.png](img/Pasted image 20240402225815.png)

12. Click Ok
13. Still under **Routing** tab, click on **ECMP** menu
<br>![Pasted image 20240402233659.png](img/Pasted image 20240402233659.png)

14. Click **Add** to add a new ECMP zone.
15. Name the zone **ECMP-VTI**, select both SVTI and add them.
![Pasted image 20240402233724.png](img/Pasted image 20240402233724.png)

16. Click Ok and then Save
<br>![Pasted image 20240402151013.png](img/Pasted image 20240402151013.png)

## Direct Internet Access (DIA) configuration

- DIA is a feature that gives the branch local internet access without going to HUB for internet or SaaS services. 
- Branch2 and Branch3 only receive specific routes from HUB via their respective dynamic routing protocols. With that, the default route prevails and they already send internet traffic to their local ISP.
- Branch1 by the other hand, it receives and default route via BGP, meaning, it will send everything to HUB. In this case we can leverage DIA to send some applications to exit via local ISPs, balanced by their dual ISP approach.

#### Branch1 - Default routes to WAN providers - VRF ext

1. Under **Routing** tab, click on **Manage Virtual Routers**
![Pasted image 20240402183253.png](img/Pasted image 20240402183253.png)

2. Select **ext** using the drop down menu.
<br>![Pasted image 20240402185535.png](img/Pasted image 20240402185535.png)

3. VRF is called **ext**. Click on **Static route** to see Branch1 two default route to local ISPs. 
![Pasted image 20240402183406.png](img/Pasted image 20240402183406.png)

4. Click on **ECMP** to see the load-balancing on these two links
![Pasted image 20240402185725.png](img/Pasted image 20240402185725.png)

#### Branch1 - Static route - Global table

As we mentioned, Branch1 will receive a default route via BGP when tunnel is up. This default route will be installed in the global routing table which will not conflict with ISP routes installed at VRF ext

1. Click on **Manage Virtual Routes** again and now select Global
<br>![Pasted image 20240402190055.png](img/Pasted image 20240402190055.png)

2. Now click on **Static Route**. Notice that there aren't any routes configured since it will receive via BGP
![Pasted image 20240402190142.png](img/Pasted image 20240402190142.png)

#### Enabling Path Monitoring

Path monitoring, when configured on interfaces, derive metrics such as round trip time (RTT), jitter, mean opinion score (MOS), and packet loss per interface. These metrics are used to determine the best path for routing PBR traffic.

ICMP-based Path Monitoring
- The metrics on the interfaces are collected dynamically using ICMP probe messages to the interface's default gateway or a specified remote peer.
HTTP-based Path Monitoring
- Path monitoring computes flexible metrics for multiple remote peers per interface. To monitor and determine best path for multiple applications through a policy on a branch firewall, HTTP is preferred over ICMP for the following reasons:
	- HTTP-ping can derive the performance metrics of the path up to the application layer of the server, where the application is hosted.
	- The need to change the firewall configuration whenever the application server IP address is changed is removed as the application domain is tracked instead of the IP address.
	

1. Edit Branch1, under **Interfaces** tab, we will select both WAN interfaces to enable Path Monitoring.
2. Select Gi0/3 and click on the pencil to edit it
3. Click on **Path Monitoring** to enable the metrics over ICMP and HTTP. We will enable both in this lab
4. Click **Ok**
<br>![Pasted image 20240402192140.png](img/Pasted image 20240402192140.png)

5. Select Gi0/4 and click on the pencil to edit it
6. Click on **Path Monitoring** to enable the metrics over ICMP and HTTP. We will enable both in this lab
![Pasted image 20240402192140.png](img/Pasted image 20240402192140.png)

#### Enabling PBR

PBR and Path Monitoring
- Typically, in PBR, traffic is forwarded through egress interfaces based on the priority value (interface cost) configured on them. From management center version 7.2, PBR uses IP-based path monitoring to collect the performance metrics (RTT, jitter, packet-lost, and MOS) of the egress interfaces. PBR uses the metrics to determine the best path (egress interface) for forwarding the traffic. Path monitoring periodically notifies PBR about the monitored interface whose metric got changed. PBR retrieves the latest metric values for the monitored interfaces from the path monitoring database and updates the data path.

PBR and HTTP-based Path Monitoring
- From management center version 7.4, PBR can be configured to use HTTP-based path monitoring to collect the performance metrics of the application domains and not just one destination IP address. Path monitoring does not commence monitoring immediately after HTTP-based application monitoring is configured. It starts monitoring only when a DNS entry is snooped for a domain. With the information on the resolved IP for the domain, it sends and receives the HTTP request and response respectively. When DNS resolves multiple IP addresses for a single domain, the first resolved IP address will be used for probing and monitoring the application. It continues to monitor till the IP address changes or the HTTP-based path monitoring is disabled.

1. Now, under **Routing** tab, inside Global routing table, select **Policy Based Routing**
<br>![Pasted image 20240402195251.png](img/Pasted image 20240402195251.png)

2. Click **Configure Interface Priority** to specify if there is any preferred interface over the other. Set both WAN interfaces with priority **10** so they can load-balance between them.
3. Click Save.
4. Create a new PBR policy by clicking on Add button
<br>![Pasted image 20240402195649.png](img/Pasted image 20240402195649.png)

5. Select which interface the users will be coming from. In our case, interface inside
![Pasted image 20240402195734.png](img/Pasted image 20240402195734.png)

6. Click Add again
7. In this page, we will configure the following
	1. **Match ACL**: this is the applications we want to enable DIA. Create an ACL called **Social-Networking** and add social network applications such as LinkedIn, Facebook and Instagram
	2. **Send to**: which interface you want to send this traffic that matches the ACL. This can be either an IP or Egress interface. Select **Egress Interfaces** 
	3. **Interface Ordering**: how we are going to choose which interface to send the traffic to. Select **Interface Priority**. Just for documentation purpose, here are the options:
		- By Interface Priority—The traffic is forwarded based on the priority of the interfaces. Traffic is routed to the interface with the least priority value first. When the interface is not available, the traffic is then forwarded to the interface with the next lowest priority value
		- By Order—The traffic is forwarded based on the sequence of the interfaces specified here
		- By Minimal Jitter—The traffic is forwarded to the interface that has the lowest jitter value
		- By Maximum Mean Opinion Score—The traffic is forwarded to the interface that has the maximum mean opinion score (MOS)
		- By Minimal Round Trip Time—The traffic is forwarded to the interface that has the minimal round trip time (RTT)
		- By Minimal Packet Loss—The traffic is forwarded to the interface that has the minimal packet loss. You need to enable Path Monitoring on the interfaces for PBR to obtain the packet loss values.
	
8. To create an ACL, click on the + (plus) sign next to Match ACL field.
9. Give it a name and click Add
![Pasted image 20240402200751.png](img/Pasted image 20240402200751.png)

10. Keep the defaults. Under **Application** tab, select the applications we mentioned above. 
11. Click **Add** then click **Save**
![Pasted image 20240402200856.png](img/Pasted image 20240402200856.png)

![Pasted image 20240402200942.png](img/Pasted image 20240402200942.png)

12. Click **Save** again
![Pasted image 20240402201031.png](img/Pasted image 20240402201031.png)

13. Click **Save** to finish the process
![Pasted image 20240402201111.png](img/Pasted image 20240402201111.png)

14. Click **Save** to save the policy
<br>![Pasted image 20240402201204.png](img/Pasted image 20240402201204.png)

| **Note:** | We can monitor the behavior by debugging the decisions using the CLI command ```debug policy-route``` |
| --------- | ----------------------------------------------------------------------------------------------------- |

## SDWAN configuration

With all the previous steps having been completed, we are ready to configure SDWAN topologies. 

1. Go to **Devices > Site to Site** menu
<br>![Pasted image 20240402202229.png](img/Pasted image 20240402202229.png)

2. Click on **Site to Site VPN** button
<br>![Pasted image 20240402202247.png](img/Pasted image 20240402202247.png)

### SDWAN Topology 1
1. Type a **topology name**, select **Route Based VTI** and then select **Hub and Spoke** network topology
2. The topology name is important to remind us which Topology we are working with, in this case, the HUB DVTI will be using **outside** interface and **Loop10**
![Pasted image 20240402202811.png](img/Pasted image 20240402202811.png)

3. Under **Endpoints** tab, click the + (plus) sign to add **Hub nodes**
4. Now select the following:
	1. Device: **NGFW1**
	2. Dynamic Virtual Tunnel Interface (DVTI): **DVTI_outside_Loop10**
	3. Tunnel Source: **Giga0/0 (outside)**
	4. IP address: 198.18.133.81
	5. Click **Advanced Settings**
		- Check the option **Send Virtual Tunnel Interface IP to the peers**
	6. Click **Ok**
<br>![Pasted image 20240402203201.png](img/Pasted image 20240402203201.png)

5. Click on the + (plus) sign to add **Spoke Nodes**
6. Select the following:
	1. Device: **branch1**
	2. Static Virtual Tunnel Interface (SVTI): **SVTI_outside_gi3_40_Loop10**
	3. Click **Ok**
<br>![Pasted image 20240402203512.png](img/Pasted image 20240402203512.png)

7. Click on the + (plus) sign again to add **Spoke Nodes**
8. Select the following:
	1. Device: **branch2**
	2. Static Virtual Tunnel Interface (SVTI): **SVTI_outside_gi3_40_Loop10**
	3. Click **Ok**
<br>![Pasted image 20240402203642.png](img/Pasted image 20240402203642.png)

9. Click on the + (plus) sign again to add **Spoke Nodes**
10. Select the following:
	1. Device: **branch3**
	2. Static Virtual Tunnel Interface (SVTI): **SVTI_outside20_Loop10**
	3. Click **Ok**
<br>![Pasted image 20240402203720.png](img/Pasted image 20240402203720.png)

11. Under **IKE** tab, change **Authentication Type** to **Pre-shared Manual Key**
	- Type and confirm the key: C1sco12345
12. Click **Save**

SDWAN1 - Overall configuration
![Pasted image 20240402224225.png](img/Pasted image 20240402224225.png)

#### SDWAN Topology 2
1. Type a **topology name**, select **Route Based VTI** and then select **Hub and Spoke** network topology
2. The topology name is important to remind us which Topology we are working with, in this case, the HUB DVTI will be using **in10** interface and **Loop11**
![Pasted image 20240402223517.png](img/Pasted image 20240402223517.png)

3. Under **Endpoints** tab, click the + (plus) sign to add **Hub nodes**
4. Now select the following:
	1. Device: **NGFW1**
	2. Dynamic Virtual Tunnel Interface (DVTI): **DVTI_in10_Loop11**
	3. Tunnel Source: **Giga0/1 (in10)**
	4. IP address: 198.19.10.1
	5. Click **Advanced Settings**
		- Check the option **Send Virtual Tunnel Interface IP to the peers**
	6. Click **Ok**
<br>![Pasted image 20240402223907.png](img/Pasted image 20240402223907.png)

5. Click on the + (plus) sign to add **Spoke Nodes**
6. Select the following:
	1. Device: **branch1**
	2. Static Virtual Tunnel Interface (SVTI): **SVTI_outside_gi4_30_Loop11**
	3. Click **Ok**
<br>![Pasted image 20240402223948.png](img/Pasted image 20240402223948.png)

7. Click on the + (plus) sign again to add **Spoke Nodes**
8. Select the following:
	1. Device: **branch2**
	2. Static Virtual Tunnel Interface (SVTI): **SVTI_outside_gi4_50_Loop11**
	3. Click **Ok**
<br>![Pasted image 20240402224022.png](img/Pasted image 20240402224022.png)

9. Click on the + (plus) sign again to add **Spoke Nodes**
10. Select the following:
	1. Device: **branch3**
	2. Static Virtual Tunnel Interface (SVTI): **SVTI_outside20_Loop11**
	3. Click **Ok**
<br>![Pasted image 20240402224052.png](img/Pasted image 20240402224052.png)

11. Under **IKE** tab, change **Authentication Type** to **Pre-shared Manual Key**
	- Type and confirm the key: C1sco12345
12. Click **Save**

SDWAN2 - Overal configuration
![Pasted image 20240402224250.png](img/Pasted image 20240402224250.png)

## Deploy and Validation

### Deploy
1. Deploy the configuration to all devices
2. Click **Deploy** at the top right hand side
![Pasted image 20240402224437.png](img/Pasted image 20240402224437.png)

3. Click **Ignore Warnings** and click **Deploy all**
<br>![Pasted image 20240402224507.png](img/Pasted image 20240402224507.png)

4. Wait to see whether the deploy was completed
![Pasted image 20240402224734.png](img/Pasted image 20240402224734.png)

### Validation

Tests are based on this topology
![Pasted image 20240403004913.png](img/Pasted image 20240403004913.png)

1. Open Putty and SSH to the following IPs
	1. HUB (NGFW1): 198.19.10.81
		- username: admin / password: C1sco12345
	2. Branch1: 198.18.133.42
		- username: admin / password: C1sco12345
	3. Branch2: 198.19.10.82
		- username: admin / password: C1sco12345
	4. Branch3: 198.19.10.83
		- username: admin / password: C1sco12345

#### Validating Branch3 connections

1. Inside HUB (NGFW1) and Branch3, type the command ``show route ospf`` to see which routes have been advertised
2. HUB (NGFW1)
![Pasted image 20240403004707.png](img/Pasted image 20240403004707.png)

3. Branch3
![Pasted image 20240403004729.png](img/Pasted image 20240403004729.png)

4. Open **Quick Launch** and click **CSR-64** (topology reference as CSR4) 
![Pasted image 20240407112708.png](img/Pasted image 20240407112708.png)

5. Type the command ```debug ip icmp``` to see the ICMP messages
6. Now type the command ```ping vrf branch3 198.19.40.64``` to test connectivity from Branch3 to Corporate4. Follow output from ping from 198.60.30.64 to 198.19.40.64
![Pasted image 20240403005044.png](img/Pasted image 20240403005044.png)
#### Validating Branch2 connections

Inside HUB (NGFW1) and Branch2, type the command ``show route ospf`` to see which routes have been advertised
2. HUB (NGFW1)
![Pasted image 20240403005333.png](img/Pasted image 20240403005333.png)

3. Branch2
![Pasted image 20240403005426.png](img/Pasted image 20240403005426.png)

4. Open **Quick Launch** and click **CSR-61** (topology references as CSR1)
![Pasted image 20240407114517.png](img/Pasted image 20240407114517.png)

5. Type the command **debug ip icmp** to see the ICMP messages
6. Now type the command **ping vrf branch2 198.19.30.63** to test connectivity from Branch2 to Corporate3. Follow the output of ping from 198.60.20.61 to 198.19.30.63
![Pasted image 20240403005659.png](img/Pasted image 20240403005659.png)

#### Validating Branch1 connections

Inside HUB (NGFW1) and Branch1, type the command ``show route bgp`` to see which routes have been advertised
2. HUB (NGFW1)
![Pasted image 20240403005849.png](img/Pasted image 20240403005849.png)

3. Branch1
![Pasted image 20240403005913.png](img/Pasted image 20240403005913.png)

4. Open **Quick Launch** and click **Kali Inside Linux** (topology references as Kali)
![Pasted image 20240407114940.png](img/Pasted image 20240407114940.png)

5. Now type the command **ping 198.19.20.62** to test connectivity from Branch1 to Corporate2. Follow the output of ping from 198.19.11.200 to 198.19.20.62
![Pasted image 20240403010000.png](img/Pasted image 20240403010000.png)

6. SSH into branch1 (198.18.133.42 / admin / C1sco12345), type the command **debug policy-route** to see the PBR being hit and steering the traffic to the local exit ISP.  
7. Testing Path Monitoring, DIA and PBR by pinging one or all of the following domains from **Branch Office Linux**.  
```shell
ping facebook.com
ping instagram.com
ping linkedin.com
```

8. Output from ​Branch Office Linux after a ping
```
[root@branch ~]# ping facebook.com
PING facebook.com (31.13.80.36) 56(84) bytes of data.
64 bytes from edge-star-mini-shv-01-yyz1.facebook.com (31.13.80.36): icmp_seq=1 
```

9. Output from ​branch1 debug command
![Pasted image 20240403002843.png](img/Pasted image 20240403002843.png)

## SDWAN and WAN status/summary

1. Go to **Devices > Site to Site VPN** and expand the two SDWANs topology we created earlier. They should be green with 3 tunnels UP
2. SDWAN1 using HUB DVTI Loop10
![Pasted image 20240403010525.png](img/Pasted image 20240403010525.png)

3. SDWAN2 using HUB DVTI Loop11
![Pasted image 20240403010618.png](img/Pasted image 20240403010618.png)

4. Go to **Overview > SD-WAN Summary**. Overview tab will show some stats about SDWAN health
![Pasted image 20240403010704.png](img/Pasted image 20240403010704.png)

5. Also at the SD-WAN Summary, click on **Application Monitoring** tab. It will show you the stats of the interfaces we enabled **Path Monitoring** which was only on branch1
![Pasted image 20240403011202.png](img/Pasted image 20240403011202.png)

6. Go to **Overview > Site to Site VPN**
![Pasted image 20240403010841.png](img/Pasted image 20240403010841.png)

