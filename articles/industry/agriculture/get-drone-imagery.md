---
title: 获取无人机图像
description: 介绍如何从合作伙伴获取无人机图像
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 598248a0efb3322a9c22a5e38e4986f5ba5142ab
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73798448"
---
# <a name="get-drone-imagery-from-drone-partners"></a>从无人机合作伙伴获取无人机图像

本文介绍了如何将无人机图像合作伙伴的 orthomosaic 数据引入到 Azure FarmBeats 数据中心。 目前支持以下图像合作伙伴：  

  ![项目场节拍](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

将无人机图像数据与 Azure FarmBeats 集成可帮助你获取从你在场的无人机航班到数据中心的 orthomosaic 数据。 数据可用后，可在 FarmBeats 加速器中查看数据，并可将其用于数据合成和 AI/ML 模型构建。

## <a name="before-you-begin"></a>开始之前

  - 请确保已部署 Azure FarmBeats。 若要部署，请访问[部署 FarmBeats](prepare-for-deployment.md)。
  - 确保你已在 FarmBeats 系统中定义无人机图像所需的场。

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>启用无人机图像与 FarmBeats 的集成   

你需要向设备提供商提供以下信息以启用与 FarmBeats 的集成：  
 - API 终结点  
 - 租户 ID  
 - 客户端 ID  
 - 客户端机密  

请执行以下步骤：

1. 下载此[脚本](https://aka.ms/farmbeatspartnerscript)，并将其解压缩到本地驱动器上。 你将在此 ZIP 文件中找到两个文件。  
2. 登录到[Azure 门户](https://portal.azure.com/)并打开 Cloud Shell （门户右上栏中提供了此选项）。   

    ![项目场节拍](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. 确保环境设置为**PowerShell**

    ![项目场节拍](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. 上载 Cloud Shell 中下载的两个文件（从上面的步骤1开始）。  

    ![项目场节拍](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. 中转到上载文件的目录。 （默认情况下，会将其上传到主目录 > 用户名。）  
6. 运行以下脚本：

    ```azurepowershell-interactive 

    PS> ./generateCredentials.ps1   

    ```

7. 按照屏幕上的说明来捕获 API 终结点、租户 ID、客户端 ID、客户端密钥和 EventHub 连接字符串的值。

    输入所需的凭据到合作伙伴的无人机 software 系统后，你将能够从 FarmBeats 系统导入所有场，并使用场详细信息来完成航班路径规划和无人机映像收集。

    无人机提供程序的软件处理原始映像后，无人机软件系统会将拼接 orthomosaic 和其他已处理的图像上传到数据中心。

## <a name="view-drone-imagery"></a>查看无人机图像

将数据发送到 FarmBeats 数据中心后，应该能够使用 FarmBeats 数据中心 Api 查询场景存储。

或者，您应该能够在**场详细信息**页中查看最新的无人机图像。 若要查看，请执行以下步骤：  

1. 选择要将图像上传到的场。 将显示**场**详细信息页。
2. 向下滚动到最新的**精度地图**部分。
3. 你应该能够在 "**无人机**图像" 部分中查看该图像。

    ![项目场节拍](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>下载无人机图像

选择 "无人机图像" 部分时，会打开一个弹出窗口，显示无人机 orthomosaic 的高分辨率图像。

![项目场节拍](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>查看所有无人机映射

无人机提供程序上载的文件和映像显示在 Maps 部分。 选择 "**映射**" 部分，按**场**进行筛选，然后选择要查看和下载的相应文件：

  ![项目场节拍](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>后续步骤

了解如何使用 FarmBeats 数据中心[api](references-for-farmbeats.md#rest-api)获取无人机影像。
