# Azure VMWare Solution: VPN GW to ExpressRoute GW Transitivity
![image](https://user-images.githubusercontent.com/97964083/214078217-86c26773-6339-4ea3-a1ca-0f35291b25ea.png)

## Description
This design scenario is only intended for customers who do not have ExpressRoute and only use a Site to Site VPN connection back to Azure. AVS only uses an Azure managed ExpressRoute to connect back to Azure. This is a step-by-step guide on how to connect On-Premise to the AVS private cloud transiting a Site to Site VPN to an AVS managed ExpressRoute and vice versa.

## Prerequisites  
1. Already have an existing Site-to-Site VPN Connection back to Azure  
https://learn.microsoft.com/en-us/azure/vpn-gateway/tutorial-site-to-site-portal

## Step 1: Create ExpressRoute Gateway
Deploy a new ExpressRoute Gateway in the same vNET where your current VPN Gateway is located. This new ExpressRoute Gateway will live in the same "GatewaySubnet" as your VPN Gateway.  

**Configure a virtual network gateway for ExpressRoute using the Azure portal**  
https://learn.microsoft.com/en-us/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager  

## Step 2: Deploy Active/Active VPN Gateway
- Go to your VPN Gateway under Settings>Configuration. 
- Under "Active-Active mode" click "Enabled" 
- Please create a new Second Public IP address that will be assigned to the secondary VPN GW.	
- Click Save 
- Do not move onto the next step until Active-Active mode configuration has completed. This change can take up to 45 minutes to complete. 
![image](https://user-images.githubusercontent.com/97964083/215498428-e7a264d4-5c8c-4ae2-a65c-db9f9834561b.png)
		
## Step 3: Create RouteServerSubnet for Azure Route Server
Create a subnet with the exact name of RouteServerSubnet. The minimum size of this subnet needs to be a /27. 

## Step 4: Deploy Azure Route Server
- Create an Azure Route Server in the RouteServerSubnet  

**Create a Route Server**   
https://learn.microsoft.com/en-us/azure/route-server/quickstart-configure-route-server-portal#create-a-route-server-1  

![image](https://user-images.githubusercontent.com/97964083/215504753-220f176f-b5e5-4f9f-947f-f5f5861fa2a1.png)
![image](https://user-images.githubusercontent.com/97964083/215504930-38a991cc-eb84-400c-8306-ecc43aded0a3.png)
	
## Step 5: Enable Azure Route Server Branch-to-Branch
On Azure Enable Branch-To-Branch  

**Configure Route Exchange**  
https://learn.microsoft.com/en-us/azure/route-server/quickstart-configure-route-server-portal#configure-route-exchange  

![image](https://user-images.githubusercontent.com/97964083/215505504-8327ac13-bc2e-42f1-a7e8-1da61db015e9.png)

	
## Step 6:Configure BGP on VPN Gateway	
- Go to your VPN Gateway and under Settings>Configuration. 
- Click "Configure BGP"
- Do not change the default VPN GW BGP AS 65515
- Click Save
![image](https://user-images.githubusercontent.com/97964083/215501023-d2710b78-62d0-434b-afcc-15fcecf3876f.png)

## Step 7: Configure BGP on Local Network Gateway
- Local Network Gateway and click Yes "Configure BGP Settings"
- ASN: On-Premise ASN
- BGP Peer IP Address: On-Premise BGP Peer Address
- Address Space: Delete all subnets and add /32 of BGP On-Premise Peer Address
- Click save
![image](https://user-images.githubusercontent.com/97964083/215502946-48364f6e-39f1-4a35-80fd-29f9f8833ec7.png)

## Step 8: Enable BGP on VPN Connection
Under VPN Gateway Settings>Connections. Double Click on your VPN Connection. 
Go Under Settings>Configuration Enable BGP. 
Click Save
![image](https://user-images.githubusercontent.com/97964083/215503579-faad957e-0803-4b05-b4e9-c763b58e532d.png)

## Step 9: Configure On-Premise BGP
- Configure BGP On-Premise and confirm BGP neighbor is up. 
- Make sure to advertise via BGP your on-premise routes back to Azure.  

## Step 10: Connect AVS back to vNET
Connect AVS Managed ExpressRoute back to your ExpressRoute GW. 

**Use an existing virtual network gateway**   
https://learn.microsoft.com/en-us/azure/azure-vmware/deploy-azure-vmware-solution?tabs=azure-portal#use-an-existing-virtual-network-gateway  

## Notes
When using HCX with a Site to Site VPN connection, please take into the account the IPSEC overhead. The Uplink Network Profiles MTU should be set to 1350 or lower. This change needs to be completed on both the HCX on-premise and HCX Cloud Uplink Network Profiles. 
![image](https://user-images.githubusercontent.com/97964083/215516121-70edb806-9d12-4363-89bf-b4c77313f409.png)

https://learn.microsoft.com/en-us/azure/azure-vmware/configure-vmware-hcx#create-network-profiles
