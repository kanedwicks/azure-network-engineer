[On-Prem Network]
   CIDR: 10.0.0.0/16
   Subnets: Users, Servers
        |
        |  VPN Gateway / ExpressRoute
        |  Route: 0.0.0.0/0 -> Azure
        v
[Azure Hub VNet]
   CIDR: 10.10.0.0/16
   Subnets:
     - GatewaySubnet
     - AzureFirewallSubnet
     - ManagementSubnet
   NSGs: Hub-NSG (control inbound/outbound)
   Route Table: UDR -> Firewall
   Firewall: Azure Firewall (central inspection)
   NAT Gateway: outbound internet egress
   DNS: Azure Private DNS Resolver
        |
        | VNet Peering (Hub-to-Spoke)
        | Route propagation via UDR
        v
[Azure Spoke VNet]
   CIDR: 10.20.0.0/16
   Subnets:
     - AppSubnet (10.20.1.0/24)
     - DataSubnet (10.20.2.0/24)
   NSGs: Spoke-NSG (app-level rules)
   Route Table: force traffic via Hub Firewall
        |
        | Private Link connection
        v
[Private Endpoint Subnet]
   CIDR: 10.20.3.0/24
   NSG: deny public access
   DNS: Private DNS Zone (app.privatelink.*)
        |
        v
[Application]
   App Service / VM / AKS
   Private IP only (no public exposure)
   Monitored by Azure Monitor + Log Analytics
