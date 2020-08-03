---
title: 在 Azure 门户中创建 SSH 密钥
description: 了解如何在用于连接 Linux Vm 的 Azure 门户中生成和存储 SSH 密钥。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/31/2020
ms.author: cynthn
ms.openlocfilehash: 39dbf7d9ad933dd47f0a566f02b5e276e4b615a3
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2020
ms.locfileid: "87514263"
---
# <a name="generate-and-store-ssh-keys-in-the-azure-portal"></a>在 Azure 门户中生成并存储 SSH 密钥

如果经常使用门户来部署 Linux Vm，则可以通过在门户中直接创建 SSH 密钥或从计算机上传它们来更简单地使用它。

首次创建 VM 时，可以创建 SSH 密钥，并将其重新用于其他 Vm。 或者，你可以单独创建 SSH 密钥，以便在 Azure 中存储一组密钥以满足你的组织的需求。 

如果你有现成的密钥，并且想要在门户中简化使用，则可以上传这些密钥，并将其存储在 Azure 中供重复使用。

有关创建和使用 Linux Vm 的 SSH 密钥的详细信息，请参阅（使用 SSH 密钥连接到 Linux Vm） [./linux/ssh-from-windows.md]。

## <a name="generate-new-keys"></a>生成新密钥

1. 打开 [Azure 门户] （ https://portal.azure.com 。

1. 在页面顶部，键入 " *SSH* " 进行搜索。 在 "*Marketplace*" 下，选择 " **SSH 密钥**"。

1. 在 " **SSH 密钥**" 页上，选择 "**创建**"。

:::image type="content" source="./media/ssh-keys/portal-sshkey.png" alt-text="创建新的资源组并生成 SSH 密钥对":::

1. 在 "**资源组**" 中，选择 "**新建**" 以创建新的资源组来存储密钥。 键入资源组的名称，然后选择 **"确定"**。

1. 在 "**区域**" 中，选择用于存储密钥的区域。 你可以使用任何区域中的密钥，这只是存储它们的区域。

1. 在 "**密钥对名称**" 中键入密钥的名称。

1. 在 " **SSH 公钥源**" 中，选择 "**生成公钥源**"。 

1. 完成操作后，选择“查看 + 创建”。

1. 通过验证后，选择“创建”。

1. 然后，你将看到一个弹出窗口，选择 "**下载私钥" 和 "创建资源**"。 这将下载作为 pem 文件的 SSH 密钥。

:::image type="content" source="./media/ssh-keys/download-key.png" alt-text="下载作为 pem 文件的私钥":::

1. 下载 pem 文件后，你可能需要将其从计算机上的某个位置移动到 SSH 客户端。


## <a name="connect-to-the-vm"></a>连接到 VM

在本地计算机上，打开 PowerShell 提示符，然后键入：

```powershell
ssh -i <path to the .pem file> username@<ipaddress of the VM>
```

例如，键入：`ssh -i /Downloads/mySSHKey.pem azureuser@123.45.67.890`


## <a name="upload-an-ssh-key"></a>上传 SSH 密钥

还可以上传公共 SSH 密钥，使其存储在 Azure 中。 有关如何创建 SSH 密钥对的信息，请参阅[使用 ssh 密钥连接到 Linux vm ](./linux/ssh-from-windows.md)。

1. 打开 [Azure 门户] （ https://portal.azure.com 。

1. 在页面顶部，键入 " *SSH* " 进行搜索。 在 "*Marketplace*" 下，选择 " **SSH 密钥**"。

1. 在 " **SSH 密钥**" 页上，选择 "**创建**"。

:::image type="content" source="./media/ssh-keys/upload.png" alt-text="上载要存储在 Azure 中的 SSH 公钥":::

1. 在 "**资源组**" 中，选择 "**新建**" 以创建新的资源组来存储密钥。 键入资源组的名称，然后选择 **"确定"**。

1. 在 "**区域**" 中，选择用于存储密钥的区域。 你可以使用任何区域中的密钥，这只是存储它们的区域。

1. 在 "**密钥对名称**" 中键入密钥的名称。

1. 在 " **SSH 公钥源**" 中，选择 "**上传现有公钥**"。 

1. 将公钥的完整内容粘贴到 "**上传密钥**"，然后选择 "**查看 + 创建**"。

1. 验证完成后，选择“创建”。 

上传密钥后，可以在创建 VM 时选择使用它。

## <a name="next-steps"></a>后续步骤

若要了解有关在 Azure Vm 中使用 SSH 密钥的详细信息，请参阅（使用 SSH 密钥连接到 Linux Vm） [./linux/ssh-from-windows.md]。