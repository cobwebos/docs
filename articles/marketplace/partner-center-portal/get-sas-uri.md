---
title: 获取 VM 映像的 SAS URI-Azure Marketplace
description: 在 Azure 市场中为虚拟硬盘 (VHD) 生成共享访问签名 (SAS) URI。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: a84f287c6303e093d68dd462ccc5cecc34b463cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89144480"
---
# <a name="get-a-sas-uri-for-your-vm-image"></a>获取 VM 映像的 SAS URI

在发布过程中，必须为与计划相关联的每个 VHD 提供 SAS (共享访问签名) URI (之前称为 "Sku) "。 Microsoft 需要在认证过程中访问这些 VHD。 你将在合作伙伴中心的 " **计划** " 选项卡上输入此 URI。

为 Vhd 生成 SAS Uri 需要满足以下要求：

- 它们仅支持非托管 Vhd。
- 只需要 List 和 Read 权限。 不要提供写入或删除访问权限。
- 访问权限持续时间（过期日期）应至少是从 SAS URI 创建时间开始算起的 3 周。
- 若要防范 UTC 时间更改，请将开始日期设置为当前日期的前一天。 例如，如果当前日期为2020年6月16日，则选择6/15/2020。

## <a name="generate-the-sas-address"></a>生成 SAS 地址

有两个用于创建 SAS 地址 (URL) 的常用工具：

1. Microsoft 存储资源管理器 – 适用于 Windows、macOS 和 Linux 的图形工具。
2. Microsoft Azure CLI – 建议用于非 Windows 操作系统，以及自动化或连续集成环境。

### <a name="using-tool-1-microsoft-storage-explorer"></a>使用工具1： Microsoft 存储资源管理器

1. 下载并安装 [Microsoft Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。
2. 打开资源管理器，在左侧菜单中，选择 " **添加帐户**"。
3. 在 " **连接到 Azure 存储** " 对话框中，选择 " **添加 azure 帐户** " 并登录到 Azure 帐户。
4. 在左侧的 "资源管理器" 窗格中，展开 " **存储帐户** " 节点。
5. 右键单击 VHD，然后选择 " **获取共享访问签名**"。
6. 在 " **共享访问签名** " 对话框中，填写以下字段：

    1. 开始时间 – VHD 访问权限的开始日期。 请提供当前日期的前一天的日期。
    2. 过期时间 – VHD 访问权限的过期日期。 请提供自当前日期开始算起的至少三周后的日期。
    3. 权限 – 选择“读取”和“列出”权限。
    4. 容器级别 – 选中“生成容器级别的共享访问签名 URI”复选框。

    !["共享访问签名" 对话框。](media/vm/create-sas-uri-storage-explorer.png)

7. 若要为此 VHD 创建关联 SAS URI，请选择“创建”。 该对话框会刷新并显示有关此操作的详细信息。

8. 复制“URL”，并将其保存到位于安全位置的某个文本文件。

    ![正在复制 URI。](media/vm/create-sas-uri-shared-access-signature-details.png)

    此生成的 SAS URL 适用于容器级访问权限。 若要使其特定，请编辑文本文件以添加 VHD 名称。

9. 在 SAS URI（包括正斜杠）中的 vhds 字符串后面插入 VHD 名称。 最终 SAS URI 应如下所示：

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

1. 对要发布的计划中的每个 VHD 重复这些步骤。

### <a name="using-tool-2-azure-cli"></a>使用工具2： Azure CLI

1. 下载并安装 [MICROSOFT AZURE CL](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)I。 版本适用于 Windows、macOS，以及 Linux 的各种分发版。
2. 创建一个 PowerShell 文件（扩展名为 .ps1），在其中复制以下代码，然后在本地保存该文件。

    ```JSON
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net’ --name <vhd-name> --permissions rl --start ‘<start-date>’ --expiry ‘<expiry-date>’
    ```

3. 编辑该文件以使用以下参数值。 提供 UTC 日期时间格式的日期，例如 2020-04-01T00：00：00Z。

    - 帐户-名称–你的 Azure 存储帐户名称。
    - 帐户密钥–你的 Azure 存储帐户密钥。
    - vhd-名称–你的 VHD 名称。
    - 开始日期– VHD 访问权限的开始日期。 请提供当前日期的前一天的日期。
    - 过期-日期-用于 VHD 访问的权限到期日期。 请提供自当前日期开始算起的至少三周后的日期。

    下面是编写) 时 (的正确参数值的示例：

    `az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ON c+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net’ --name vhds -- permissions rl --start ‘2020-04-01T00:00:00Z’ --expiry ‘2021-04-01T00:00:00Z’`

1. 保存更改。
2. 使用以下方法之一，通过管理特权运行此脚本，以创建容器级访问权限的 SAS 连接字符串：

    - 从控制台运行脚本。 在 Windows 中，右键单击脚本，然后选择“以管理员身份运行”。
    - 从 PowerShell 脚本编辑器（例如 [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise)）运行脚本。 此屏幕显示如何在此编辑器中创建 SAS 连接字符串：

    [![在 PowerShell 编辑器中创建 SAS 连接字符串](media/vm/create-sas-uri-power-shell-ise.png)](media/vm/create-sas-uri-power-shell-ise.png#lightbox)

6. 复制 SAS 连接字符串，并将其保存到位于安全位置的某个文本文件。 编辑此字符串，以添加 VHD 位置信息来创建最终 SAS URI。
7. 在 Azure 门户中，转到包含与新 URI 关联的 VHD 的 Blob 存储。
8. 复制 thebBlob 服务终结点的 URL：

    ![复制 blob 服务终结点的 URL。](media/vm/create-sas-uri-blob-endpoint.png)

9. 编辑包含步骤 6 中所述 SAS 连接字符串的文本文件。 使用以下格式创建完整 SAS URI：

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

## <a name="verify-the-sas-uri"></a>验证 SAS URI

请先检查 SAS URI，然后再将其发布到合作伙伴中心，以避免提交请求后的 SAS URI 相关的任何问题。 此过程是可选的，但建议这样做。

- 该 URI 包含 VHD 映像文件名，包括文件扩展名 `.vhd` 。
- `Sp=rl` 出现在 URI 中间附近。 此字符串显示指定的读取和列表访问权限。
- 当 `sr=c` 出现时，这表示指定了容器级访问权限。
- 将 URI 复制并粘贴到浏览器中以对 blob 进行测试下载（可以在下载完成之前取消该操作）。

## <a name="next-step"></a>后续步骤

- 请参阅 [创建 Azure 虚拟机产品/服务](azure-vm-create-offer.md)。
