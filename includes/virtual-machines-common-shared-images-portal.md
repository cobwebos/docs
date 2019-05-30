---
title: include 文件
description: include 文件
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/29/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 291ec651061b7a8a3ea3c0645a6bd6581d529ef6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245014"
---
## <a name="sign-in-to-azure"></a>登录 Azure 

通过 https://portal.azure.com 登录到 Azure 门户。

> [!NOTE]
> 如果你注册使用预览版期间共享图像库，您可能需要重新注册`Microsoft.Compute`提供程序。 打开[Cloud Shell](https://shell.azure.com/bash)和类型： `az provider register -n Microsoft.Compute`

## <a name="create-an-image-gallery"></a>创建映像库

映像库是用于启用映像共享的主要资源。 允许用于库名称的字符为大写或小写字母、数字、点和句点。 库名称不能包含短划线。  库名称在你的订阅中必须唯一。 

以下示例在“myGalleryRG”资源组中创建名为“myGallery”的库   。

1. 在 Azure 门户的左上角选择“创建资源”。 
1. 使用类型**共享映像库**在搜索框中，选择**共享映像库**结果中。
1. 在中**共享映像库**页上，单击**创建**。
1. 选择正确的订阅。
1. 在**资源组**，选择**新建**并键入*myGalleryRG*的名称。
1. 在中**名称**，类型*myGallery*库的名称。
1. 保留为默认值**区域**。
1. 您可以如键入库的简短说明*我的映像库进行测试。* 然后单击**查看 + 创建**。
1. 验证通过后，选择**创建**。
1. 部署完成后，选择**转到资源**。
   
## <a name="create-an-image-definition"></a>创建映像定义 

映像定义为映像创建一个逻辑分组。 它们用于管理有关映像版本的信息，这些版本是在其中创建的。 映像定义名称可能包含大写或小写字母、数字、点、短划线和句点。 若要详细了解可以为映像定义指定的值，请参阅[映像定义](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions)。

创建您的库内的库映像定义。 在此示例中，名为的库映像*myImageDefinition*。

1. 在为新的映像库页上，选择**添加新的图像定义**从页面顶部。 
1. 有关**图像定义名称**，类型*myImageDefinition*。
1. 有关**操作系统**，选择正确的选项根据源映像。
1. 有关**发布服务器**，类型*myPublisher*。 
1. 有关**产品/服务**，类型*myOffer*。
1. 有关**SKU**，类型*mySKU*。
1. 完成后，选择**查看 + 创建**。
1. 映像定义通过验证后，选择**创建**。
1. 部署完成后，选择**转到资源**。


## <a name="create-an-image-version"></a>创建映像版本

从托管映像创建的映像版本。 在此示例中，映像版本为 1.0.0，该版本被复制到美国中西部和美国中南部数据中心    。 选择复制的目标区域时，请记住，你还需包括源区域作为复制的目标。 

允许用于映像版本的字符为数字和句点。 数字必须在 32 位整数范围内。 格式：*MajorVersion*.*MinorVersion*.*Patch*。

1. 在你的图像定义页中，选择**添加版本**从页面顶部。
1. 在中**区域**，选择托管的映像的存储位置的区域。 映像版本需要在从创建托管映像所在的同一区域中创建。
1. 有关**名称**，类型*1.0.0*。 映像版本名称应遵循*主要*。*次要*。*修补程序*使用整数格式。 
1. 在中**源图像**，从下拉列表中选择源托管的映像。
1. 在中**从最新的排除**，保留默认值为*否*。
1. 有关**期限**，从几个在将来几个月的日历选择一个日期。
1. 在中**复制**，将保留**默认副本计数**为 1。 您需要将复制到源区域，因此保留为默认值的第一个副本，然后选择要进行的第二个副本区域*美国东部*。
1. 完成后，选择**查看 + 创建**。 Azure 将验证配置。
1. 映像版本通过验证后，选择**创建**。
1. 部署完成后，选择**转到资源**。

可能需要一段时间才能将该映像复制到所有目标区域。

## <a name="share-the-gallery"></a>共享库

我们建议你共享的映像库级别的访问权限。 下面逐步讲解如何通过共享你刚刚创建的库。

1. 打开 [Azure 门户](https://portal.azure.com)。
1. 在左侧菜单中，选择**资源组**。 
1. 在资源组的列表中选择**myGalleryRG**。 你的资源组的边栏选项卡将打开。
1. 在左侧的菜单**myGalleryRG**页上，选择**访问控制 (IAM)** 。 
1. 下**添加的角色分配**，选择**添加**。 **添加的角色分配**窗格也会打开。 
1. 在“角色”下，选择“读取者”。  
1. 下**分配访问权限**，保留默认值为**Azure AD 用户、 组或服务主体**。
1. 下**选择**，你想要邀请的人员的电子邮件地址中的类型。
1. 如果用户是组织外部，将看到消息**此用户将发送一封电子邮件，使其与 Microsoft 协作。** 选择电子邮件地址的用户，然后单击**保存**。

如果用户是组织外部，他们将得到的电子邮件邀请加入组织。 用户需要接受邀请后，则他们将能够看到库及其所有的图像定义和在其列表中的资源的版本。

