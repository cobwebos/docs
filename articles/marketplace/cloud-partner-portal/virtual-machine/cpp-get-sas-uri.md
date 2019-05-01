---
title: 获取基于 Microsoft Azure 的 VM 映像的共享访问签名 URI | Microsoft Docs
description: 介绍如何获取 VM 映像的共享访问签名 (SAS) URI。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: c21fa3cf819f48dcda46f2d444ed52bc2eb9ae3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743959"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>获取 VM 映像的共享访问签名 URI

在发布过程中，必须为 SKU 关联的每个虚拟硬盘 (VHD) 提供统一资源标识符 (URI)。 Microsoft 需要在认证过程中访问这些 VHD。 本文介绍如何生成每个 VHD 的共享访问签名 (SAS) URI。 将在云合作伙伴门户上的“SKU”选项卡中输入此 URI。 

生成 VHD 的 SAS URI 时，请遵循以下要求：

- 仅支持非托管的 VHD。
- `List` 和 `Read` 权限足够。 不要提供 `Write` 或 `Delete` 访问权限。
- 访问权限持续时间（过期日期）应至少是从 SAS URI 创建时间开始算起的 3 周。
- 为了防范出现 UTC 时间偏差，请将开始日期设置为当前日期的前一天。 例如，如果当前时间是 2014 年 10 月 6 日，则选择 10/5/2014。

## <a name="generate-the-sas-url"></a>生成 SAS URL

可使用以下工具通过两种常用方式生成 SAS URL：

-   Microsoft 存储资源管理器 - 适用于 Windows、macOS 和 Linux 的图形工具
-   Microsoft Azure CLI - 建议用于非 Windows OS，以及自动化或连续集成环境


### <a name="azure-cli"></a>Azure CLI

在 Azure CLI 中使用以下步骤生成 SAS URI。

1. 下载并安装 [Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)。  版本适用于 Windows、macOS，以及 Linux 的各种分发版。 
2. 创建一个 PowerShell 文件（扩展名为 `.ps1`），在其中复制以下代码，然后在本地保存该文件。

   ``` powershell
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
   ```
    
3. 编辑该文件以提供以下参数值。  应以 UTC 日期时间格式提供日期，例如 `10-25-2016T00:00:00Z`。
   - `<account-name>` - Azure 存储帐户名称
   - `<account-key>` - Azure 存储帐户密钥
   - `<vhd-name>` - VHD 名称
   - `<start-date>` - VHD 访问权限的开始日期。 请提供当前日期的前一天的日期。 
   - `<expiry-date>` - VHD 访问权限的过期日期。  请提供自当前日期开始算起的至少三周后的日期。 
 
   以下示例显示了（在撰写本文时）正确的参数值。

   ``` powershell
       az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2017-11-06T00:00:00Z' --expiry '2018-08-20T00:00:00Z'
   ```
 
4. 保存对此 PowerShell 脚本所做的更改。
5. 使用管理特权运行此脚本，以生成容器级访问权限的 SAS 连接字符串。  可以使用两种基本方法：
   - 从控制台运行脚本。  例如，在 Windows 中，右键单击该脚本并选择“以管理员身份运行”。
   - 使用管理特权从某个 PowerShell 脚本编辑器（例如 [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise)）运行脚本。 
     以下示例演示了在此编辑器中生成的 SAS 连接字符串。 

     ![在 PowerShell ISE 中生成 SAS URI](./media/publishvm_032.png)

6. 复制生成的 SAS 连接字符串，并将其保存到位于安全位置的某个文本文件。  稍后将要编辑此字符串，以便在其中添加关联的 VHD 位置信息来创建最终的 SAS URI。 
7. 在 Azure 门户中，导航到包含与新生成 URI 关联的 VHD 的 Blob 存储。
8. 复制“Blob 服务终结点”的 URL 值，如下所示。

    ![Azure 门户中的 Blob 服务终结点](./media/publishvm_033.png)

9. 编辑包含步骤 6 中所述 SAS 连接字符串的文本文件。  使用以下格式构造完整的 SAS URI：

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    例如，如果 VDH 名为 `TestRGVM2.vhd`，则生成的 SAS URI 将是：

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

对要发布的 SKU 中的每个 VHD 重复这些步骤。


### <a name="microsoft-storage-explorer"></a>Microsoft 存储资源管理器

使用以下步骤在 Microsoft Azure 存储资源管理器中生成 SAS URI。

1. 下载并安装 [Microsoft Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。
2. 打开资源管理器，在左侧菜单栏中单击“添加帐户”图标。  此时会显示“连接到 Azure 存储”对话框。
3. 选择“添加 Azure 帐户”，然后单击“登录”。  继续执行所需的步骤以登录到 Azure 帐户。
4. 在左侧的“资源管理器”窗格中，导航到“存储帐户”并展开此节点。
5. 右键单击你的 VHD，并从上下文菜单中选择“获取共享访问签名”。 

    ![在 Azure 资源管理器中获取 SAS 项](./media/publishvm_034.png)

6. 此时会显示“共享访问签名”对话框。 输入以下字段的值：
   - **开始时间** - VHD 访问权限的开始日期。 请提供当前日期的前一天的日期。
   - **过期时间** - VHD 访问权限的过期日期。  请提供自当前日期开始算起的至少三周后的日期。
   - **权限** - 选择 `Read` 和 `List` 权限。 

     ![Azure 资源管理器中的 SAS 对话框](./media/publishvm_035.png)

7. 单击“创建”以创建与此 VHD 关联的 SAS URI。  现在，该对话框会显示有关此操作的详细信息。 
8. 复制“URL”值，并将其保存到位于安全位置的某个文本文件。 

    ![在 Azure 资源管理器中创建 SAS URI](./media/publishvm_036.png)

    生成的 SAS URL 适用于容器级访问权限。  若要将其设为专用的 SAS URL，必须将关联的 VHD 名称添加到其中。

9. 编辑文本文件。 在 SAS URL（包括前导正斜杠）中的 `vhds` 字符串后面插入 VHD 名称。  最终的 SAS URI 应采用以下格式：

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    例如，如果 VDH 名为 `TestRGVM2.vhd`，则生成的 SAS URI 将是：

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

对要发布的 SKU 中的每个 VHD 重复这些步骤。


## <a name="verify-the-sas-uri"></a>验证 SAS URI

使用以下查检表检查并验证生成的每个 SAS URI。  验证：
- URI 的格式 ：`<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
- URI 包含 VHD 映像文件名（包括扩展名“.vhd”）。
- URI 的中间位置会显示 `sp=rl`。 此字符串指示指定了 `Read` 和 `List` 访问权限。
- 该位置的后面还会显示 `sr=c`。 此字符串指示指定了容器级访问权限。
- 复制该 URI 并将其粘贴到浏览器中，以开始下载关联的 Blob。  （在下载完成之前可以取消该操作。）


## <a name="next-steps"></a>后续步骤

如果在生成 SAS URI 时遇到了问题，请参阅[常见 SAS URL 问题](./cpp-common-sas-url-issues.md)。  否则，请将 SAS URI 保存到安全位置供将来使用。 在云合作伙伴门户中[发布 VM 套餐](./cpp-publish-offer.md)时需要使用它。
