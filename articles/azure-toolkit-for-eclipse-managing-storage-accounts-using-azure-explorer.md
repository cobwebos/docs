---
title: "使用用于 Eclipse 的 Azure 资源管理器管理存储帐户 | Microsoft Docs"
description: "了解如何使用用于 Eclipse 的 Azure 资源管理器管理 Azure 存储帐户。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/14/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 3c95ddde2909cd8dee055724ba01f22647627bd3
ms.lasthandoff: 04/22/2017


---

# <a name="managing-storage-accounts-using-the-azure-explorer-for-eclipse"></a>使用用于 Eclipse 的 Azure 资源管理器管理存储帐户

Azure 资源管理器是用于 Eclipse 的 Azure 工具包的一部分，它为 Java 开发人员提供易用的解决方案，用于从 Eclipse IDE 内部管理其 Azure 帐户中的存储帐户。

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-eclipse-show-azure-explorer](../includes/azure-toolkit-for-eclipse-show-azure-explorer.md)]

## <a name="creating-a-storage-account-in-eclipse"></a>在 Eclipse 中创建存储帐户

以下步骤将引导你完成使用 Azure 资源管理器创建存储帐户的步骤。

1. 遵循[用于 Eclipse 的 Azure 工具包的登录说明]一文中的步骤登录到 Azure 帐户。

1. 在“Azure 资源管理器”视图中，展开 Azure 节点，右键单击“存储帐户”，然后单击“创建存储帐户”。
   ![“创建存储帐户”菜单][CS01]

1. 显示“创建存储帐户”对话框时，指定以下选项：![“新建存储帐户”对话框][CS02]

   a. **名称**：指定要用于新存储帐户的名称。

   b. **订阅**：为新的存储帐户指定要使用的 Azure 订阅。

   c. **资源组**：为虚拟机指定资源组；需要选择以下选项之一：
      * **新建**：指定要创建新的资源组。
      * **使用现有**：指定将从列表中选择与 Azure 帐户关联的资源组。

   d.单击“下一步”。 **区域**：指定将创建存储帐户的位置；例如“美国西部”。

   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 **帐户类型**：指定要创建的存储帐户的类型；例如“Blob 存储”。 （有关详细信息，请参阅[关于 Azure 存储帐户]。）

   f. **性能**：指定要使用哪一个从所选发布者提供的存储帐户；例如，“高级”。 （有关详细信息，请参阅 [Azure 存储可伸缩性和性能目标]。）

   g. **复制**：指定存储帐户的复制；例如“区域冗余”。 （有关详细信息，请参阅 [Azure 存储复制]。）

1. 指定上述所有选项后，请单击“创建”。

## <a name="creating-a-storage-container-in-eclipse"></a>在 Eclipse 中创建存储容器

以下步骤将引导你完成使用 Azure 资源管理器创建存储容器的步骤。

1. 在 Azure 资源管理器中，右键单击要在其中创建容器的存储帐户，然后单击“创建 Blob 容器”。
   ![“创建存储容器”菜单][CC01]

1. 显示“创建 Blob 容器”对话框时，指定容器的名称，然后单击“确定”。 （有关命名存储容器的详细信息，请参阅[命名和引用容器、Blob 和元数据]。）![“创建存储容器”对话框][CC02]

## <a name="deleting-a-storage-container-in-eclipse"></a>在 Eclipse 中删除存储容器

若要使用 Azure 资源管理器删除存储容器，请使用以下步骤：

1. 在 Azure 资源管理器中，右键单击存储容器，然后单击“删除”。
   ![“删除存储容器”菜单][DC01]

1. 出现提示时，单击“确定”删除存储容器。
   ![“删除存储容器”对话框][DC02]

## <a name="deleting-a-storage-account-in-eclipse"></a>在 Eclipse 中删除存储帐户

若要使用 Azure 资源管理器删除存储帐户，请使用以下步骤：

