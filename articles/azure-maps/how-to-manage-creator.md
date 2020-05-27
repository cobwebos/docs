---
title: 管理 Azure Maps Creator
description: 本文介绍如何管理 Azure Maps Creator。
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 579294388dbcf9f785ef41e06505c14b6767565f
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83677949"
---
# <a name="manage-azure-maps-creator"></a>管理 Azure Maps Creator

Azure Maps Creator 允许你创建专用室内定位数据。 使用 Azure Maps API 和室内定位模块，可开发动态交互式室内定位 Web 应用程序。 目前，Creator 只在使用 S1 定价层的美国提供。

本文将指导你完成在 Azure Maps 帐户中创建和删除 Creator 资源的步骤。

## <a name="create-creator-resource"></a>创建 Creator 资源

1. 登录到 [Azure 门户](https://portal.azure.com)

2. 选择你的 Azure Maps 帐户。 如果在“最近使用的资源”下看不到 Azure Maps 帐户，请导航到“Azure 门户”菜单。 选择“所有资源”， 找到并选择你的 Azure Maps 帐户。

    ![Azure Maps 门户主页](./media/how-to-manage-creator/select-maps-account.png)

3. 在“Azure Maps 帐户”页上，导航到“Creator”下的“概述”选项。 单击“创建”以创建 Azure Maps Creator 资源。

    ![创建 Azure Maps Creator 页](./media/how-to-manage-creator/creator-blade-settings.png)

4. 输入 Creator 资源的“名称”和“位置”。 目前，仅在美国支持 Creator。 单击“查看 + 创建”。

   ![输入 Creator 帐户信息页](./media/how-to-manage-creator/creator-creation-dialog.png)

5. 检查设置并单击“创建”。

    ![确认 Creator 帐户设置页](./media/how-to-manage-creator/creator-create-dialog.png)

6. 部署完成后，会看到一个页面，其中包含成功或失败消息。

   ![资源部署状态页](./media/how-to-manage-creator/creator-resource-created.png)

7. 单击“转到资源”。 Creator 资源视图页将显示 Creator 资源和所选人口统计区域的状态。

    ![Creator 状态页](./media/how-to-manage-creator/creator-resource-view.png)

   >[!NOTE]
   >在 Creator 资源页上，可以通过单击 Azure Maps 帐户向后导航到它所属的 Azure Maps 帐户。

## <a name="delete-creator-resource"></a>删除 Creator 资源

若要删除 Creator 资源，请导航到 Azure Maps 帐户。 选择“Creator”下的“概述”。 单击“删除” 按钮。

>[!WARNING]
>删除 Azure Maps 帐户的 Creator 资源后，还会删除使用 Creator 服务创建的数据集、图块集和功能状态集。

![带“删除”按钮的 Creator 页](./media/how-to-manage-creator/creator-delete.png)

单击“删除”按钮，然后键入 Creator 名称以确认删除。 删除资源后，会看到一个确认页，如下图所示：

![带删除确认的 Creator 页](./media/how-to-manage-creator/creator-confirmdelete.png)

## <a name="authentication"></a>身份验证

Creator 继承 Azure Maps 访问控制 (IAM) 设置。 必须使用身份验证和授权规则发送数据访问的所有 API 调用。

Creator 使用情况数据合并到 Azure Maps 使用情况图表和活动日志中。  有关详细信息，请参阅[在 Azure Maps 中管理身份验证](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)。

## <a name="access-to-creator-services"></a>访问 Creator 服务

仅在创建过程中选择的位置内才能访问 Creator 服务。 如果从所选位置外部对 Creator 服务进行调用，则会返回用户错误消息。 若要从所选位置外部进行调用，服务 URL 必须包含所选位置的地理前缀。 例如，如果 Creator 是在美国创建的，则必须将对转换服务的所有调用提交到 `us.atlas.microsoft.com/conversion/convert`。

此外，导入到 Creator 的所有数据都应上传到与 Creator 资源相同的地理位置。 例如，如果在美国预配了 Creator，则所有原始数据都应通过 `us.atlas.microsoft.com/mapData/upload` 上传。

## <a name="next-steps"></a>后续步骤

室内定位 Creator 简介：

> [!div class="nextstepaction"]
> [数据上传](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [数据转换](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [数据集](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [图块集](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [功能状态集](creator-indoor-maps.md#feature-statesets)

了解如何使用 Creator 在应用程序中呈现室内定位：

> [!div class="nextstepaction"]
> [Azure Maps Creator 教程](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [室内定位的动态样式](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [使用室内定位模块](how-to-use-indoor-module.md)