# Azure VMWare Solution: VPN GW to ExpressRoute GW Transitivity
![image](https://user-images.githubusercontent.com/97964083/214078217-86c26773-6339-4ea3-a1ca-0f35291b25ea.png)

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
Create a subnet in your vNET called exactly RouteServerSubnet. The minimum size of this subnet needs to be a /27. 

## Step 4: Deploy Azure Route Server
Create an Azure Route Server in the RouteServerSubnet
	
Create a Route Server
https://learn.microsoft.com/en-us/azure/route-server/quickstart-configure-route-server-portal#create-a-route-server-1

## Step 5: Enable Azure Route Server Branch-to-Branch
On Azure Enable Branch-To-Branch

Configure Route Exchange
https://learn.microsoft.com/en-us/azure/route-server/quickstart-configure-route-server-portal#configure-route-exchange
	
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
Configure BGP On-Premise and confirm BGP neighbor is up.

## Step 10: Connect AVS back to vNET
Connect AVS Managed ExpressRoute back to your ExpressRoute GW. 

Use an existing virtual network gateway  
https://learn.microsoft.com/en-us/azure/azure-vmware/deploy-azure-vmware-solution?tabs=azure-portal#use-an-existing-virtual-network-gateway  

