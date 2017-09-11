## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm#create) 命令创建 VM。 

下面的示例创建一个名为 *myVM* 的 VM，并且在默认密钥位置中不存在 SSH 密钥时创建这些密钥。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

创建 VM 后，Azure CLI 会显示类似于以下示例的信息。 记下 `publicIpAddress`。 此地址用于访问 VM。

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```



## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80 

默认情况下，仅允许通过 SSH 连接登录到 Azure 中部署的 Linux VM。 由于此 VM 将用作 Web 服务器，因此需要从 Internet 打开端口 80。 使用 [az vm open-port](/cli/azure/vm#open-port) 命令打开所需端口。  
 
```azurecli-interactive 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```
## <a name="ssh-into-your-vm"></a>通过 SSH 连接到 VM


如果还不知道 VM 的公共 IP 地址，请运行 [az network public-ip list](/cli/azure/network/public-ip#list) 命令：


```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

使用以下命令创建与虚拟机的 SSH 会话。 使用虚拟机的正确的公共 IP 地址进行替换。 在此示例中，IP 地址为 *40.68.254.142*。

```bash
ssh azureuser@40.68.254.142
```

