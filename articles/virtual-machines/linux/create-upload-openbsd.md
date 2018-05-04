---
title: 创建 OpenBSD VM 映像并上传到 Azure | Microsoft Docs
description: 了解如何通过 Azure CLI 创建和上传包含 OpenBSD 操作系统的虚拟硬盘 (VHD) 以创建 Azure 虚拟机
services: virtual-machines-linux
documentationcenter: ''
author: thomas1206
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: huishao
ms.openlocfilehash: 6c0eae36874c6d2738385c4530cc208a0b1362c4
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>创建 OpenBSD 磁盘映像并上传到 Azure
本文说明如何创建和上传包含 OpenBSD 操作系统的虚拟硬盘 (VHD)。 将其上传后，可以通过 Azure CLI 使用它作为你自己的映像在 Azure 中创建虚拟机 (VM)。


## <a name="prerequisites"></a>先决条件
本文假设拥有以下项目：

* **Azure 订阅** - 如果没有帐户，只需几分钟即可创建一个。 如果有 MSDN 订阅，请参阅 [Visual Studio 订户的每月 Azure 信用额度](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。 否则，请了解如何[创建一个免费试用帐户](https://azure.microsoft.com/pricing/free-trial/)。  
* **Azure CLI 2.0** - 确保已安装了最新的 [Azure CLI 2.0](/cli/azure/install-azure-cli) 并已使用 [az login](/cli/azure/reference-index#az_login) 登录到 Azure 帐户。
* **安装在 .vhd 文件中的 OpenBSD 操作系统** - 必须将受支持的 OpenBSD 操作系统（[6.1 版本 AMD64](https://ftp.openbsd.org/pub/OpenBSD/6.1/amd64/)）安装到虚拟硬盘中。 可使用多个工具创建 .vhd 文件。 例如，可使用虚拟化解决方案（如 Hyper-V）创建 .vhd 文件并安装操作系统。 有关如何安装和使用 Hyper-V 的说明，请参阅[安装 Hyper-V 和创建虚拟机](http://technet.microsoft.com/library/hh846766.aspx)。


## <a name="prepare-openbsd-image-for-azure"></a>为 Azure 准备 OpenBSD 映像
在安装 OpenBSD 操作系统 6.1（这会添加 Hyper-V 支持）的 VM 上，完成以下过程：

1. 如果在安装过程中未启用 DHCP，则按如下所示启用该服务：

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. 按如下所示设置串行控制台：

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. 按如下所示配置程序包安装：

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. 默认情况下，禁止在 Azure 中的虚拟机上使用 `root` 用户。 用户可以在 OpenBSD VM 上通过 `doas` 命令使用提升的权限运行各种命令。 Doas 在默认情况下处于启用状态。 有关详细信息，请参阅 [doas.conf](http://man.openbsd.org/doas.conf.5)。 

5. 按如下所示为 Azure 代理安装和配置先决条件：

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. 始终可以在 [Github](https://github.com/Azure/WALinuxAgent/releases) 上找到 Azure 代理的最新版本。 按如下所示安装代理：

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > 安装 Azure 代理之后，按如下所示验证它是否正在运行是一个好主意：
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. 取消预配系统以清除系统并使其适用于重新预配。 以下命令还会删除上次预配的用户帐户和关联数据：

    ```sh
    waagent -deprovision+user -force
    ```

现在，可以关闭 VM 了。


## <a name="prepare-the-vhd"></a>准备 VHD
Azure 不支持 VHDX 格式，仅支持**固定大小的 VHD**。 可使用 Hyper-V 管理器或 Powershell [convert-vhd](https://technet.microsoft.com/itpro/powershell/windows/hyper-v/convert-vhd) cmdlet 将磁盘转换为固定 VHD 格式。 下面是一个示例。

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>创建存储资源并上传
首先，使用 [az group create](/cli/azure/group#az_group_create) 创建资源组。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：

```azurecli
az group create --name myResourceGroup --location eastus
```

若要上传 VHD，请使用 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 创建存储帐户。 存储帐户名称必须唯一，因此请提供自己的名称。 以下示例创建一个名为 mystorageaccount 的存储帐户：

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

若要控制对存储帐户的访问，请按如下所示，使用 [az storage account key list](/cli/azure/storage/account/keys#az_storage_account_keys_list) 获取存储密钥：

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

若要在逻辑上分隔上传的 VHD，请使用 [az storage container create](/cli/azure/storage/container#az_storage_container_create) 在存储帐户中创建容器：

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

最后，按如下所示使用 [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) 上传 VHD：

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>通过 VHD 创建 VM
可以使用[示例脚本](../scripts/virtual-machines-linux-cli-sample-create-vm-vhd.md)或直接使用 [az vm create](/cli/azure/vm#az_vm_create) 创建 VM。 若要指定你上传的 OpenBSD VHD，请按如下所示使用 `--image` 参数：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

按如下所示，使用 [az vm list-ip-addresses](/cli/azure/vm#list-ip-addresses) 获取 OpenBSD VM 的 IP 地址：

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

现在可以使用 SSH 正常连接到 OpenBSD VM：
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>后续步骤
如果要了解有关 OpenBSD6.1 上的 Hyper-V 支持的详细信息，请阅读 [OpenBSD 6.1](https://www.openbsd.org/61.html) 和 [hyperv.4](http://man.openbsd.org/hyperv.4)。

如果要通过托管磁盘创建 VM，请阅读 [az disk](/cli/azure/disk)。 
