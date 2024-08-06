---
title: (Cisco Secure Access) Tectorial From Zero to Hero 
author:
- "Bruno Fagioli (bgimenez@cisco)"
revision: 0
---

# (Cisco Secure Access) Tectorial from Zero to Hero

## Index
1. **Connect**
    1. End User Connectivity
    2. Network Connections
    3. Users and Groups
2. **Resources**
    1. Private Resources
    2. Internet and SaaS Resources
3. **Secure**
    1. Data Classification
    2. Endpoint Posture Profiles
    3. Web Profiles
    4. Data Loss Prevention Policy
    5. Access Policy
4. **Tests**
    1. Posture for VPN and ZTNA
    2. VPN on
    3. Web Access
5. **Monitor**
    1. ZTNA Client-Based
    2. ZTNA Browser-based
    3. Web access

## Secure Access
1. **Connect**
	1. End User Connectivity
		- Virtual Private Network: VPN Pools: **172.31.33.0/24** and **172.31.34.0/24**
		- Create new VPN
			- ![Pasted%20image%2020240218105035.png](img/Pasted%20image%2020240218105035.png)
			- ![Pasted%20image%2020240218105113.png](img/Pasted%20image%2020240218105113.png)
			- ![Pasted%20image%2020240218105411.png](img/Pasted%20image%2020240218105411.png)
			- ![Pasted%20image%2020240218105709.png](img/Pasted%20image%2020240218105709.png)
			- Picture name: **SSE_picture.jpg**
			- Single sign-on URL: **<< AssertionConsumeService >>**
			- Audience URI (img/SP Entity ID): **<< EntityDescriptor >>**
			- ![Pasted%20image%2020240218112441.png](img/Pasted%20image%2020240218112441.png)
			- ![Pasted%20image%2020240218111425.png](img/Pasted%20image%2020240218111425.png)
			- XML > save to a file Fagioli1_VPN_Okta.xml
			- Select which groups
			- ![Pasted%20image%2020240218115128.png](img/Pasted%20image%2020240218115128.png)
			- ![Pasted%20image%2020240218112722.png](img/Pasted%20image%2020240218112722.png)
			- ![Pasted%20image%2020240218112738.png](img/Pasted%20image%2020240218112738.png)
			- ![Pasted%20image%2020240218112802.png](img/Pasted%20image%2020240218112802.png)
			- ![Pasted%20image%2020240218120015.png](img/Pasted%20image%2020240218120015.png)
			- ![Pasted%20image%2020240218112820.png](img/Pasted%20image%2020240218112820.png)
			- ![Pasted%20image%2020240218115448.png](img/Pasted%20image%2020240218115448.png)
		- Download Cisco Secure Client and Profiles
			- Open Secure Access console inside Win10-TPM
			- ![Pasted%20image%2020240218113513.png](img/Pasted%20image%2020240218113513.png)
			- Add the profiles inside the folders
			- Install Secure Client
			- ![Pasted%20image%2020240218113932.png](img/Pasted%20image%2020240218113932.png)
			- Reboot
			- Test VPN Login
			- 
	2. Network Connections
		- Network Tunnel Groups
		- ![Pasted%20image%2020240218135728.png](img/Pasted%20image%2020240218135728.png)
		- ![Pasted%20image%2020240218101551.png](img/Pasted%20image%2020240218101551.png)
		- Passphrase: C1sco12345C1sco12345
		- Static Routing: 10.1.1.0/24
		- FMC configuration
			1. VPN S2S
				- ![Pasted%20image%2020240218102507.png](img/Pasted%20image%2020240218102507.png)
			2. Routing
				- Secure Access is always .1 IP address for VTI VPN-Based
				- VPN1: 172.31.33.0/24
				- VPN2: 172.31.34.0/24
				- VPN_CGNAT: 100.64.0.0/10
				- ![Pasted%20image%2020240218180918.png](img/Pasted%20image%2020240218180918.png)
				- ![Pasted%20image%2020240218131818.png](img/Pasted%20image%2020240218131818.png)
				- 
			4. ACP
				- Create a rule at the top
				- ![Pasted%20image%2020240218181014.png](img/Pasted%20image%2020240218181014.png)
	3. Users and Groups
		- Configuration Management
		-  ![Pasted%20image%2020240218123929.png](img/Pasted%20image%2020240218123929.png)
		- ![Pasted%20image%2020240218124030.png](img/Pasted%20image%2020240218124030.png)
		- ![Pasted%20image%2020240218124048.png](img/Pasted%20image%2020240218124048.png)
		- Single sign-on URL: << AssertionConsumerService >>
		- SP Entity ID: << EntityDescriptor : entityID : https:// >>
		- ![Pasted%20image%2020240218123638.png](img/Pasted%20image%2020240218123638.png)
		- ![Pasted%20image%2020240218124633.png](img/Pasted%20image%2020240218124633.png)
		- Picture name: **SSE_picture.jpg**
		- Enroll Win10 computer
