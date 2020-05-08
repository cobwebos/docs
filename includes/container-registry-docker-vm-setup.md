---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/07/2020
ms.author: danlep
ms.openlocfilehash: be170144fddeb1a69592f1714ec745d559665832
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982408"
---
## <a name="create-a-docker-enabled-virtual-machine"></a>创建启用了 Docker 的虚拟机

出于测试目的，请使用启用了 Docker 的 Ubuntu VM 来访问 Azure 容器注册表。 若要对注册表使用 Azure Active Directory 身份验证，请在 VM 上安装[Azure CLI][azure-cli] 。 如果已有 Azure 虚拟机，请跳过此创建步骤。

你可以对虚拟机和容器注册表使用同一资源组。 此设置简化了在结束时的清理，但并不是必需的。 如果选择为虚拟机和虚拟网络创建单独的资源组，请运行[az group create][az-group-create]。 以下示例假设你已为资源组名称和注册表位置设置环境变量：

```azurecli
az group create --name $RESOURCE_GROUP --location $REGISTRY_LOCATION
```

现在使用[az vm create][az-vm-create]部署默认 Ubuntu Azure 虚拟机。 以下示例创建名为*myDockerVM*的 VM。

```azurecli
VM_NAME=myDockerVM

az vm create \
  --resource-group $RESOURCE_GROUP \
  --name $VM_NAME \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

创建 VM 需要几分钟时间。 等该命令完成后，记下 Azure CLI 显示的 `publicIpAddress`。 使用此地址与 VM 建立 SSH 连接。

### <a name="install-docker-on-the-vm"></a>在 VM 上安装 Docker

等 VM 正常运行后，与 VM 建立 SSH 连接。 将 *publicIpAddress* 替换为 VM 的公共 IP 地址。

```bash
ssh azureuser@publicIpAddress
```

运行以下命令，在 Ubuntu VM 上安装 Docker：

```bash
sudo apt-get update
sudo apt install docker.io -y
```

安装完成后，运行以下命令验证 Docker 在 VM 上是否正常运行：

```bash
sudo docker run -it hello-world
```

输出：

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>安装 Azure CLI

按照[使用 apt 安装 Azure CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) 中的步骤在 Ubuntu 虚拟机上安装 Azure CLI。 例如：

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

退出 SSH 连接。

[azure-cli]: /cli/azure/install-azure-cli
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-group-create]: /cli/azure/group