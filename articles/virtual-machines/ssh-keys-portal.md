---
title: 在 Azure 门户中创建 SSH 密钥
description: 了解如何在用于连接 Linux Vm 的 Azure 门户中生成和存储 SSH 密钥。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/25/2020
ms.author: cynthn
ms.openlocfilehash: abc9a2ae130d987c90ce87ffaecbf2bb44b06010
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88929426"
---
# <a name="generate-and-store-ssh-keys-in-the-azure-portal"></a>在 Azure 门户中生成并存储 SSH 密钥

如果经常使用门户来部署 Linux Vm，则可以通过在门户中直接创建 SSH 密钥或从计算机上传它们来更简单地使用它。

首次创建 VM 时，可以创建 SSH 密钥，并将其重新用于其他 Vm。 或者，你可以单独创建 SSH 密钥，以便在 Azure 中存储一组密钥以满足你的组织的需求。 

如果你有现成的密钥，并且想要在门户中简化使用，则可以上传这些密钥，并将其存储在 Azure 中供重复使用。

若要详细了解如何创建 SSH 密钥并将其用于 Linux Vm，请参阅 [使用 ssh 密钥连接到 Linux vm](./linux/ssh-from-windows.md)。

## <a name="generate-new-keys"></a>生成新密钥

1. 打开 [Azure 门户](https://portal.azure.com)。

1. 在页面顶部，键入 " *SSH* " 进行搜索。 在 " **Marketplace**" 下，选择 " **SSH 密钥**"。

1. 在 " **SSH 密钥** " 页上，选择 " **创建**"。

   :::image type="content" source="./media/ssh-keys/portal-sshkey.png" alt-text="创建新的资源组并生成 SSH 密钥对&quot;:::

1. 在 &quot; **资源组** &quot; 中，选择 &quot; **新建** &quot; 以创建新的资源组来存储密钥。 键入资源组的名称，然后选择 **&quot;确定&quot;**。

1. 在 &quot; **区域** &quot; 中，选择用于存储密钥的区域。 你可以使用任何区域中的密钥，这只是存储它们的区域。

1. 在 &quot; **密钥对名称**&quot; 中键入密钥的名称。

1. 在 &quot; **SSH 公钥源**&quot; 中，选择 &quot; **生成公钥源**&quot;。 

1. 完成操作后，选择“查看 + 创建”。

1. 通过验证后，选择“创建”。

1. 然后，你将看到一个弹出窗口，选择 &quot; **下载私钥" 和 "创建资源**"。 这将下载作为 pem 文件的 SSH 密钥。

   :::image type="content" source="./media/ssh-keys/download-key.png" alt-text="创建新的资源组并生成 SSH 密钥对&quot;:::

1. 在 &quot; **资源组** &quot; 中，选择 &quot; **新建** &quot; 以创建新的资源组来存储密钥。 键入资源组的名称，然后选择 **&quot;确定&quot;**。

1. 在 &quot; **区域** &quot; 中，选择用于存储密钥的区域。 你可以使用任何区域中的密钥，这只是存储它们的区域。

1. 在 &quot; **密钥对名称**&quot; 中键入密钥的名称。

1. 在 &quot; **SSH 公钥源**&quot; 中，选择 &quot; **生成公钥源**&quot;。 

1. 完成操作后，选择“查看 + 创建”。

1. 通过验证后，选择“创建”。

1. 然后，你将看到一个弹出窗口，选择 &quot; **下载私钥":::

1. 下载 pem 文件后，你可能需要将其从计算机上的某个位置移动到 SSH 客户端。


## <a name="connect-to-the-vm"></a>连接到 VM

在本地计算机上，打开 PowerShell 提示符，然后键入：

```powershell
ssh -i <path to the .pem file> username@<ipaddress of the VM>
```

例如，键入：`ssh -i /Downloads/mySSHKey.pem azureuser@123.45.67.890`


## <a name="upload-an-ssh-key"></a>上传 SSH 密钥

还可以上传公共 SSH 密钥，使其存储在 Azure 中。 有关如何创建 SSH 密钥对的信息，请参阅 [使用 ssh 密钥连接到 Linux vm](./linux/ssh-from-windows.md)。

1. 打开 [Azure 门户](https://portal.azure.com)。

1. 在页面顶部，键入 " *SSH* " 进行搜索。 在 "*Marketplace*" 下，选择 " **SSH 密钥**"。

1. 在 " **SSH 密钥** " 页上，选择 " **创建**"。

   :::image type="content" source="./media/ssh-keys/upload.png" alt-text="创建新的资源组并生成 SSH 密钥对&quot;:::

1. 在 &quot; **资源组** &quot; 中，选择 &quot; **新建** &quot; 以创建新的资源组来存储密钥。 键入资源组的名称，然后选择 **&quot;确定&quot;**。

1. 在 &quot; **区域** &quot; 中，选择用于存储密钥的区域。 你可以使用任何区域中的密钥，这只是存储它们的区域。

1. 在 &quot; **密钥对名称**&quot; 中键入密钥的名称。

1. 在 &quot; **SSH 公钥源**&quot; 中，选择 &quot; **生成公钥源**&quot;。 

1. 完成操作后，选择“查看 + 创建”。

1. 通过验证后，选择“创建”。

1. 然后，你将看到一个弹出窗口，选择 &quot; **下载私钥" **查看 + 创建**"。

1. 验证完成后，选择“创建”。 

上传密钥后，可以在创建 VM 时选择使用它。

## <a name="list-keys"></a>列出密钥

在门户中创建的 SSH 密钥存储为资源，因此可以筛选资源视图以查看所有这些密钥。

1. 在门户中，选择 " **所有资源**"。
1. 在筛选器中，选择 " **类型**"，并取消 **选中 "全选** " 选项以清除列表。
1. 在筛选器中键入 " **ssh** "，然后选择 " **ssh 密钥**"。

   :::image type="content" source="./media/ssh-keys/filter.png" alt-text="创建新的资源组并生成 SSH 密钥对&quot;:::

1. 在 &quot; **资源组** &quot; 中，选择 &quot; **新建** &quot; 以创建新的资源组来存储密钥。 键入资源组的名称，然后选择 **&quot;确定&quot;**。

1. 在 &quot; **区域** &quot; 中，选择用于存储密钥的区域。 你可以使用任何区域中的密钥，这只是存储它们的区域。

1. 在 &quot; **密钥对名称**&quot; 中键入密钥的名称。

1. 在 &quot; **SSH 公钥源**&quot; 中，选择 &quot; **生成公钥源**&quot;。 

1. 完成操作后，选择“查看 + 创建”。

1. 通过验证后，选择“创建”。

1. 然后，你将看到一个弹出窗口，选择 &quot; **下载私钥":::

## <a name="get-the-public-key"></a>获取公钥

如果需要公钥，可以轻松地从门户网站页中复制该密钥。 使用上一节中的过程 (列出你的密钥) 然后从列表中选择一个密钥。 你的密钥页面将打开，你可以单击密钥旁边的 " **复制到剪贴板** " 图标以复制它。

## <a name="next-steps"></a>后续步骤

若要了解有关在 Azure Vm 中使用 SSH 密钥的详细信息，请参阅 [使用 ssh 密钥连接到 Linux vm](./linux/ssh-from-windows.md)。
