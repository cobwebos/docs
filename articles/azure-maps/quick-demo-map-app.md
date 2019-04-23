---
title: 使用 Azure Maps 执行交互式地图搜索 | Microsoft Docs
description: Azure 快速入门 - 使用 Azure Maps 创建演示版交互式地图搜索
author: walsehgal
ms.author: v-musehg
ms.date: 03/07/2019
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 6afe76aca388f1f6bd479f53eb4e18cc62c10584
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59268664"
---
# <a name="create-an-interactive-search-map-by-using-azure-maps"></a>使用 Azure Maps 创建交互式搜索地图

本文演示 Azure Maps 功能如何创建地图，为用户提供交互式搜索体验。 本文演示如何完成以下基本步骤：
* 创建自己的 Azure Maps 帐户。
* 获取要在演示版 Web 应用程序中使用的帐户密钥。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-an-account-and-get-your-key"></a>创建一个帐户并获取密钥

1. 在 [Azure 门户](https://portal.azure.com)的左上角，选择“创建资源”。
2. 在“在市场中搜索”框中，输入“Maps”。
3. 从“结果”中，选择“Maps”。 选择地图下面显示的“创建”按钮。
4. 在“创建 Azure Maps 帐户”页上，输入以下值：
   - 新帐户的名称。
   - 要用于此帐户的订阅。
   - 此帐户的资源组。 可以选择“新建”或“使用现有”选项，以便新建资源组或使用现有资源组。
   - 选择所选的定价层。
   - 阅读**许可**和**隐私声明**。 选择接受条款的复选框。
   - 最后，选择“创建”按钮。

     ![在门户中创建 Azure Maps 帐户](./media/quick-demo-map-app/create-account.png)

5. 帐户成功创建后，将其打开，查找帐户菜单的设置部分。 选择“密钥”，查看 Azure Maps 帐户的主要密钥和辅助密钥。 将“主密钥”值复制到本地剪贴板，以便在下一部分中使用。

## <a name="download-the-application"></a>下载应用程序

1. 下载或复制 [interactiveSearch.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html) 文件的内容。
2. 在本地将此文件的内容另存为 **AzureMapDemo.html**。 在文本编辑器中将其打开。
3. 搜索字符串 `<Your Azure Maps Key>`。 将其替换为在上一部分获取的**主密钥**值。

## <a name="open-the-application"></a>打开应用程序

1. 在所选的浏览器中打开文件 **AzureMapDemo.html**。
2. 观察显示的洛杉矶市地图。 进行缩放，查看地图如何自动根据缩放级别呈现更多或更少信息。 
3. 更改地图的默认中心。 在“AzureMapDemo.html”文件中，搜索名为“center”的变量。 将此变量的经度、纬度对值替换为新值“[-74.0060, 40.7128]”。 保存文件并刷新浏览器。
4. 尝试交互式搜索体验。 在演示版 Web 应用程序左上角的搜索框中，搜索“餐厅”。
5. 将鼠标移到搜索框下面显示的地址/位置列表上。 可以看到，地图上的相应图钉会弹出有关该位置的信息。 为保护私营企业的隐私，地图中显示的名称和地址都是虚构的。

    ![交互式搜索 Web 应用程序](./media/quick-demo-map-app/interactive-search.png)

## <a name="clean-up-resources"></a>清理资源

这些教程详细介绍如何通过帐户使用和配置 Azure Maps。 如何打算继续学习这些教程，请勿清除本快速入门中创建的资源。 如果不打算继续学习，请通过以下步骤来清理资源：

1. 关闭运行 **AzureMapDemo.html** Web 应用程序的浏览器。
2. 在 Azure 门户的左侧菜单中，选择“所有资源”。 然后选择你的 Azure Maps 帐户。 在“所有资源”边栏选项卡的顶部选择“删除”。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了 Azure Maps 帐户和演示版应用。 请查看以下教程以了解有关 Azure Maps 的信息：

> [!div class="nextstepaction"]
> [使用 Azure Maps 搜索附近兴趣点](tutorial-search-location.md)

有关更多代码示例和交互式编码体验，请参阅以下指南：

> [!div class="nextstepaction"]
> [使用 Azure Maps 搜索服务查找地址](how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [使用 Azure Maps Map Control](how-to-use-map-control.md)
