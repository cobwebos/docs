---
title: "Azure Application Insights 中的资源、角色和访问控制 | Microsoft Docs"
description: "组织的见解的所有者、参与者和读者。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 49f736a5-67fe-4cc6-b1ef-51b993fb39bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 50a3cb726646c46d1f46767b51428eb751e6d3fe
ms.contentlocale: zh-cn
ms.lasthandoff: 03/21/2017


---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Application Insights 中的资源、角色和访问控制
通过使用 [Microsoft Azure 中基于角色的访问控制](../active-directory/role-based-access-control-configure.md)，可以控制哪些用户对 Azure [Application Insights][start] 中的数据具有读取和更新访问权限。

> [!IMPORTANT]
> 将访问权限分配给应用程序资源所属**资源组或订阅**中的用户（并非资源本身）。 分配 **Application Insights 组件参与者**角色。 这可确保对 Web 测试和警报及应用程序资源的访问实现统一控制。 [了解详细信息](#access)。
> 
> 

## <a name="resources-groups-and-subscriptions"></a>资源、组和订阅
首先，某些定义如下：

* **资源** - Microsoft Azure 服务的实例。 Application Insights 资源会收集、分析并显示从应用程序发送的遥测数据。  其他类型的 Azure 资源包括 Web 应用、数据库和 VM。
  
    若要查看你的资源，请打开 [Azure 门户][portal]并登录，然后单击“所有资源”。 为找到资源，请在筛选器字段中键入部分名称。
  
    ![Azure 资源的列表](./media/app-insights-resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**资源组**][group] - 每个资源都属于一个组。 组是一种管理相关资源的简便方式，对于访问控制尤其如此。 例如，可以将 Web 应用放入资源组中，监视应用和存储资源以保存导出的数据。

    ![依次选择“浏览”、“资源组”，然后选择某个组](./media/app-insights-resources-roles-access-control/11-group.png)

* [**订阅**](https://manage.windowsazure.com) - 若要使用 Application Insights 或其他 Azure 资源，则登录到 Azure 订阅。 每个资源组都属于一个 Azure 订阅，从中选择价格封装，并选择成员及其访问权限（如果它是组织订阅）。
* [**Microsoft 帐户**][account] - 用于登录到 Microsoft Azure 订阅、XBox Live、Outlook.com 和其他 Microsoft 服务的用户名和密码。

## <a name="access"></a> 控制资源组中的访问
请务必了解，除了为应用程序创建的资源外，也有为警报和 Web 测试单独隐藏的资源。 它们会附加到与应用程序相同的[资源组](#resource-group)。 还可以在其中放置其他 Azure 服务，例如网站或存储。

![Application Insights 中的资源](./media/app-insights-resources-roles-access-control/00-resources.png)

若要控制对这些资源的访问，建议：

* 在**资源组或订阅**级别控制访问。
* 为用户分配 **Application Insights 组件参与者**角色。 这将允许他们编辑 Web 测试、警报和 Application Insights 资源，而无需提供对组内任何其他服务的访问。

## <a name="to-provide-access-to-another-user"></a>对其他用户提供访问权限
你必须对订阅或资源组拥有所有者权限。

用户必须具有 [Microsoft 帐户][account]，或对[组织 Microsoft 帐户](../active-directory/sign-up-organization.md)的访问权限。 可以将访问权限提供给个人，也可以提供给 Azure Active Directory 中定义的用户组。

#### <a name="navigate-to-the-resource-group"></a>导航到资源组
在该处添加用户。

![在应用程序的资源边栏选项卡中，打开 Essentials、打开资源组，然后从中选择“设置/用户”。 单击“添加”。](./media/app-insights-resources-roles-access-control/01-add-user.png)

或者，可以上移到另一个级别并将用户添加到订阅。

#### <a name="select-a-role"></a>选择角色
![为新用户选择角色](./media/app-insights-resources-roles-access-control/03-role.png)

| 角色 | 在资源组中 |
| --- | --- |
| 所有者 |可以更改任何内容，包括用户访问权限 |
| 参与者 |可以编辑任何内容，包括所有资源 |
| Application Insights 组件参与者 |可以编辑 Application Insights 资源、Web 测试和警报 |
| 读取器 |可以查看但无法更改任何内容 |

“编辑”包括创建、删除和更新：

* 资源
* Web 测试
* 警报
* 连续导出

#### <a name="select-the-user"></a>选择用户

如果你希望的用户不在目录中，可以邀请具有 Microsoft 帐户的任何人。
（如果他们使用诸如 Outlook.com、OneDrive、Windows Phone 或 XBox Live 等服务，他们就会有 Microsoft 帐户。）

## <a name="related-content"></a>相关内容

* [Azure 中基于角色的访问控制](../active-directory/role-based-access-control-configure.md)

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../azure-resource-manager/resource-group-overview.md
[portal]: https://portal.azure.com/
[start]: app-insights-overview.md

