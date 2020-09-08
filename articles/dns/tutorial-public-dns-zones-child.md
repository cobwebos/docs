---
title: 创建 Azure 子 DNS 区域
titleSuffix: Azure DNS
description: 有关如何在 Azure 门户中创建子 DNS 区域的教程。
author: jonbeck
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: tutorial
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 7/16/2020
ms.author: jonbeck
ms.openlocfilehash: 3f35d39634470ccacffa4d35c272a82725e9001c
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89080259"
---
# <a name="tutorial-creating-a-new-child-dns-zone"></a>教程：创建新的子 DNS 区域

在本教程中，你将了解如何执行以下操作： 

> [!div class="checklist"]
> * 登录到 Azure 门户。
> * 通过新的 DNS 区域创建子 DNS 区域。
> * 通过父 DNS 区域创建子 DNS 区域。
> * 验证新的子 DNS 区域的 NS 委派。



## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。  如果没有帐户，可以[免费创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 现有的父 Azure DNS 区域。  

就本教程而言，我们将使用 contoso.com 作为父区域，使用 subdomain.contoso.com 作为子域名。  请将 contoso.com 替换为你的父域名，将 subdomain 替换为你的子域。  如果尚未创建父 DNS 区域，请参阅[使用 Azure 门户创建 DNS 区域](https://docs.microsoft.com/azure/dns/dns-getstarted-portal#create-a-dns-zone)中的步骤。 


## <a name="sign-in-to-azure-portal"></a>登录到 Azure 门户

使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com/)。
如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。

可以通过两种方式创建子 DNS 区域。
1.  通过“创建 DNS 区域”门户页。
1.  通过父 DNS 区域的配置页。


## <a name="create-child-dns-zone-via-create-dns-zone"></a>通过“创建 DNS 区域”创建子 DNS 区域

在本步骤中，我们将创建一个名为 subdomain.contoso.com 的新子 DNS 区域，并将其委派给现有的父 DNS 区域 contoso.com。 你将使用“创建 DNS 区域”页上的选项卡来创建 DNS 区域。
1.  在 Azure 门户菜单或“主页”页上，选择“创建资源” 。 此时会显示“新建”窗口。
1.  选择“网络”，选择“DNS 区域”，然后选择“添加”按钮。

1.  在“基本信息”选项卡上，键入或选择以下值：
    * 订阅：选择要在其中创建区域的订阅。
    * 资源组：输入现有资源组，或者可以通过选择“新建”来创建一个新的资源组，输入 MyResourceGroup，然后选择“确定”。 资源组名称在 Azure 订阅中必须唯一。
    * 选中此复选框：**此区域是 Azure DNS 中已托管的现有区域的子区域**
    * **父区域订阅**：在此下拉列表中，搜索和/或选择在其中创建了父 DNS 区域 contoso.com 的订阅名称。
    * **父区域**：在搜索栏中，键入 contoso.com 以将其加载到下拉列表中。 加载后，从下拉列表中选择“contoso.com”。
    * **名称：** 在本教程示例中，键入 subdomain。 请注意，当我们在上一步中选择父区域时，父 DNS 区域名称 contoso.com 会自动添加为名称后缀。

1. 在完成时选择“下一步:查看 + 创建”。
1. 在“查看 + 创建”选项卡上查看摘要，更正任何验证错误，然后选择“创建” 。
创建区域可能需要几分钟。

 
    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-inline.png" alt-text="“创建 DNS 区域”页的屏幕截图。" lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-expanded.png":::

## <a name="create-child-dns-zone-via-parent-dns-zone-overview-page"></a>通过父 DNS 区域概述页创建子 DNS 区域
还可以使用父区域概述页中的“子区域”按钮，创建一个新的子 DNS 区域并将其委派给父 DNS 区域。 使用此按钮会自动预填充子区域的父参数。 

1.  在 Azure 门户中的“所有资源”下，打开 MyResourceGroup 资源组中的 contoso.com DNS 区域。 可以在“按名称筛选”框中输入 contoso.com，以便更轻松地找到它。
1.  在 DNS 区域概述页上，选择“+ 子区域”按钮。

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-inline.png" alt-text="子区域按钮的屏幕截图。" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-expanded.png":::

1.  随即将打开“创建 DNS 区域”页。 在此页面上，子区域选项处于选中状态，父区域订阅和父区域已填充内容。
1.  在本教程示例中，键入名称作为子区域。 请注意，父 DNS 区域名称 contoso.com 会自动添加为名称前缀。
1.  在完成时选择“下一步:  标记”，然后选择“下一步:  查看 + 创建”。
1.  在“查看 + 创建”选项卡上查看摘要，更正任何验证错误，然后选择“创建” 。

    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-inline.png" alt-text="所选子区域的屏幕截图" border="true"  lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-expanded.png":::
## <a name="verify-child-dns-zone"></a>验证子 DNS 区域
现在，你已经创建了一个新的子 DNS 区域 subdomain.contoso.com。 若要验证委派是否正确，你需要检查子区域的名称服务器 (NS) 记录是否在父区域中，具体如下所述。  

**检索子 DNS 区域的名称服务器：**

1.  在 Azure 门户中的“所有资源”下，打开 MyResourceGroup 资源组中的 subdomain.contoso.com DNS 区域。 可以在“按名称筛选”框中输入 subdomain.contoso.com，以便更轻松地找到它。
1.  从 DNS 区域概述页中检索名称服务器。 在本示例中，系统为区域 contoso.com 分配了名称服务器 ns1-08.azure-dns.com、ns2-08.azure-dns.net、ns3-08.azure-dns.org 和 ns4-08.azure-dns.info ：

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-inline.png" alt-text="子区域名称服务器的屏幕截图" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-expanded.png":::
**验证父 DNS 区域中的 NS 记录：**

现在，在本步骤中，我们转到父 DNS 区域 contoso.com，检查是否为其子区域名称服务器创建了 NS 记录集条目。

1. 在 Azure 门户中的“所有资源”下，打开 MyResourceGroup 资源组中的 contoso.com DNS 区域 。 可以在“按名称筛选”框中输入 contoso.com，以便更轻松地找到它。
1.  在 contoso.com DNS 区域概述页上，检查记录集。
1.  你会发现在父 DNS 区域中已经创建了类型为 NS、名称为 subdomain 的记录集。 检查此记录集的值，它类似于我们在上一步中从子 DNS 区域检索到的名称服务器列表。

     :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-inline.png" alt-text="子区域名称服务器验证的屏幕截图" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-expanded.png":::
## <a name="clean-up-resources"></a>清理资源
当你不再需要在本教程中创建的资源时，请通过删除 MyResourceGroup 资源组来删除它们。 打开 MyResourceGroup 资源组，并选择“删除资源组”********。



## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure DNS 专用区域方案](private-dns-scenarios.md)
