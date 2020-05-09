---
title: 在 Azure 自动化中管理更改跟踪和清单
description: 本文介绍如何使用更改跟踪和清单来跟踪你的环境中发生的软件和 Microsoft 服务更改。
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 8ca1bd7a724d3256bc2e171ce39fd6a06e2e5935
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779291"
---
# <a name="manage-change-tracking-and-inventory"></a>管理更改跟踪和清单

添加要跟踪的新文件或注册表项时，Azure 自动化将为[更改跟踪和清单](change-tracking.md)功能启用此功能。 本文包括使用此功能的过程。

## <a name="enable-the-full-change-tracking-and-inventory-feature"></a>启用完整更改跟踪和清单功能

如果已启用[Azure 安全中心文件完整性监视（FIM）](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)，可以使用如下所述的完整更改跟踪和清单功能。 此过程不会删除您的设置。

> [!NOTE]
> 启用 "完全更改跟踪和清单" 功能可能会导致额外的费用。 请参阅[自动化定价](https://azure.microsoft.com/pricing/details/automation/)。

1. 通过导航到工作区并在[已安装的监视解决方案列表](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions)中找到该解决方案，删除该监视解决方案。
2. 单击解决方案的名称以打开其 "摘要" 页，然后单击 "**删除**"，如[删除监视解决方案](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution)中所述。
3. 若要重新启用更改跟踪和清单，请导航到自动化帐户，并选择 "**配置管理**" 下的 "**更改跟踪**"。
4. 选择 "Log Analytics" 工作区和自动化帐户，确认工作区设置，然后单击 "**启用**"。

## <a name="onboard-machines-to-change-tracking-and-inventory"></a><a name="onboard"></a>要更改跟踪和清点的计算机上架

若要开始跟踪更改，必须在 Azure 自动化中启用更改跟踪和清单。 以下是将计算机载入此功能的建议和支持的方法： 

* [从虚拟机载入](automation-onboard-solutions-from-vm.md)
* [浏览多台计算机](automation-onboard-solutions-from-browse.md)
* [从自动化帐户加入](automation-onboard-solutions-from-automation-account.md)
* [加入 Azure 自动化 runbook](automation-onboard-solutions.md)

## <a name="track-files"></a>跟踪文件

### <a name="configure-file-tracking-on-windows"></a>在 Windows 上配置文件跟踪

使用以下步骤在 Windows 计算机上配置文件跟踪：

1. 在自动化帐户中，选择 "**配置管理**" 下的 "**更改跟踪**"。 
2. 单击“编辑设置”（齿轮符号）****。
3. 在 "工作区配置" 页上，选择 " **Windows 文件**"，然后单击 " **+ 添加**" 以添加要跟踪的新文件。
4. 在 "为更改跟踪添加 Windows 文件" 窗格中，输入要跟踪的文件的信息，然后单击 "**保存**"。 下表定义了可用于信息的属性。

    |属性  |说明  |
    |---------|---------|
    |已启用     | 如果应用了该设置，则为 True; 否则为 False。        |
    |项名称     | 要跟踪的文件的友好名称。        |
    |组     | 用于对文件进行逻辑分组的组名。        |
    |输入路径     | 要检查文件的路径，例如， **c：\temp\\\***。 你还可以使用环境变量，如`%winDir%\System32\\\*.*`。       |
    |路径类型     | 路径的类型。 可能的值为文件和目录。        |    
    |递归     | 如果在查找要跟踪的项时使用递归，则为 True; 否则为 False。        |    
    |上传文件内容 | 若要在跟踪的更改上上传文件内容，则为 True; 否则为 False。|

5. 确保将 "**上传文件内容**" 指定为 "True"。 此设置对所指示的文件路径启用文件内容跟踪。

### <a name="configure-file-tracking-on-linux"></a>在 Linux 上配置文件跟踪

使用以下步骤，在 Linux 计算机上配置要跟踪的文件：

1. 在自动化帐户中，选择 "**配置管理**" 下的 "**更改跟踪**"。 
2. 单击“编辑设置”（齿轮符号）****。
3. 在 "工作区配置" 页上，选择 " **Linux 文件**"，然后单击 " **+ 添加**" 以添加要跟踪的新文件。
4. 在 "为更改跟踪添加 Linux 文件" 窗格中，输入要跟踪的文件或目录的信息，然后单击 "**保存**"。 下表定义了可用于信息的属性。

    |属性  |说明  |
    |---------|---------|
    |已启用     | 如果应用了该设置，则为 True; 否则为 False。        |
    |项名称     | 要跟踪的文件的友好名称。        |
    |组     | 用于对文件进行逻辑分组的组名。        |
    |输入路径     | 要检查文件的路径，例如， **/etc/*.**       |
    |路径类型     | 路径的类型。 可能的值为文件和目录。        |
    |递归     | 如果在查找要跟踪的项时使用递归，则为 True; 否则为 False。        |
    |使用 Sudo     | 如果在检查项时使用 sudo，则为 True; 否则为 False。         |
    |链接     | 此设置确定在遍历目录时如何处理符号链接。 可能的值为：<br> 忽略 - 忽略符号链接，不包括引用的文件/目录。<br>接下来，在递归期间遵循符号链接，还包括引用的文件/目录。<br>管理 - 追随符号链接并允许更改返回的内容。 **注意**-不建议使用此选项，因为它不支持文件内容检索。    |
    |上传文件内容 | 若要在跟踪的更改上上传文件内容，则为 True; 否则为 False。 |

5. 确保将 "**上传文件内容**" 指定为 "True"。 此设置对所指示的文件路径启用文件内容跟踪。

   ![添加 Linux 文件](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>跟踪文件内容

文件内容跟踪允许在跟踪的更改前后查看文件的内容。 每次更改后，该功能会将文件内容保存到存储帐户。 下面是跟踪文件内容时应遵循的一些规则：

* 若要存储文件内容，需要有一个使用资源管理器部署模型的标准存储帐户。 

* 不要使用高级和经典部署模型存储帐户。 请参阅[关于 Azure 存储帐户](../storage/common/storage-create-storage-account.md)。

* 你使用的存储帐户只能连接到一个自动化帐户。

* 在自动化帐户中启用[更改跟踪和清单](change-tracking.md)。

### <a name="enable-tracking-for-file-content-changes"></a>启用文件内容更改跟踪

1. 在 Azure 门户中，打开自动化帐户，然后选择 "**配置管理**" 下的 "**更改跟踪**"。
2. 单击“编辑设置”（齿轮符号）****。
3. 选择“文件内容”并单击“链接”********。 此选择将打开更改跟踪窗格中的 "添加内容位置"。

   ![启用内容位置](./media/change-tracking-file-contents/enable.png)

4. 选择用于存储文件内容的订阅和存储帐户。 

5. 如果想针对所有现有的已跟踪文件启用文件内容跟踪，请在“上传所有设置的文件内容”处选择“开启”********。 以后可以更改每个文件路径的此设置。

   ![设置存储帐户](./media/change-tracking-file-contents/storage-account.png)

6. 更改跟踪和清单在启用文件内容更改跟踪时显示存储帐户和共享访问签名（SAS） Uri。 签名在365天后过期，你可以通过单击 "**重新生成**" 来重新创建它们。

   ![列出帐户密钥](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>查看跟踪文件的内容

更改跟踪和清单检测到对所跟踪文件的更改后，可以在 "更改详细信息" 窗格中查看文件内容。  

![列出更改](./media/change-tracking-file-contents/change-list.png)

1. 在 Azure 门户中，打开自动化帐户，然后选择 "**配置管理**" 下的 "**更改跟踪**"。

2. 在更改列表中选择一个文件，然后选择 "**查看文件内容更改**" 以查看该文件的内容。 "更改详细信息" 窗格将显示文件信息之前和之后的标准。

   ![更改详细信息](./media/change-tracking-file-contents/change-details.png)

3. 你正在并排查看此文件内容。 您可以选择 "**内联**" 查看更改的内联视图。

## <a name="track-registry-keys"></a>跟踪注册表项

使用以下步骤，配置要在 Windows 计算机上跟踪的注册表项：

1. 在自动化帐户中，选择 "**配置管理**" 下的 "**更改跟踪**"。 
2. 单击“编辑设置”（齿轮符号）****。
3. 在 "工作区配置" 页上，选择 " **Windows 注册表**"。
4. 单击 " **+ 添加**" 添加要跟踪的新注册表项。
5. 在 "为更改跟踪添加 Windows 注册表" 窗格上，输入要跟踪的密钥信息，然后单击 "**保存**"。 下表定义了可用于信息的属性。

    |属性  |说明  |
    |---------|---------|
    |已启用     | 如果应用了设置，则为 True; 否则为 False。        |
    |项名称     | 要跟踪的注册表项的友好名称。        |
    |组     | 用于对注册表项进行逻辑分组的组名。        |
    |Windows 注册表项   | 具有路径的项名称，例如**HKEY_LOCAL_MACHINE \Software\microsoft\windows\currentversion\explorer\user Shell Folders\Common Startup**。      |

## <a name="search-logs-for-change-records"></a>搜索日志中的更改记录

您可以对 Azure Monitor 日志执行各种搜索，以便进行更改记录。 打开 "更改跟踪" 页后，单击 " **Log Analytics** " 打开 "日志" 页。 下表提供了用于更改记录的示例日志搜索。

|查询  |说明  |
|---------|---------|
|ConfigurationData<br>&#124;，其中 ConfigDataType = = "Microsoft 服务"，SvcStartupType = = "Auto"<br>&#124; where SvcState == "Stopped"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | 显示已设置为 "自动" 但被报告为 "已停止" 的 Microsoft 服务的最新清单记录。 结果限制为指定软件名称和计算机的最新记录。    |
|ConfigurationChange<br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Removed"<br>&#124; order by TimeGenerated desc|显示删除的软件的更改记录。|

## <a name="create-alerts-on-changes"></a>创建有关更改的警报

以下示例显示已在计算机上修改了文件**C:\windows\system32\drivers\etc\hosts** 。 此文件非常重要，因为 Windows 使用它将主机名解析为 IP 地址。 此操作优先于 DNS，可能会导致连接问题。 还可能会导致将流量重定向到恶意或其他危险的网站。

![一个图表，显示 hosts 文件的更改情况](./media/change-tracking-file-contents/changes.png)

下面的示例介绍了创建有关更改的警报的步骤。

1. 在自动化帐户中，选择 "**配置管理**" 下的 "**更改跟踪**"，然后选择 " **Log Analytics**"。 
2. 在日志搜索中，通过查询`ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`查找对**hosts**文件的内容更改。 此查询将查找具有包含 "hosts" 一词的完全限定路径的文件的内容更改。 你还可以通过将路径部分更改为其完全限定的格式（例如，使用`FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`）来请求特定文件。

3. 查询返回所需的结果后，单击 "日志搜索" 中的 "**新建警报规则**" 以打开 "警报创建" 页。 还可以通过 Azure 门户中的**Azure Monitor**导航到此页面。 

4. 再次检查查询并修改警报逻辑。 在本示例中，你希望在检测到更改的情况下触发警报，即使只在环境中的所有计算机上检测到一个更改。

    ![更改以查询跟踪主机文件更改](./media/change-tracking-file-contents/change-query.png)

5. 设置警报逻辑后，分配操作组来执行操作，以响应触发的警报。 在这种情况下，我们要设置要发送的电子邮件以及要创建的 IT 服务管理（ITSM）票证。 

    ![将操作组配置为更改时发出警报](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>后续步骤

* 有关更改跟踪和清单的基础知识，请参阅[更改跟踪和清单概述](change-tracking.md)。
* 若要解决 Azure VM 的更改问题，请参阅[排查更改跟踪和清单问题](troubleshoot/change-tracking.md)。
* 使用[Azure Monitor 日志中的日志搜索](../log-analytics/log-analytics-log-searches.md)查看详细的更改跟踪数据。