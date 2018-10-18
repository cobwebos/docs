---
author: jluk
ms.service: cloud-shell
ms.topic: persist-storage
ms.date: 9/7/2018
ms.author: juluk
ms.openlocfilehash: 6055b70c7df2704a334b7f14c9365863ddafbd5a
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164534"
---
# <a name="persist-files-in-azure-cloud-shell"></a>在 Azure Cloud Shell 中持久保存文件
Cloud Shell 利用 Azure 文件存储在会话之间持久保存文件。 初始启动时，Cloud Shell 会提示关联新的或现有的文件共享，以便在会话之间持久保存文件。

> [!NOTE]
> Bash 和 PowerShell 共享相同的文件共享。 只能有一个文件共享与 Cloud Shell 中的自动装载相关联。

## <a name="create-new-storage"></a>创建新存储

使用基本设置并仅选择一个订阅时，Cloud Shell 会在最靠近你的支持区域中代表你创建三个资源：
* 资源组：`cloud-shell-storage-<region>`
* 存储帐户：`cs<uniqueGuid>`
* 文件共享：`cs-<user>-<domain>-com-<uniqueGuid>`

![订阅设置](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

文件共享在 `$Home` 目录中装载为 `clouddrive`。 这是一次性操作，文件共享会自动装载在后续会话中。 

> [!NOTE]
> 安全起见，每个用户应预配自己的存储帐户。  对于基于角色的访问控制 (RBAC)，用户必须具有存储帐户级别的“参与者”访问权限或更高访问权限。

文件共享还包含一个为你创建的 5-GB 映像，该映像自动将数据持久保存在 `$Home` 目录中。 对于 Bash 和 PowerShell 都是这样。

## <a name="use-existing-resources"></a>使用现有资源

通过使用高级选项，可以将现有资源相关联。 出现存储设置的提示时，选择“显示高级设置”查看其他选项。 填充的存储选项针对本地冗余存储 (LRS)、异地冗余存储 (GRS) 和区域冗余存储 (ZRS) 帐户进行筛选。 可以转到[此处来详细了解](https://docs.microsoft.com/azure/storage/common/storage-redundancy#choosing-a-replication-option) Azure 存储帐户的复制选项。

![资源组设置](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

当选择 Cloud Shell 区域时，还必须选择在该区域中装载后备存储帐户。

### <a name="supported-storage-regions"></a>支持的存储区域
关联的 Azure 存储帐户必须与其装载到的 Cloud Shell 计算机位于同一区域。 若要查找当前区域，可以在 Bash 中运行 `env` 并找到变量 `ACC_LOCATION`。 文件共享会收到系统创建的 5-GB 映像，用于保存 `$Home` 目录。

Cloud Shell 计算机位于以下区域中：
|区域|区域|
|---|---|
|美洲|美国东部、美国中南部、美国西部|
|欧洲|欧洲北部、欧洲西部|
|亚太区|印度中部、亚洲东南部|

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>根据 Azure 资源策略限制资源创建
在 Cloud Shell 中创建的存储帐户都标记有 `ms-resource-usage:azure-cloud-shell`。 如果想禁止用户在 Cloud Shell 中创建存储帐户，请创建此特定标记触发的[适用于标记的 Azure 资源策略](../articles/azure-policy/json-samples.md)。
