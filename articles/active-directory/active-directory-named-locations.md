---
title: "Azure Active Directory 中的命名位置 | Microsoft Docs"
description: "通过配置命名位置，可以避免让组织所拥有的 IP 地址针对不可能前往的异常位置风险事件类型误报。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: e11f19d518b22a7be4f8daf93304821b42e94a9b
ms.contentlocale: zh-cn
ms.lasthandoff: 05/25/2017

---
# <a name="named-locations-in-azure-active-directory"></a>Azure Active Directory 中的命名位置

使用 Azure Active Directory 的命名位置功能，可以标记组织中的受信任 IP 地址范围。 在你的环境中，可以在[风险事件](active-directory-reporting-risk-events.md)检测上下文中使用命名位置。 此功能有助于减少对“不可能转到异常位置”风险事件类型的误报数。 

## <a name="configuration"></a>配置

配置命名位置：

1. 以全局管理员身份登录到 [Azure 门户](https://portal.azure.com)。

2. 在左窗格中，单击“Azure Active Directory”。

    ![Azure Active Directory 链接位于左窗格中](./media/active-directory-named-locations/01.png)

3. 在“Azure Active Directory”边栏选项卡的“安全性”部分中，单击“条件性访问”。

    ![条件性访问命令](./media/active-directory-named-locations/05.png)


4. 在“Azure Active Directory”边栏选项卡的“管理”部分中，单击“命名位置”。

    ![命名位置命令](./media/active-directory-named-locations/06.png)


5. 在“命名位置”边栏选项卡上，单击“新建位置”。

    ![新建位置命令](./media/active-directory-named-locations/07.png)


6. 在“新建”边栏选项卡上执行以下操作：

    ![新建边栏选项卡](./media/active-directory-named-locations/08.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”框中，键入命名位置的名称。

    b. 在“IP 范围”框中，键入 IP 范围。 IP 范围需采用*无类别域际路由* (CIDR) 格式。  

    c. 单击“创建” 。



## <a name="what-you-should-know"></a>要点

批量更新：创建或更新命名位置时，若要进行批量更新，可以上传或下载含 IP 范围的 CSV 文件。 上传会将在文件中的 IP 范围添加到列表而不是覆盖列表。

![上传和下载链接](./media/active-directory-named-locations/09.png)


限制：可以定义最多 60 个命名位置，其中每个都分配有一个 IP 范围。 如果只配置了一个命名位置，则可为其定义最多 500 个 IP 范围。


## <a name="next-steps"></a>后续步骤

有关风险事件的详细信息，请参阅 [Azure Active Directory 风险事件](active-directory-reporting-risk-events.md)。


