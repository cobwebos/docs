---
title: include 文件
description: include 文件
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: include
ms.date: 08/20/2018
ms.author: anshan
ms.custom: include file
ms.openlocfilehash: fb45ea02f365cf4e7b394e249f9b91a784e5469f
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2018
ms.locfileid: "46369816"
---
## <a name="grant-data-access"></a>授予数据访问权限

遵循以下步骤向用户主体授予数据访问权限：

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 查找时序见解环境。 在“搜索”框中，键入“时序见解”。 在搜索结果中选择“时序环境”。 

3. 从列表中选择时序见解环境。

4. 选择“数据访问策略”，然后选择“+ 添加”。
    ![管理时序见解源 - 环境](media/iot-tsi-data-access/getstarted-grant-data-access1.png)

5. 选择“选择用户”。  搜索用户名称或电子邮件地址，查找要添加的用户。 单击“选择”确认选择。 

    ![管理时序见解源 - 添加](media/iot-tsi-data-access/getstarted-grant-data-access2.png)

6. 选择“选择角色”。 为用户选择相应的访问角色：
    - 若要允许用户更改引用数据，以及与环境的其他用户共享保存的查询和透视，请选择“参与者”。 
    - 否则请选择“读取者”，允许用户查询环境中的数据，以及在环境中保存个人（非共享）查询。

    选择“确定”确认角色选择。

    ![管理时序见解源 - 选择用户](media/iot-tsi-data-access/getstarted-grant-data-access3.png)

7. 在“选择用户角色”页中，选择“确定”。

    ![管理时序见解源 - 选择角色](media/iot-tsi-data-access/getstarted-grant-data-access4.png)

8. “数据访问策略”页列出了用户和每个用户的角色。

    ![管理时序见解源 - 结果](media/iot-tsi-data-access/getstarted-grant-data-access5.png)