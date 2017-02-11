---
title: "实施大型部署"
description: "了解如何使用 Azure Toolkit for Eclipse 实施大型部署。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 5e18bace-5df0-4af8-ad86-6151ea8bd823
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0c74663599e8f74ce80948abc3b9e1677bb86de0


---
# <a name="deploying-large-deployments"></a>实施大型部署
如果你的部署太大，从而无法包含在默认 approot 文件夹中，你可以使用本地存储资源作为 JDK 和应用程序服务器的部署根文件夹。

## <a name="to-use-a-local-storage-resource-as-the-deployment-root-folder-for-large-deployments"></a>使用本地存储资源作为大型部署的部署根文件夹
1. 创建新的本地存储资源。 资源的名称并不重要。 存储资源在角色级别定义。 访问本地存储配置对话框（可从中创建新的本地存储资源）的最快方式是使用以下步骤：在“项目资源管理器”视图中右键单击角色（如果看不到该角色，请展开 Azure 项目节点），单击“Azure”，然后单击“本地存储”。 在“本地存储”对话框中，单击“添加”创建新的本地存储资源。
2. 将所需大小设置为至少 2048 MB（指定更小的值可能会导致你在 approot 中遇到的相同文件大小问题）。
3. 确保已选中“回收角色实例时清除内容”；这有助于在回收角色实例时，防止部署启动逻辑与资源中的现有文件发生冲突。
4. 确保将“部署后存储资源目录路径的环境变量”值设置为字符串 **DEPLOYROOT**。 本地存储资源对话框如下所示。
    ![][ic667943]

或者，如果使用 **DEPLOYROOT** 作为本地资源的*名称*，并且不更改自动生成的环境变量名称（在此情况下将设置为 **DEPLOYROOT_PATH**），则这也适用于你的应用程序。

有关创建本地存储资源的其他信息可以在[本地存储属性][本地存储属性]中找到。

## <a name="see-also"></a>另请参阅
[用于 Eclipse 的 Azure 工具包][用于 Eclipse 的 Azure 工具包]

[在 Eclipse 中为 Azure 创建 Hello World 应用程序][在 Eclipse 中为 Azure 创建 Hello World 应用程序]

[安装用于 Eclipse 的 Azure 工具包][安装用于 Eclipse 的 Azure 工具包] 

有关将 Azure 与 Java 配合使用的详细信息，请参阅 [Azure Java 开发人员中心][Azure Java 开发人员中心]。

<!-- URL List -->

[Azure Java 开发人员中心]: http://go.microsoft.com/fwlink/?LinkID=699547
[用于 Eclipse 的 Azure 工具包]: http://go.microsoft.com/fwlink/?LinkID=699529
[在 Eclipse 中为 Azure 创建 Hello World 应用程序]: http://go.microsoft.com/fwlink/?LinkID=699533
[安装用于 Eclipse 的 Azure 工具包]: http://go.microsoft.com/fwlink/?LinkId=699546
[本地存储属性]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties

<!-- IMG List -->

[ic667943]: ./media/azure-toolkit-for-eclipse-deploying-large-deployments/ic667943.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268601.aspx -->



<!--HONumber=Nov16_HO3-->