2. **Resources**
	1. Private Resources
		- ![Pasted%20image%2020240218145219.png](img/Pasted%20image%2020240218145219.png)
		- ![Pasted%20image%2020240218145740.png](img/Pasted%20image%2020240218145740.png)
		- ![Pasted%20image%2020240218145815.png](img/Pasted%20image%2020240218145815.png)
		- Check ZTNA configuration on Win10
			- C:\\ProgramData\\Cisco\\Cisco Secure Client\\ZTA\\enrollments\cached_configs
		- ![Pasted%20image%2020240218142311.png](img/Pasted%20image%2020240218142311.png)
	2. Internet and SaaS Resources
		- ![Pasted%20image%2020240218143038.png](img/Pasted%20image%2020240218143038.png)
		- ![Pasted%20image%2020240218143115.png](img/Pasted%20image%2020240218143115.png)
		- 
3. **Secure**
	1. Data Classification
		- ![Pasted%20image%2020240218143530.png](img/Pasted%20image%2020240218143530.png)
	2. Endpoint Posture Profiles
		- Zero Trust Connection
			- ![Pasted%20image%2020240218143958.png](img/Pasted%20image%2020240218143958.png)
			- ![Pasted%20image%2020240218150418.png](img/Pasted%20image%2020240218150418.png)
			- 
		- VPN Connection
			- ![Pasted%20image%2020240218144033.png](img/Pasted%20image%2020240218144033.png)
			- ![Pasted%20image%2020240218151108.png](img/Pasted%20image%2020240218151108.png)
	3. Web Profiles
		- ![Pasted%20image%2020240218144122.png](img/Pasted%20image%2020240218144122.png)
	4. Data Loss Prevention Policy
		- ![Pasted%20image%2020240218144341.png](img/Pasted%20image%2020240218144341.png)
		- ![Pasted%20image%2020240218144353.png](img/Pasted%20image%2020240218144353.png)
		- ![Pasted%20image%2020240218144835.png](img/Pasted%20image%2020240218144835.png)
	5. Access Policy
		- ![Pasted%20image%2020240218150543.png](img/Pasted%20image%2020240218150543.png)
		- ![Pasted%20image%2020240218150505.png](img/Pasted%20image%2020240218150505.png)
		- ![Pasted%20image%2020240218150900.png](img/Pasted%20image%2020240218150900.png)
		- ![Pasted%20image%2020240218150914.png](img/Pasted%20image%2020240218150914.png)
		- ![Pasted%20image%2020240218150804.png](img/Pasted%20image%2020240218150804.png)
		- ![Pasted%20image%2020240218150958.png](img/Pasted%20image%2020240218150958.png)
4. **Tests**
	1. Posture for VPN and ZTNA
		- VPN
			- Check file testfile.txt exists in Desktop. It fails if it doesn't
		- ZTNA Client-based
			- SSH to 10.1.1.1 - it doesn't require firewall, it should work
			- HTTPS to 10.1.1.200 - Requires Firewall, it should fail otherwise
		- ZTNA Browser
			- Access clientless from other computer to https://webserver-8215246.ztna.sse.cisco.io/ - only Edge allowed, it should fail otherwise
	2. VPN on
		- Ping 10.1.1.1-126 - it should work
		- Ping 10.1.1.200 - it should fail, it is not published via VPN access
	3. Web Access
		- DLP - pastebin using creditcard - it should fail
		- Tinder - Application block - it should fail (img/on/off VPN) - since it is NOT full tunnel, always comes using Roaming client
		- Facebook - Content category block - since it is NOT full tunnel, always comes using Roaming client
5. **Monitor**
	1. ZTNA Client-Based
		- ![Pasted%20image%2020240218154109.png](img/Pasted%20image%2020240218154109.png)
		- ![Pasted%20image%2020240218154201.png](img/Pasted%20image%2020240218154201.png)
	2. ZTNA Browser-based
		- ![Pasted%20image%2020240218154248.png](img/Pasted%20image%2020240218154248.png)
	3. Web access
		- ![Pasted%20image%2020240218165057.png](img/Pasted%20image%2020240218165057.png)
		- ![Pasted%20image%2020240218165122.png](img/Pasted%20image%2020240218165122.png)
		- ![Pasted%20image%2020240218180520.png](img/Pasted%20image%2020240218180520.png)
		- ![Pasted%20image%2020240218180625.png](img/Pasted%20image%2020240218180625.png)
