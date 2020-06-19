---
title: 在 Azure 自动化中管理更改跟踪和库存
description: 本文介绍如何使用更改跟踪和库存在环境中跟踪软件和 Microsoft 服务的更改。
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 4b8bf6a3f583e4c17f61e0a46911990ac5cc827c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830474"
---
# <a name="manage-change-tracking-and-inventory"></a>管理更改跟踪和清单

Azure 自动化将为环境中的计算机启用[更改跟踪和库存](change-tracking.md)功能。 此功能可在注册表项、文件、内容等位置跟踪和做出适用的更改。 本文包含使用此功能的过程。

## <a name="enable-the-full-change-tracking-and-inventory-feature"></a>启用完整的更改跟踪和库存功能

如果你已启用 [Azure 安全中心文件完整性监视 (FIM)](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)，则可按如下所述对计算机使用完整的更改跟踪和库存功能。 此过程不会删除你的设置。

> [!NOTE]
> 启用完整的更改跟踪和库存功能可能会产生额外的费用。 请参阅[自动化定价](https://azure.microsoft.com/pricing/details/automation/)。

1. 导航到工作区，在 [已安装的监视解决方案列表](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions) 中找到所需的监视解决方案，并将其删除。
2. 单击解决方案的名称打开其摘要页，然后单击“删除”，详见[删除监视解决方案](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution)。
3. 若要重新启用更改跟踪和库存，请导航到自动化帐户，并选择“配置管理”下的“更改跟踪”或“库存”。  
4. 选择 Log Analytics 工作区和自动化帐户，确认工作区设置，然后单击“启用”。

## <a name="enable-machines-for-change-tracking-and-inventory"></a><a name="onboard"></a>为计算机启用更改跟踪和库存

若要开始跟踪更改，必须在 Azure 自动化中启用更改跟踪和库存。 下面是为计算机启用此功能的建议方法和支持的方法： 

* [从虚拟机启用](automation-onboard-solutions-from-vm.md)
* [通过浏览多个计算机来启用](automation-onboard-solutions-from-browse.md)
* [从自动化帐户启用](automation-onboard-solutions-from-automation-account.md)
* [在 Azure 自动化 Runbook 中启用](automation-onboard-solutions.md)

## <a name="track-files"></a>跟踪文件

### <a name="configure-file-tracking-on-windows"></a>在 Windows 上配置文件跟踪

使用以下步骤在 Windows 计算机上配置文件跟踪：

1. 在自动化帐户中，选择“配置管理”下的“更改跟踪” 。 
2. 单击“编辑设置”（齿轮符号）。
3. 在“工作区配置”页上选择“Windows 文件”，然后单击“+ 添加”以添加要跟踪的新文件。 
4. 在“添加用于更改跟踪的 Windows 文件”窗格中，输入要跟踪的文件的信息，然后单击“保存”。 下表定义了可在信息中使用的属性。

    |properties  |说明  |
    |---------|---------|
    |已启用     | 如果应用了设置，则为 True，否则为 False。        |
    |项名称     | 要跟踪的文件的友好名称。        |
    |组     | 用于对文件进行逻辑分组的组名。        |
    |输入路径     | 用于检查文件的路径，例如：**c:\temp\\\*.txt**。 还可以使用环境变量，例如 `%winDir%\System32\\\*.*`。       |
    |路径类型     | 路径的类型。 可能的值为“文件”和“目录”。        |    
    |递归     | 在查找要跟踪的项时，如果使用递归，则为 True，否则为 False。        |    
    |上传文件内容 | 若要在跟踪的更改中上传文件内容，则为 True，否则为 False。|

5. 确保为“上传文件内容”指定 True。 此设置将为指示的文件路径启用文件内容跟踪。

### <a name="configure-file-tracking-on-linux"></a>在 Linux 上配置文件跟踪

使用以下步骤，在 Linux 计算机上配置要跟踪的文件：

1. 在自动化帐户中，选择“配置管理”下的“更改跟踪” 。 
2. 单击“编辑设置”（齿轮符号）。
3. 在“工作区配置”页上选择“Linux 文件”，然后单击“+ 添加”以添加要跟踪的新文件。 
4. 在“添加要进行更改跟踪的 Linux 文件”窗格中，输入要跟踪的文件或目录的信息，然后单击“保存”。 下表定义了可在信息中使用的属性。

    |properties  |说明  |
    |---------|---------|
    |已启用     | 如果应用了设置，则为 True，否则为 False。        |
    |项名称     | 要跟踪的文件的友好名称。        |
    |组     | 用于对文件进行逻辑分组的组名。        |
    |输入路径     | 用于检查文件的路径，例如 **/etc/*.conf**。       |
    |路径类型     | 路径的类型。 可能的值为“文件”和“目录”。        |
    |递归     | 在查找要跟踪的项时，如果使用递归，则为 True，否则为 False。        |
    |使用 Sudo     | 在检查项时，如果使用 sudo，则为 True，否则为 False。         |
    |链接     | 此设置确定在遍历目录时如何处理符号链接。 可能的值包括：<br> 忽略 - 忽略符号链接，不包括引用的文件/目录。<br>追随 - 在递归期间追随符号链接，并且包含引用的文件/目录。<br>管理 - 追随符号链接并允许更改返回的内容。 **注意** - 不建议使用此选项，因为它不支持文件内容检索。    |
    |上传文件内容 | 若要在跟踪的更改中上传文件内容，则为 True，否则为 False。 |

5. 确保为“上传文件内容”指定 True。 此设置将为指示的文件路径启用文件内容跟踪。

   ![添加 Linux 文件](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>跟踪文件内容

使用文件内容跟踪可以在发生跟踪的更改之前和之后查看文件的内容。 每次发生更改后，该功能会将文件内容保存到存储帐户。 下面是跟踪文件内容所要遵循的一些规则：

* 若要存储文件内容，需要有一个使用资源管理器部署模型的标准存储帐户。 

* 不要使用高级和经典部署模型存储帐户。 请参阅[关于 Azure 存储帐户](../storage/common/storage-create-storage-account.md)。

* 使用的存储帐户只能连接到一个自动化帐户。

* 在自动化帐户中启用[更改跟踪和库存](change-tracking.md)。

### <a name="enable-tracking-for-file-content-changes"></a>启用文件内容更改跟踪

1. 在 Azure 门户中打开你的自动化帐户，然后选择“配置管理”下的“更改跟踪” 。
2. 单击“编辑设置”（齿轮符号）。
3. 选择“文件内容”并单击“链接” 。 选择此项会打开“添加更改跟踪的内容位置”窗格。

   ![启用内容位置](./media/change-tracking-file-contents/enable.png)

4. 选择用于存储文件内容的订阅和存储帐户。 

5. 如果想针对所有现有的已跟踪文件启用文件内容跟踪，请在“上传所有设置的文件内容”处选择“开启” 。 以后可以更改每个文件路径的此设置。

   ![设置存储帐户](./media/change-tracking-file-contents/storage-account.png)

6. 更改跟踪和库存在启用文件内容更改跟踪时，会显示存储帐户和共享访问签名 (SAS) URI。 签名在 365 天后过期；可以单击“重新生成”来重新创建签名。

   ![列出帐户密钥](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>查看所跟踪文件的内容

更改跟踪和库存检测到所跟踪文件的更改后，你就可以在“更改详细信息”窗格中查看文件内容。  

![列出更改](./media/change-tracking-file-contents/change-list.png)

1. 在 Azure 门户中打开你的自动化帐户，然后选择“配置管理”下的“更改跟踪” 。

2. 在更改列表中选择某个文件，然后选择“查看文件内容更改”以查看文件的内容。 “更改详细信息”窗格会在文件信息的前面和后面显示标准。

   ![更改详细信息](./media/change-tracking-file-contents/change-details.png)

3. 可以在并排视图中查看文件内容。 可以选择“内联”以查看内联的更改视图。

## <a name="track-registry-keys"></a>跟踪注册表项

使用以下步骤，配置要在 Windows 计算机上跟踪的注册表项：

1. 在自动化帐户中，选择“配置管理”下的“更改跟踪” 。 
2. 单击“编辑设置”（齿轮符号）。
3. 在“工作区配置”页上，选择“Windows 注册表”。
4. 单击“+ 添加”以添加要跟踪的新注册表项。
5. 在“添加要进行更改跟踪的 Windows 注册表”窗格中，输入要跟踪的项的信息，然后单击“保存”。 下表定义了可在信息中使用的属性。

    |properties  |说明  |
    |---------|---------|
    |已启用     | 如果应用了设置，则为 True，否则为 False。        |
    |项名称     | 要跟踪的注册表项的易记名称。        |
    |组     | 用于对注册表项进行逻辑分组的组名。        |
    |Windows 注册表项   | 包含路径的项名，例如 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup**。      |

## <a name="search-logs-for-change-records"></a>在日志中搜索更改记录

可对 Azure Monitor 日志执行各种搜索以查找更改记录。 “更改跟踪”页打开后，单击“Log Analytics”打开“日志”页。 下表提供了在日志中搜索更改记录的示例。

|查询  |说明  |
|---------|---------|
|ConfigurationData<br>&#124; where   ConfigDataType == "Microsoft services" and SvcStartupType == "Auto"<br>&#124; where SvcState == "Stopped"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | 显示已设置为“自动”但报告为“已停止”的 Microsoft 服务的最新库存记录。 结果仅限于指定软件名称和计算机的最新记录。    |
|ConfigurationChange<br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Removed"<br>&#124; order by TimeGenerated desc|显示已删除的软件的更改记录。|

## <a name="create-alerts-on-changes"></a>针对更改创建警报

以下示例显示计算机上的 **C:\windows\system32\drivers\etc\hosts** 文件已修改。 此文件非常重要，因为 Windows 需要使用它将主机名解析为 IP 地址。 此操作优先于 DNS，可能导致连接问题。 此外，可能导致将流量重定向到恶意网站或危险网站。

![一个图表，显示 hosts 文件的更改情况](./media/change-tracking-file-contents/changes.png)

让我们使用此示例来探讨针对更改创建警报的步骤。

1. 在自动化帐户中，选择“配置管理”下的“更改跟踪”，然后选择“Log Analytics”  。 
2. 在“日志搜索”中，使用查询 `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` 查找 **hosts** 文件的内容更改。 此查询将查找其完全限定路径包含“hosts”一词的文件的内容更改。 还可将路径部分更改为完全限定的格式来请求特定的文件，例如，使用 `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"` 来这样做。

3. 在查询返回所需结果后，单击日志搜索中的“新建警报规则”以打开警报创建页。 还可以通过 Azure 门户中的“Azure Monitor”导航到此页。 

4. 再次检查查询，并修改警报逻辑。 在本示例中，你希望在检测到更改的情况下触发警报，即使只在环境中的所有计算机上检测到一个更改。

    ![对用于跟踪 hosts 文件更改的查询进行更改](./media/change-tracking-file-contents/change-query.png)

5. 设置警报逻辑后，请分配操作组，以便执行操作来响应触发的警报。 在本例中，我们将设置要发送的电子邮件，以及要创建的 IT 服务管理 (ITSM) 票证。 

    ![将操作组配置为针对更改发出警报](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>后续步骤

* 如果需要搜索存储在 Log Analytics 工作区中的日志，请参阅 [Azure Monitor 日志中的日志搜索](../log-analytics/log-analytics-log-searches.md)。
* 若要排查功能错误，请参阅[排查更改跟踪和库存的问题](troubleshoot/change-tracking.md)。