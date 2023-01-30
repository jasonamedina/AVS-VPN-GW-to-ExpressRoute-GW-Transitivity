# Azure VMWare Solution: VPN GW to ExpressRoute GW Transitivity
![image](https://user-images.githubusercontent.com/97964083/214078217-86c26773-6339-4ea3-a1ca-0f35291b25ea.png)

## Step 1
Deploy a new ExpressRoute Gateway in the same vNET where your current VPN Gateway is located. This new ExpressRoute Gateway will live in the same "GatewaySubnet" as your VPN Gateway.  

**Configure a virtual network gateway for ExpressRoute using the Azure portal**  
https://learn.microsoft.com/en-us/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager  

## Step 2
Go to your VPN Gateway and under Settings>Configuration. 
Under "Active-Active mode" click "Enabled" 
Please create a new Second Public IP address that will be assigned to the secondary VPN GW.
	
Click Save
Do not move onto the next step until Active-Active mode configuration has completed. This change can take up to 45 minutes to complete. 
	
	
## Step 3
Create a subnet in your vNET called RouteServerSubnet The minimum size of this subnet needs to be a /27. 

## Step 4
Create an Azure Route Server in the RouteServerSubnet
	
Create a Route Server
https://learn.microsoft.com/en-us/azure/route-server/quickstart-configure-route-server-portal#create-a-route-server-1

## Step 5
On Azure Enable Branch-To-Branch

Configure Route Exchange
https://learn.microsoft.com/en-us/azure/route-server/quickstart-configure-route-server-portal#configure-route-exchange
	
## Step 6	
Go to your VPN Gateway and under Settings>Configuration. 
	Click "Configure BGP"
	Do not change the default VPN GW BGP AS 65515
	Click Save

## Step 7
Local Network Gateway and click Yes "Configure BGP Settings"
	ASN: On-Premise ASN
	BGP Peer IP Address: On-Premise BGP Peer Address
	Address Space: /32
	Click save

## Step 8
Under VPN Gateway Settings>Connections. Double Click on your VPN Connection. 

## Step 9
Go Under Settings>Configuration Enable BGP. 
Click Save

## Step 10
Configure BGP On-Premise and confirm BGP neighbor is up.
Connect AVS Manag
