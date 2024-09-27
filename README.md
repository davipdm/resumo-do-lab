# resumo-do-lab
Este repositório contém o resumo das lições aprendidas durante o desenvolvimento do lab na DIO
#### Olá, primeiro desafio é criar uma maquina virtual ####
Estou utilizando Azure CLI para criar minha vm.

# 0. Instalar o Azure CLI
Se ainda não tiver o Azure CLI instalado, você pode fazer o download e a instalação seguindo as instruções na página oficial: Azure CLI Installation. https://learn.microsoft.com/pt-br/cli/azure/install-azure-cli

# 1. Criar grupo de recursos
az group create --name "rg-Lab01" --location eastus

# 2. Criar uma rede virtual
az network vnet create --name minhaVNet --resource-group "rg-Lab01" --subnet-name "vnet-prod01"

# 3. Criar um IP público
az network public-ip create --resource-group "rg-Lab01" --name "pip-vm01"

# 4. Criar uma interface de rede
az network nic create \
  --resource-group meuGrupoDeRecursos \
  --name minhaNIC \
  --vnet-name "vnet-prod01" \
  --subnet minhaSubRede \
  --public-ip-address "pip-vm01"

# 5. Criar a máquina virtual
az vm create \
  --resource-group "rg-Lab01" \
  --name vm01 \
  --nics minhaNIC \
  --image UbuntuLTS \
  --admin-username adm-davi \
  --admin-password MinhaSenhaSegura123*

# 6. Abrir a porta 22 para SSH (opcional)
az vm open-port --port 22 --resource-group "rg-Lab01" --name vm01

# 7. Obter o IP público
az vm list-ip-addresses --resource-group "rg-Lab01" --name vm01 --output table

### Criando uma VM usando Terraform ###
# Configuração do provedor Azure
provider "azurerm" {
  features {}
}

# Criar um grupo de recursos
resource "azurerm_resource_group" "example_rg" {
  name     = "example-resources"
  location = "East US"
}

# Criar uma rede virtual
resource "azurerm_virtual_network" "example_vnet" {
  name                = "example-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = azurerm_resource_group.example_rg.location
  resource_group_name = azurerm_resource_group.example_rg.name
}

# Criar uma subnet
resource "azurerm_subnet" "example_subnet" {
  name                 = "example-subnet"
  resource_group_name  = azurerm_resource_group.example_rg.name
  virtual_network_name = azurerm_virtual_network.example_vnet.name
  address_prefixes     = ["10.0.1.0/24"]
}

# Criar um IP público
resource "azurerm_public_ip" "example_public_ip" {
  name                = "example-public-ip"
  location            = azurerm_resource_group.example_rg.location
  resource_group_name = azurerm_resource_group.example_rg.name
  allocation_method   = "Dynamic"
}

# Criar uma interface de rede
resource "azurerm_network_interface" "example_nic" {
  name                = "example-nic"
  location            = azurerm_resource_group.example_rg.location
  resource_group_name = azurerm_resource_group.example_rg.name

  ip_configuration {
    name                          = "internal"
    subnet_id                     = azurerm_subnet.example_subnet.id
    private_ip_address_allocation = "Dynamic"
    public_ip_address_id          = azurerm_public_ip.example_public_ip.id
  }
}

# Criar a máquina virtual
resource "azurerm_virtual_machine" "example_vm" {
  name                  = "example-vm"
  location              = azurerm_resource_group.example_rg.location
  resource_group_name   = azurerm_resource_group.example_rg.name
  network_interface_ids = [azurerm_network_interface.example_nic.id]
  vm_size               = "Standard_DS1_v2"

  # Especificar a imagem
  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "18.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "example_os_disk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  os_profile {
    computer_name  = "example-vm"
    admin_username = "adminuser"
    admin_password = "AdminPassword123!"
  }

  os_profile_linux_config {
    disable_password_authentication = false
  }
}

# Output para exibir o endereço IP público da VM
output "public_ip_address" {
  value = azurerm_public_ip.example_public_ip.ip_address
}

### AULA ARMAZENAMENTO ###


