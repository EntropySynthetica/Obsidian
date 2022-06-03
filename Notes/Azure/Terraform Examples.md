

```json
## Define Virtual Network for the Cluster
resource "azurerm_virtual_network" "vnet1" {
  name                = "vnet-aks-ncus"
  resource_group_name = azurerm_resource_group.SandboxRG.name
  location            = azurerm_resource_group.SandboxRG.location
  address_space       = ["10.0.0.0/8"]
}

## Define Public IP
resource "azurerm_public_ip" "SandboxPubIP" {
  name                = "SandboxPublicIPForLB"
  resource_group_name = azurerm_resource_group.SandboxRG.name
  location            = azurerm_resource_group.SandboxRG.location
  sku                 = "Basic"
  allocation_method   = "Static"
}

## Define Load Balancer
resource "azurerm_lb" "example" {
  name                = "SandboxLoadBalancer"
  resource_group_name = azurerm_resource_group.SandboxRG.name
  location            = azurerm_resource_group.SandboxRG.location
  sku                 = "Basic"

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = azurerm_public_ip.SandboxPubIP.id
  }
}
```