1. 在“Azure 资源管理器”视图中，右键单击存储帐户，然后选择“删除”。
   ![“删除存储帐户”菜单][DS01]

1. 出现提示时，请单击“确定”删除存储帐户。
   ![“删除存储帐户”对话框][DS02]

## <a name="see-also"></a>另请参阅
有关 Azure 存储帐户大小和定价的详细信息，请参阅以下链接：

* [Microsoft Azure 存储空间简介]
* [关于 Azure 存储帐户]
* Azure 存储帐户大小
  * [Azure 中的 Windows 存储帐户的大小]
  * [Azure 中的 Linux 存储帐户的大小]
* Azure 存储帐户定价
  * [Windows 存储帐户定价]
  * [Linux 存储帐户定价]

有关 Azure Toolkits for Java IDE 的详细信息，请参阅以下链接：

* [用于 Eclipse 的 Azure 工具包]
  * [用于 Eclipse 的 Azure 工具包的新增功能]
  * [安装用于 Eclipse 的 Azure 工具包]
  * [用于 Eclipse 的 Azure 工具包的登录说明]
  * [在 Eclipse 中创建 Azure 的 Hello World Web 应用]
* [用于 IntelliJ 的 Azure 工具包]
  * [用于 IntelliJ 的 Azure 工具包的新增功能]
  * [安装用于 IntelliJ 的 Azure 工具包]
  * [用于 IntelliJ 的 Azure 工具包的登录说明]
  * [在 IntelliJ 中创建 Azure 的 Hello World Web 应用]

有关将 Azure 与 Java 配合使用的详细信息，请参阅 [Azure Java 开发人员中心]和[用于 Visual Studio Team Services 的 Java 工具]。

<!-- URL List -->

[用于 Eclipse 的 Azure 工具包]: ./azure-toolkit-for-eclipse.md
[用于 IntelliJ 的 Azure 工具包]: ./azure-toolkit-for-intellij.md
[在 Eclipse 中创建 Azure 的 Hello World Web 应用]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[在 IntelliJ 中创建 Azure 的 Hello World Web 应用]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[安装用于 Eclipse 的 Azure 工具包]: ./azure-toolkit-for-eclipse-installation.md
[安装用于 IntelliJ 的 Azure 工具包]: ./azure-toolkit-for-intellij-installation.md
[用于 Eclipse 的 Azure 工具包的登录说明]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[用于 IntelliJ 的 Azure 工具包的登录说明]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[用于 Eclipse 的 Azure 工具包的新增功能]: ./azure-toolkit-for-eclipse-whats-new.md
[用于 IntelliJ 的 Azure 工具包的新增功能]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java 开发人员中心]: https://azure.microsoft.com/develop/java/
[用于 Visual Studio Team Services 的 Java 工具]: https://java.visualstudio.com/

[Microsoft Azure 存储空间简介]: /azure/storage/storage-introduction
[关于 Azure 存储帐户]: /azure/storage/storage-create-storage-account
[Azure 存储复制]: /azure/storage/storage-redundancy
[Azure 存储可伸缩性和性能目标]: /azure/storage/storage-scalability-targets
[命名和引用容器、Blob 和元数据]: http://go.microsoft.com/fwlink/?LinkId=255555

[Azure 中的 Windows 存储帐户的大小]: /azure/virtual-machines/virtual-machines-windows-sizes
[Azure 中的 Linux 存储帐户的大小]: /azure/virtual-machines/virtual-machines-linux-sizes
[Windows 存储帐户定价]: /pricing/details/virtual-machines/windows/
[Linux 存储帐户定价]: /pricing/details/virtual-machines/linux/

<!-- IMG List -->

[CS01]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CS01.png
[CS02]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CS02.png
[CC01]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CC01.png
[CC02]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CC02.png

[DS01]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DS01.png
[DS02]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DS02.png
[DC01]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DC01.png
[DC02]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DC02.png

