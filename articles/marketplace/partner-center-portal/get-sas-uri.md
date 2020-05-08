---
title: VM 映像的共享访问签名 URI-Azure Marketplace
description: 在 Azure Marketplace 中为虚拟硬盘（VHD）生成共享访问签名（SAS） URI。
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mingshen
ms.openlocfilehash: b521a3a035044e2f0c1b625df19d265cfa35b49a
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857926"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>获取 VM 映像的共享访问签名 URI

> [!IMPORTANT]
> 我们正在将 Azure 虚拟机产品/服务的管理从云合作伙伴门户迁移到合作伙伴中心。 在迁移你的产品/服务之前，请按照[获取 VM 映像的共享访问签名 URI](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri)中的说明进行操作，以便云合作伙伴门户管理你的产品/服务。

本文介绍如何为每个虚拟硬盘（VHD）生成共享访问签名（SAS）统一资源标识符（URI）。

在发布过程中，必须提供与计划关联的每个 VHD 的 URI。 这些计划以前称为 Sku 或库存单位。 Microsoft 需要在认证过程中访问这些 VHD。 你将在合作伙伴中心的 "**计划**" 选项卡上输入此 URI。

为 Vhd 生成 SAS Uri 时，请遵循以下要求：

* 仅支持非托管的 VHD。
* 仅`List`和`Read`权限是必需的。 不要提供写入或删除访问权限。
* 访问权限持续时间（过期日期）应至少是从 SAS URI 创建时间开始算起的 3 周。
* 若要防止 UTC 时间更改，请将开始日期设置为当前日期的前一天。 例如，如果当前日期为10月6日2019，则选择10/5/2019。

## <a name="generate-the-sas-address"></a>生成 SAS 地址

有两个常用工具用于创建 SAS 地址（URL）：

* **Microsoft 存储资源管理器**–适用于 Windows、MacOS 和 Linux 的图形工具。
* **MICROSOFT AZURE CLI** -建议用于非 Windows 操作系统和自动或持续集成环境。

### <a name="use-microsoft-storage-explorer"></a>使用 Microsoft 存储资源管理器

1. 下载并安装[Microsoft Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。
2. 打开资源管理器，在左侧菜单中，选择 "**添加帐户**"。 此时将显示 "**连接到 Azure 存储**" 对话框。
3. 选择 "**添加 Azure 帐户**"，然后**登录**。 完成登录到你的 Azure 帐户所需的步骤。
4. 在左侧的 "**资源管理器**" 窗格中，切换到你的**存储帐户**并展开此节点。
5. 右键单击 VHD，然后选择 "**获取共享访问签名**"。
6. 此时将显示 "**共享访问签名**" 对话框。 完成以下字段：

    * **开始时间**– VHD 访问权限的开始日期。 请提供当前日期的前一天的日期。
    * **过期时间**– VHD 访问权限的到期日期。 提供至少三周超过当前日期的日期。
    * **权限**–选择 "读取" 和 "列出" 权限。
    * **容器级别**–选中 "**生成容器级别的共享访问签名 URI** " 复选框。

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="说明 "共享访问签名" 对话框":::

7. 若要为此 VHD 创建关联的 SAS URI，请选择 "**创建**"。 对话框会刷新并显示有关此操作的详细信息。
8. 复制该**URI**并将其保存到位于安全位置的文本文件中。

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="说明 "共享访问签名详细信息" 框":::

    此生成的 SAS URI 适用于容器级别的访问。 为此，请编辑文本文件以添加 VHD 名称（下一步）。

9. 将 VHD 名称插入到 SAS URI 中的 vhd 字符串后（包括正斜杠）。 最终的 SAS URI 应如下所示：

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`例如，如果 VDH 的名称为`TestRGVM2.vhd`，则生成的 SAS URI 将为：

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. 对要发布的计划中的每个 VHD 重复这些步骤。

### <a name="using-azure-cli"></a>使用 Azure CLI

1. 下载并安装[MICROSOFT AZURE CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)。 版本适用于 Windows、macOS，以及 Linux 的各种分发版。
2. 创建一个 PowerShell 文件（ps1 文件扩展名），复制以下代码，并将其保存在本地。

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. 编辑该文件以使用以下参数值。 提供 UTC 日期时间格式的日期，如`2020-04-01T00:00:00Z`。

    * `<account-name>`–你的 Azure 存储帐户名称
    * `<account-key>`–你的 Azure 存储帐户密钥
    * `<vhd-name>`–你的 VHD 名称
    * `<start-date>`– VHD 访问的权限开始日期。 请提供当前日期的前一天的日期。
    * `<expiry-date>`–用于 VHD 访问权限的到期日期。 提供当前日期之后至少三周的日期。

    此示例显示了正确的参数值（在撰写本文时）：

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. 保存更改。
5. 使用以下方法之一，使用管理权限运行此脚本，为容器级别的访问创建**SAS 连接字符串**：

    * 从控制台运行脚本。 在 Windows 中，右键单击该脚本，然后选择 "以**管理员身份运行**"。
    * 从 PowerShell 脚本编辑器（如[Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise)）运行脚本。 此屏幕显示了如何在此编辑器中创建 SAS 连接字符串：

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="说明如何创建带有 Windows PowerShell ISE 的 SAS 连接字符串":::

6. 复制 SAS 连接字符串，并将其保存到位于安全位置的文本文件中。 编辑此字符串，以添加 VHD 位置信息以创建最终的 SAS URI。
7. 在 Azure 门户中，请前往包含与新 URI 关联的 VHD 的 blob 存储。
8. 复制**Blob 服务终结点**的 URL，如以下屏幕截图所示

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="阐释 Blob 服务终结点":::

9. 编辑包含步骤 6 中所述 SAS 连接字符串的文本文件。 使用以下格式创建完整的 SAS URI：

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    例如，如果 VHD 的名称为`TestRGVM2.vhd`，则 SAS URI 将为：

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

对要发布的 SKU 中的每个 VHD 重复这些步骤。

## <a name="verify-the-sas-uri"></a>验证 SAS URI

使用以下清单检查每个创建的 SAS URI，验证：

* URI 如下所示：`<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* 该 URI 包含 VHD 映像文件名，其中包括文件扩展名 ".vhd"。
* `sp=rl`出现在 URI 中间附近。 此字符串显示指定`Read`和`List`访问权限。
* 出现`sr=c`时，这意味着指定了容器级别的访问权限。
* 将 URI 复制并粘贴到浏览器中以测试-下载 blob （可以在下载完成前取消该操作）。

## <a name="next-step"></a>后续步骤

如果在创建 SAS URI 时遇到困难，请参阅[常见 SAS URL 问题](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues)。 否则，请将 SAS URI 保存到安全位置供将来使用。 你需要它来在合作伙伴中心发布 VM 产品/服务。

* [创建 Azure 虚拟机套餐](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer)
