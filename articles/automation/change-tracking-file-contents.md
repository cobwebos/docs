---
title: 在 Azure 自动化中管理更改跟踪和库存
description: 本文介绍如何使用更改跟踪和库存在环境中跟踪软件和 Microsoft 服务的更改。
services: automation
ms.subservice: change-inventory-management
ms.date: 06/15/2020
ms.topic: conceptual
ms.openlocfilehash: eab509e389c074232526aa93fcebb72f3bc986c0
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185596"
---
# <a name="manage-change-tracking-and-inventory"></a>管理更改跟踪和清单

添加要跟踪的新文件或注册表项时，Azure 自动化会将其用于[更改跟踪和清单](change-tracking.md)。 本文介绍如何在检测到更改时配置跟踪、查看跟踪结果和处理警报。

使用本文中的步骤之前，请确保已使用以下方法之一在 Vm 上启用更改跟踪和清点：

* [从自动化帐户启用更改跟踪和清单](automation-enable-changes-from-auto-acct.md)
* [通过浏览 Azure 门户启用更改跟踪和清单](automation-enable-changes-from-browse.md)
* [从 runbook“启用更改跟踪和清单”](automation-enable-changes-from-runbook.md)
* [从 Azure VM 启用更改跟踪和清单](automation-enable-changes-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>限制部署的范围

更改跟踪和库存在工作区中使用作用域配置来限定要接收更改的计算机。 有关详细信息，请参阅[Limit 更改跟踪和清点部署范围](automation-scope-configurations-change-tracking.md)。

## <a name="track-files"></a>跟踪文件

您可以使用更改跟踪和清单来跟踪对文件和文件夹/目录的更改。 本部分介绍如何在 Windows 和 Linux 上配置文件跟踪。

### <a name="configure-file-tracking-on-windows"></a>在 Windows 上配置文件跟踪

使用以下步骤在 Windows 计算机上配置文件跟踪：

1. 在自动化帐户中，选择“配置管理”下的“更改跟踪” 。 
2. 单击“编辑设置”（齿轮符号）。
3. 在“工作区配置”页上选择“Windows 文件”，然后单击“+ 添加”以添加要跟踪的新文件。 
4. 在 "为更改跟踪添加 Windows 文件" 窗格中，输入要跟踪的文件或文件夹的信息，然后单击 "**保存**"。 下表定义了可在信息中使用的属性。

    |properties  |说明  |
    |---------|---------|
    |已启用     | 如果应用了设置，则为 True，否则为 False。        |
    |项名称     | 要跟踪的文件的友好名称。        |
    |组     | 用于对文件进行逻辑分组的组名。        |
    |输入路径     | 用于检查文件的路径，例如：**c:\temp\\\*.txt**。 还可以使用环境变量，例如 `%winDir%\System32\\\*.*`。       |
    |路径类型     | 路径的类型。 可能的值为文件和文件夹。        |    
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
    |链接     | 此设置确定在遍历目录时如何处理符号链接。 可能的值包括：<br> 忽略 - 忽略符号链接，不包括引用的文件/目录。<br>追随 - 在递归期间追随符号链接，并且包含引用的文件/目录。<br>管理-遵循符号链接并允许更改返回的内容。<br>**注意：** 不建议使用 "管理" 选项，因为它不支持文件内容检索。    |
    |上传文件内容 | 若要在跟踪的更改中上传文件内容，则为 True，否则为 False。 |

5. 确保为“上传文件内容”指定 True。 此设置将为指示的文件路径启用文件内容跟踪。

   ![添加 Linux 文件](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>跟踪文件内容

使用文件内容跟踪可以在发生跟踪的更改之前和之后查看文件的内容。 每次更改后，该功能会将文件内容保存到[存储帐户](../storage/common/storage-account-overview.md)。 下面是跟踪文件内容所要遵循的一些规则：

* 若要存储文件内容，需要有一个使用资源管理器部署模型的标准存储帐户。 
* 不要使用高级和经典部署模型存储帐户。 请参阅[关于 Azure 存储帐户](../storage/common/storage-account-create.md)。
* 只能将存储帐户连接到一个自动化帐户。
* 必须在自动化帐户中启用[更改跟踪和清单](change-tracking.md)。

### <a name="enable-tracking-for-file-content-changes"></a>启用文件内容更改跟踪

使用以下步骤来启用跟踪文件内容的更改：

1. 在 Azure 门户中打开你的自动化帐户，然后选择“配置管理”下的“更改跟踪” 。
2. 单击“编辑设置”（齿轮符号）。
3. 选择“文件内容”并单击“链接” 。 选择此项会打开“添加更改跟踪的内容位置”窗格。

   ![添加内容位置](./media/change-tracking-file-contents/enable.png)

4. 选择用于存储文件内容的订阅和存储帐户。 

5. 如果想针对所有现有的已跟踪文件启用文件内容跟踪，请在“上传所有设置的文件内容”处选择“开启” 。 以后可以更改每个文件路径的此设置。

   ![设置存储帐户](./media/change-tracking-file-contents/storage-account.png)

6. 更改跟踪和库存在启用文件内容更改跟踪时，会显示存储帐户和共享访问签名 (SAS) URI。 签名在 365 天后过期；可以单击“重新生成”来重新创建签名。

   ![列出帐户密钥](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>查看所跟踪文件的内容

更改跟踪和库存检测到所跟踪文件的更改后，你就可以在“更改详细信息”窗格中查看文件内容。  

![列出更改](./media/change-tracking-file-contents/change-list.png)

1. 在 Azure 门户中打开你的自动化帐户，然后选择“配置管理”下的“更改跟踪” 。

2. 在更改列表中选择某个文件，然后选择“查看文件内容更改”以查看文件的内容。 "更改详细信息" 窗格显示每个属性的标准前后文件信息。

   ![更改详细信息](./media/change-tracking-file-contents/change-details.png)

3. 可以在并排视图中查看文件内容。 可以选择“内联”以查看内联的更改视图。

## <a name="track-registry-keys"></a>跟踪注册表项

使用以下步骤，配置要在 Windows 计算机上跟踪的注册表项：

1. 在 Azure 门户中打开你的自动化帐户，然后选择“配置管理”下的“更改跟踪” 。 
2. 单击“编辑设置”（齿轮符号）。
3. 在“工作区配置”页上，选择“Windows 注册表”。
4. 单击“+ 添加”以添加要跟踪的新注册表项。
5. 在“添加要进行更改跟踪的 Windows 注册表”窗格中，输入要跟踪的项的信息，然后单击“保存”。 下表定义了可在信息中使用的属性。

    |properties  |说明  |
    |---------|---------|
    |已启用     | 如果应用了设置，则为 True，否则为 False。        |
    |项名称     | 要跟踪的注册表项的易记名称。        |
    |组     | 用于对注册表项进行逻辑分组的组名。        |
    |Windows 注册表项   | 具有路径的键名称，例如 `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup` 。      |

## <a name="search-logs-for-change-records"></a>在日志中搜索更改记录

可对 Azure Monitor 日志执行各种搜索以查找更改记录。 “更改跟踪”页打开后，单击“Log Analytics”打开“日志”页。 下表提供了在日志中搜索更改记录的示例。

|查询  |说明  |
|---------|---------|
|`ConfigurationData`<br>&#124;`where ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"`<br>&#124;`where SvcState == "Stopped"`<br>&#124;`summarize arg_max(TimeGenerated, *) by SoftwareName, Computer`         | 显示已设置为“自动”但报告为“已停止”的 Microsoft 服务的最新库存记录。 结果仅限于指定软件名称和计算机的最新记录。    |
|`ConfigurationChange`<br>&#124;`where ConfigChangeType == "Software" and ChangeCategory == "Removed"`<br>&#124;`order by TimeGenerated desc`|显示已删除的软件的更改记录。|

## <a name="create-alerts-on-changes"></a>针对更改创建警报

以下示例显示已在计算机上修改了文件**c:\windows\system32\drivers\etc\hosts** 。 此文件非常重要，因为 Windows 需要使用它将主机名解析为 IP 地址。 此操作优先于 DNS，可能导致连接问题。 此外，可能导致将流量重定向到恶意网站或危险网站。

![显示主机文件更改的图表](./media/change-tracking-file-contents/changes.png)

让我们使用此示例来探讨针对更改创建警报的步骤。

1. 在自动化帐户中，选择“配置管理”下的“更改跟踪”，然后选择“Log Analytics”  。 
2. 在“日志搜索”中，使用查询 `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` 查找 **hosts** 文件的内容更改。 此查询将查找具有包含单词的完全限定路径名称的文件的内容更改 `hosts` 。 还可将路径部分更改为完全限定的格式来请求特定的文件，例如，使用 `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"` 来这样做。

3. 查询返回其结果后，单击 "日志搜索" 中的 "**新建警报规则**" 以打开 "警报创建" 页。 还可以通过 Azure 门户中的“Azure Monitor”导航到此页。 

4. 再次检查查询，并修改警报逻辑。 在本示例中，你希望在检测到更改的情况下触发警报，即使只在环境中的所有计算机上检测到一个更改。

    ![对用于跟踪 hosts 文件更改的查询进行更改](./media/change-tracking-file-contents/change-query.png)

5. 设置警报逻辑后，分配操作组来执行操作以响应警报触发。 在本例中，我们将设置要发送的电子邮件，以及要创建的 IT 服务管理 (ITSM) 票证。 

    ![将操作组配置为针对更改发出警报](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>后续步骤

* 有关作用域配置的信息，请参阅[限制更改跟踪和清单部署范围](automation-scope-configurations-change-tracking.md)。
* 如果需要搜索存储在 Log Analytics 工作区中的日志，请参阅 [Azure Monitor 日志中的日志搜索](../azure-monitor/log-query/log-query-overview.md)。
* 如果已完成部署，请参阅[取消工作区与自动化帐户的关联更改跟踪和清单](automation-unlink-workspace-change-tracking.md)。
* 若要从更改跟踪和清单中删除 Vm，请参阅[从更改跟踪和清单中删除 vm](automation-remove-vms-from-change-tracking.md)。
* 若要排查功能错误，请参阅[排查更改跟踪和库存的问题](troubleshoot/change-tracking.md)。
