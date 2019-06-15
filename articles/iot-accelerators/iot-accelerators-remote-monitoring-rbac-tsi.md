---
title: 远程监视数据的访问控制-Azure |Microsoft Docs
description: 本文介绍如何在远程监视解决方案加速器中为时序见解遥测数据资源浏览器配置访问控制
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 9d5d572c3e32e3645e65ba8d6fc28b567b3c1e9a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827218"
---
# <a name="configure-access-controls-for-the-time-series-insights-telemetry-explorer"></a>为时序见解遥测数据资源浏览器配置访问控制

本文介绍如何在远程监视解决方案加速器中为时序见解资源浏览器配置访问控制。 要允许解决方案加速器的用户访问时序见解资源管理器，需要授予每个用户数据访问权限。

数据访问策略授予的权限适用于：发出数据查询、操作环境中的引用数据，以及共享已保存的与环境关联的查询和透视。

## <a name="grant-data-access"></a>授予数据访问权限

遵循以下步骤向用户主体授予数据访问权限：

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 查找时序见解环境。 在“搜索”框中，键入“时序见解”   。 在搜索结果中选择“时序环境”  。 

3. 从列表中选择时序见解环境。

4. 选择“数据访问策略”，然后选择“+ 添加”   。
    ![管理时序见解源 - 环境](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access1.png)

5. 选择“选择用户”  。  搜索用户名称或电子邮件地址，查找要添加的用户。 单击“选择”确认选择  。 

    ![管理时序见解源 - 添加](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access2.png)

6. 选择“选择角色”  。 为用户选择相应的访问角色：
   - 若要允许用户更改引用数据，以及与环境的其他用户共享保存的查询和透视，请选择“参与者”  。 
   - 否则请选择“读取者”，允许用户查询环境中的数据，以及在环境中保存个人（非共享）查询  。

     选择“确定”确认角色选择  。

     ![管理时序见解源 - 选择用户](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access3.png)

7. 在“选择用户角色”页中，选择“确定”   。

    ![管理时序见解源 - 选择角色](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access4.png)

8. “数据访问策略”页列出了用户和每个用户的角色  。

    ![管理时序见解源 - 结果](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>后续步骤

本文介绍如何在远程监视解决方案加速器中为时序见解资源浏览器授予访问控制权限。

有关远程监视解决方案加速器的其他概念性信息，请参阅[远程监视体系结构](iot-accelerators-remote-monitoring-sample-walkthrough.md)

有关自定义远程监视解决方案的详细信息，请参阅[自定义和重新部署微服务](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->