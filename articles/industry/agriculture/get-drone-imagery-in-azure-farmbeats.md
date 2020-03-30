---
title: 获取无人机图像
description: 本文介绍如何从合作伙伴处获取无人机图像。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 3e452cd548738e5f211899d3a6a676f883d800ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132052"
---
# <a name="get-drone-imagery-from-drone-partners"></a>从无人机合作伙伴处获取无人机图像

本文介绍如何将无人机影像合作伙伴的正交镶嵌数据引入 Azure FarmBeats Datahub。 正形马赛克是一种航空插图或图像，从无人机收集的数据中进行几何校正和缝合。

目前，支持以下影像合作伙伴。

  ![FarmBeats 无人机影像合作伙伴](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

将无人机影像数据与 Azure FarmBeats 集成可帮助您从服务器场上执行的无人机飞行中的正射马赛克数据获取到数据库。 数据可用后，您可以在 FarmBeats 加速器中查看数据。 这些数据可用于数据融合、人工智能和机器学习模型构建。

## <a name="before-you-begin"></a>开始之前

  - 确保安装了 Azure 服务器场节拍。 有关如何安装服务器场节拍的信息，请参阅[安装 Azure FarmBeats](install-azure-farmbeats.md)。
  - 确保您拥有希望在 FarmBeats 系统中定义无人机图像的服务器场。

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>实现无人机影像集成与 FarmBeats

向设备提供商提供以下信息，以便与 FarmBeats 集成：
 - API 终结点
 - 租户 ID
 - 客户端 ID
 - 客户端机密

执行以下步骤。

1. 下载此[脚本](https://aka.ms/farmbeatspartnerscript)，并将其提取到本地驱动器。 zip 文件内有两个文件。
2. 登录到 [Azure 门户](https://portal.azure.com/)，并打开 Azure Cloud Shell。 此选项在门户右上角的工具栏上可用。

    ![在门户的右上角栏上打开 Azure 云外壳](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. 确保环境设置为**PowerShell**。

    ![电源外壳设置](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. 上传从云壳实例中步骤 1 下载的两个文件。

    ![上传文件](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. 转到上载文件的目录。 默认情况下，它们在用户名下上载到主目录。
6. 运行以下脚本：

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```

7. 按照屏幕上的说明捕获 API 终结点、租户 ID、客户端 ID、客户端密钥和事件Hub连接字符串的值。

    将所需的凭据输入合作伙伴的无人机软件系统后，可以从 FarmBeats 系统导入所有服务器场。 然后，您可以使用服务器场详细信息来执行飞行路径规划和无人机图像收集。

    在无人机提供商的软件处理原始图像后，无人机软件系统将拼接的正交马赛克和其他经过处理的图像上传到数据hub中。

## <a name="view-drone-imagery"></a>查看无人机图像

将数据发送到 FarmBeats 数据库后，可以使用 FarmBeats Datahub API 查询场景存储。

或者，您可以在 **"农场详细信息**"页上查看最新的无人机图像。 要查看图像，请按照以下步骤操作。

1. 选择将影像上载到的服务器场。 将显示 **"服务器场**详细信息"页。
2. 向下滚动到最新的**精度地图**部分。
3. 在 **"无人机影像"** 部分查看图像。

    ![无人机影像部分](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>下载无人机图像

选择无人机影像部分时，将打开一个弹出窗口，显示无人机正射马赛克的高分辨率图像。

![高分辨率正射马赛克](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>查看所有无人机地图

无人机提供商上传的文件和图像将显示在 **"地图"** 部分。 选择 **"地图**"部分，按 **"服务器场"** 筛选，然后选择要查看和下载的相应文件。

  ![地图部分](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>后续步骤

了解如何使用 FarmBeats Datahub [API](rest-api-in-azure-farmbeats.md)获取无人机图像。
