---
title: 获取无人机图像
description: 本文介绍如何从合作伙伴获取无人机图像。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 899abc4529e4ca24a4d8c6334d458c968a1fcffa
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851241"
---
# <a name="get-drone-imagery-from-drone-partners"></a>从无人机合作伙伴获取无人机图像

本文介绍如何将无人机图像合作伙伴的 orthomosaic 数据引入 Azure FarmBeats Datahub。 Orthomosaic 是一种由无人机收集的数据进行几何纠正和拼接的高空图或图像。

目前支持以下图像合作伙伴。

  ![FarmBeats 无人机图像合作伙伴](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

将无人机图像数据与 Azure FarmBeats 集成可帮助你获取从你在场的无人机航班到数据中心的 orthomosaic 数据。 数据可用后，可在 FarmBeats 加速器中查看。 数据可用于数据合成、人工智能和机器学习模型生成。

## <a name="before-you-begin"></a>开始之前

  - 请确保已部署 Azure FarmBeats。 有关如何部署 FarmBeats 的信息，请参阅[Deploy FarmBeats](prepare-for-deployment.md)。
  - 确保你有要在 FarmBeats 系统中定义无人机图像的场。

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>启用无人机图像与 FarmBeats 的集成 

向设备提供商提供以下信息以启用与 FarmBeats 的集成： 
 - API 终结点 
 - 租户 ID 
 - 客户端 ID 
 - 客户端机密 

执行以下步骤。

1. 下载此[脚本](https://aka.ms/farmbeatspartnerscript)，并将其解压缩到本地驱动器。 Zip 文件内有两个文件。 
2. 登录到 [Azure 门户](https://portal.azure.com/)，并打开 Azure Cloud Shell。 此选项在门户右上角的工具栏上可用。

    ![打开门户右上栏 Azure Cloud Shell](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. 请确保将环境设置为**PowerShell**。

    ![PowerShell 设置](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. 上传你在 Cloud Shell 实例的步骤1中下载的两个文件。 

    ![上传文件](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. 中转到上载文件的目录。 默认情况下，会将其上传到 "用户名" 下的主目录。 
6. 运行以下脚本：

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```

7. 按照屏幕上的说明来捕获 API 终结点、租户 ID、客户端 ID、客户端密钥和 EventHub 连接字符串的值。

    将所需的凭据输入到合作伙伴的无人机 software 系统后，可以从 FarmBeats 系统中导入所有场。 然后，可以使用场详细信息进行航班路径规划和无人机映像收集。

    无人机提供程序的软件处理原始映像后，无人机软件系统会将拼接 orthomosaic 和其他已处理的图像上传到数据中心。

## <a name="view-drone-imagery"></a>查看无人机图像

将数据发送到 FarmBeats 数据中心后，可以使用 FarmBeats Datahub Api 查询场景存储。

或者，你可以在**场详细信息**页上查看最新的无人机图像。 若要查看映像，请执行以下步骤。 

1. 选择要将图像上传到的场。 此时将显示**场**详细信息页。
2. 向下滚动到最新的**精度地图**部分。
3. 查看**无人机**图像部分中的图像。

    ![无人机图像部分](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>下载无人机图像

选择 "无人机图像" 部分时，会打开一个弹出窗口，显示无人机 orthomosaic 的高分辨率图像。

![高分辨率 orthomosaic](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>查看所有无人机映射

无人机提供程序上载的文件和映像显示在**Maps**部分。 选择 "**映射**" 部分，按**场**进行筛选，然后选择相应的文件进行查看和下载。

  ![Maps 部分](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>后续步骤

了解如何使用 FarmBeats Datahub [api](references-for-farmbeats.md#rest-api)获取无人机图像。
