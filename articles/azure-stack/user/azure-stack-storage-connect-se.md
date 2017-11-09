---
title: "连接到 Azure 堆栈订阅的存储资源管理器"
description: "了解如何连接到 Azure 堆栈订阅的存储 Exporer"
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: c7e6d70148d39fd74f6409a0a239833f8e9f7614
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2017
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription"></a>连接到 Azure 堆栈订阅的存储资源管理器

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

Azure 存储资源管理器 （预览版） 是一个独立应用，可用于轻松地在 Windows、 macOS 和 Linux 上使用 Azure 堆栈存储数据。 有几个工具可用来将数据移到和从 Azure 堆栈存储。 有关详细信息，请参阅[数据传输适用于 Azure 堆栈存储工具](azure-stack-storage-transfer.md)。

在本文中，你将了解如何连接到 Azure 堆栈存储帐户使用存储资源管理器。 

如果你尚未安装存储资源管理器尚未，[下载](http://www.storageexplorer.com/)和并将其安装。

连接到 Azure 堆栈订阅后，你可以使用[Azure 存储资源管理器文章](../../vs-azure-tools-storage-manage-with-storage-explorer.md)以便使用 Azure 堆栈数据。 

## <a name="prepare-an-azure-stack-subscription"></a>准备 Azure 堆栈订阅

你需要访问 Azure 堆栈主计算机的桌面或存储资源管理器无法访问该 Azure 堆栈订阅的 VPN 连接。 若要了解如何设置到 Azure Stack 的 VPN 连接，请参阅[使用 VPN 连接到 Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)。

对于 Azure 堆栈开发工具包中，你需要导出 Azure 堆栈颁发机构的根证书。

### <a name="to-export-and-then-import-the-azure-stack-certificate"></a>若要导出和导入 Azure 堆栈证书

1. 打开`mmc.exe`Azure 堆栈主机计算机或在本地计算机使用 VPN 连接到 Azure 堆栈上。 

2. 在**文件**，选择**添加/删除管理单元中**，然后添加**证书**管理**我的用户帐户**。



3. 下**控制台 Root\Certificated （本地计算机） \Trusted Root Certification Authorities\Certificates**查找**AzureStackSelfSignedRootCert**。

    ![通过 mmc.exe 加载 Azure Stack 根证书][25]

4. 右键单击该证书，选择**所有任务** > **导出**，然后按照说明操作来导出证书的**的 base-64 编码 X.509 (。CER)**。  

    导出的证书会在下一步使用。
5. 启动存储资源管理器 （预览版），并且如果你看到**连接到 Azure 存储**对话框框中，将其取消。

6. 上**编辑**菜单上，指向**SSL 证书**，然后单击**导入证书**。 通过文件选取器对话框找到并打开在上一步导出的证书。

    导入后，系统会提示重新启动存储资源管理器。

    ![将证书导入存储资源管理器（预览版）][27]

现在你已准备好存储资源管理器连接到 Azure 堆栈订阅。

### <a name="to-connect-an-azure-stack-subscription"></a>若要将 Azure 堆栈订阅连接


1. 存储资源管理器（预览版）重新启动以后，即可选择“编辑”菜单，确保选中“目标 Azure Stack”。 如果尚未选中，请将其选中，然后重新启动存储资源管理器，使更改生效。 此配置是必需的，否则无法与 Azure Stack 环境兼容。

    ![确保选中“目标 Azure Stack”][28]

7. 在左窗格中，选择“管理帐户”。  
    此时会显示已登录的所有 Microsoft 帐户。

8. 若要连接到 Azure Stack 帐户，请选择“添加帐户”。

    ![添加 Azure Stack 帐户][29]

9. 在**连接到 Azure 存储**对话框中，在**Azure 环境**，选择**使用 Azure 堆栈环境**，然后单击**下一步**.

10. 若要使用与至少一个有效的 Azure 堆栈订阅关联的 Azure 堆栈帐户登录，填写**登录到 Azure 堆栈环境**对话框。  

    每个字段的详细信息如下所示：

    * **环境名称**：用户可以自定义此字段。
    * 
            **ARM 资源终结点**：Azure 资源管理器资源终结点的示例：

        * 为云运算符：<br> https://adminmanagement.local.azurestack.external   
        * 租户：<br> https://management.local.azurestack.external
 
    * **租户 Id**： 可选。 只有在必须指定目录的情况下，才提供此值。

12. 使用 Azure Stack 帐户成功登录后，左窗格将填充与该帐户关联的 Azure Stack 订阅。 选择要使用的 Azure Stack 订阅，并选择“应用”。 （选择或清除“所有订阅”复选框会选择所有列出的 Azure Stack 订阅，或者一个都不选。）

    ![填充“自定义云环境”对话框后，选择 Azure Stack 订阅][30]  
    左窗格会显示与所选 Azure Stack 订阅关联的存储帐户。

    ![存储帐户列表，其中包括 Azure Stack 订阅帐户][31]

## <a name="next-steps"></a>后续步骤
* [存储资源管理器 （预览版） 入门](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Azure 堆栈存储： 差异和注意事项](azure-stack-acs-differences.md)


* 若要了解有关 Azure 存储空间的详细信息，请参阅[Microsoft Azure 存储空间简介](../../storage/common/storage-introduction.md)

[25]: ./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png
[26]: ./media/azure-stack-storage-connect-se/export-root-cert-azure-stack.png
[27]: ./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png
[28]: ./media/azure-stack-storage-connect-se/select-target-azure-stack.png
[29]: ./media/azure-stack-storage-connect-se/add-azure-stack-account.png
[30]: ./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png
[31]: ./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png
