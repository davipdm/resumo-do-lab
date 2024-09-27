# resumo-do-lab
Este repositório contém o resumo das lições aprendidas durante o desenvolvimento do lab na DIO
#### Olá, primeiro desafio é criar uma maquina virtual ####
Estou utilizando Azure CLI para criar minha vm.

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
