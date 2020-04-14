---
title: 获取 VM 映像的共享访问签名 URI |Azure 应用商店
description: 本文介绍如何获取每个虚拟硬盘 （VHD） 的共享访问签名 （SAS） URI。
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mingshen
ms.openlocfilehash: 50bda733b0bb8b0c98eb69a15ab3000ad278031c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265702"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>获取 VM 映像的共享访问签名 URI

> [!IMPORTANT]
> 我们将 Azure 虚拟机产品的管理从云合作伙伴门户迁移到合作伙伴中心。 在迁移产品/服务之前，请按照云合作伙伴门户[的 VM 映像获取共享访问签名 URI](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri)中的说明来管理您的产品/服务。

本文介绍如何为每个虚拟硬盘 （VHD） 生成共享访问签名 （SAS） 统一资源标识符 （URI）。

在发布过程中，您必须为与计划关联的每个 VHD 提供 URI。 这些计划以前称为 SKU 或库存单位。 Microsoft 需要在认证过程中访问这些 VHD。 您将在合作伙伴中心的 **"计划"** 选项卡上输入此 URI。

为 VHD 生成 SAS URI 时，请遵循以下要求：

* 仅支持非托管的 VHD。
* 仅`List`需要`Read`权限和权限。 不提供写入或删除访问权限。
* 访问权限持续时间（过期日期）应至少是从 SAS URI 创建时间开始算起的 3 周。
* 要防止 UTC 时间更改，将开始日期设置为当前日期之前的一天。 例如，如果当前日期为 2019 年 10 月 6 日，请选择 2019 年 10 月 5 日。

## <a name="generate-the-sas-address"></a>生成 SAS 地址

有两种常用工具用于创建 SAS 地址 （URL）：

* **微软存储资源管理器**– 可用于 Windows、macOS 和 Linux 的图形工具。
* **Microsoft Azure CLI** – 推荐用于非 Windows 操作系统和自动或连续集成环境。

### <a name="use-microsoft-storage-explorer"></a>使用微软存储资源管理器

1. 下载并安装[微软 Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。
2. 打开资源管理器，并在左侧菜单中选择"**添加帐户**"。 将显示"**连接到 Azure 存储**"对话框。
3. 选择 **"添加 Azure 帐户**"，然后**登录**。 完成登录 Azure 帐户所需的步骤。
4. 在左侧**资源管理器**窗格中，转到**存储帐户**并展开此节点。
5. 右键单击 VHD，然后选择 **"获取共享访问签名**"。
6. 将显示"**共享访问签名**"对话框。 完成以下字段：

    * **开始时间**= VHD 访问的权限开始日期。 请提供当前日期的前一天的日期。
    * **过期时间**= VHD 访问的权限到期日期。 提供比当前日期至少三周的日期。
    * **权限**= 选择"读取"和"列表"权限。
    * **容器级**= 选中 **"生成容器级共享访问签名 URI"** 复选框。

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="说明共享访问签名对话框":::

7. 要为此 VHD 创建关联的 SAS URI，请选择 **"创建**"。 对话框将刷新并显示有关此操作的详细信息。
8. 复制**URI**并将其保存到安全位置的文本文件中。

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="说明共享访问签名详细信息框":::

    此生成的 SAS URI 用于容器级访问。 要使其具体化，请编辑文本文件以添加 VHD 名称（下一步）。

9. 在 SAS URI 中的 vhds 字符串之后插入 VHD 名称（包括正向斜杠）。 最终的 SAS URI 应如下所示：

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`例如，如果 VDH 的名称为`TestRGVM2.vhd`，则生成的 SAS URI 将是：

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. 对要发布的计划中的每个 VHD 重复这些步骤。

### <a name="using-azure-cli"></a>使用 Azure CLI

1. 下载并安装[微软 Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)。 版本适用于 Windows、macOS，以及 Linux 的各种分发版。
2. 创建 PowerShell 文件（.ps1 文件扩展名），在以下代码中复制，然后将其保存在本地。

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. 编辑文件以使用以下参数值。 以 UTC 日期时间格式（如`2020-04-01T00:00:00Z`）提供日期。

    * `<account-name>`• Azure 存储帐户名称
    * `<account-key>`• Azure 存储帐户密钥
    * `<vhd-name>`• 您的 VHD 名称
    * `<start-date>`• VHD 访问的权限开始日期。 请提供当前日期的前一天的日期。
    * `<expiry-date>`• VHD 访问的权限到期日期。 在当前日期后至少三周提供日期。

    此示例显示正确的参数值（在编写本文时）：

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. 保存更改。
5. 使用以下方法之一，使用管理权限运行此脚本，为容器级访问创建**SAS 连接字符串**：

    * 从控制台运行脚本。 在 Windows 中，右键单击脚本并选择"**以管理员身份运行**"。
    * 从 PowerShell 脚本编辑器（如[Windows PowerShell ISE）](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise)运行脚本。 此屏幕显示在此编辑器中创建 SAS 连接字符串：

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="演示了使用 Windows PowerShell ISE 创建 SAS 连接字符串":::

6. 复制 SAS 连接字符串并将其保存到安全位置的文本文件中。 编辑此字符串以添加 VHD 位置信息以创建最终的 SAS URI。
7. 在 Azure 门户中，转到包含与新 URI 关联的 VHD 的 Blob 存储。
8. 复制**Blob 服务终结点**的 URL，如以下屏幕截图所示

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="说明 Blob 服务终结点":::

9. 编辑包含步骤 6 中所述 SAS 连接字符串的文本文件。 使用此格式创建完整的 SAS URI：

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    例如，如果 VHD 的名称为`TestRGVM2.vhd`，则 SAS URI 将为：

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

对要发布的 SKU 中的每个 VHD 重复这些步骤。

## <a name="verify-the-sas-uri"></a>验证 SAS URI

使用以下检查表检查每个创建的 SAS URI，以验证：

* URI 如下所示：`<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* URI 包括 VHD 映像文件名，包括文件名扩展名".vhd"。
* `sp=rl`显示在 URI 的中间附近。 此字符串显示`Read`该`List`和 访问已指定。
* 出现时`sr=c`，这意味着指定了容器级访问。
* 将 URI 复制并粘贴到浏览器中以测试下载 Blob（您可以在下载完成之前取消操作）。

## <a name="next-step"></a>后续步骤

如果您在创建 SAS URI 时遇到困难，请参阅[常见的 SAS URL 问题](https://aka.ms/AzureSAS_URI_FAQ)。 否则，请将 SAS URI 保存到安全位置供将来使用。 您需要它在合作伙伴中心发布 VM 产品/服务。

* [创建 Azure 虚拟机产品/服务](https://aka.ms/Create_AzureVMoffer)
