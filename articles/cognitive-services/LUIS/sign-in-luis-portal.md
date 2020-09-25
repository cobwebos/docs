---
title: 登录到 LUIS 门户
description: 如果你是登录到 LUIS 门户的新用户，则根据你的当前用户帐户，登录体验将略有不同。
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/08/2020
ms.topic: how-to
ms.author: a-sakand
author: skandil
ms.openlocfilehash: 22f5ab332c52a3b567b37089def6e4a494a15d29
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91309466"
---
# <a name="sign-in-to-luis-portal"></a>登录到 LUIS 门户

如果用户登录到 LUIS 门户，登录体验将略有不同，具体取决于你当前的用户帐户：
  * 与 Azure 订阅关联
  * 不与 Azure 订阅关联

## <a name="determine-account-type"></a>确定帐户类型

首次登录到 LUIS 门户时，请使用以下可视指示器来确定帐户类型。

### <a name="account-without-azure-subscription"></a>无 Azure 订阅的帐户

不与 Azure 订阅关联的帐户在右上方导航栏中具有 Azure 图标。 迁移到关联的帐户类型后，将不再显示该图标。

:::image type="content" source="media/sign-in/sign-in-with-account-without-azure-subscription.png" alt-text="部分屏幕-LUIS 导航栏与 Azure 图标的截图。":::

### <a name="account-with-azure-subscription"></a>Azure 订阅的帐户

与 Azure 订阅关联的帐户允许选择要使用的订阅和资源。

:::image type="content" source="media/sign-in/resource-selection.png" alt-text="部分屏幕-LUIS 门户和资源选择下拉选择框的截图。":::

## <a name="sign-in-with-account-associated-with-an-azure-subscription"></a>使用与 Azure 订阅关联的帐户登录

1. 登录 [LUIS 门户](https://www.luis.ai)并同意使用条款。

1. 你将有两个注册方法：

    * 继续使用 Azure 资源，这是推荐的路径，不久就会成为唯一的可用路径。 使用此路径，可以通过在订阅中选择现有资源或创建新资源来注册 LUIS 帐户和 Azure 创作资源。 这等效于注册迁移，而无需以后再进行 [迁移](luis-migration-authoring.md#what-is-migration) 。 所有用户将需要在2020年11月2日之前迁移。

    * 继续使用起始或试用密钥。 通过此路径，你可以通过提供的入门或试用资源登录到 LUIS，而无需创建任何资源。 如果选择此路径，最终需要 [迁移帐户](luis-migration-authoring.md#migration-steps) 并将应用程序链接到创作资源。 这就是为什么要选择继续使用 Azure 资源的路径。

    [了解有关创作和 starter 密钥的详细信息](luis-how-to-azure-subscription.md#luis-resources)。 这两个资源均可为你带来1000000个免费创作事务和1000个免费预测终结点事务。

    :::image type="content" source="media/sign-in/signup-landing-page.png" alt-text="部分屏幕截图，选择语言理解创作资源的类型。":::

1. 使用现有的创作资源

    :::image type="content" source="media/sign-in/signup-choose-resource.png" alt-text="选择创作资源":::

    如果订阅中已有 LUIS 创作资源，并在登录过程中将其与 LUIS 帐户关联，请选择 " **使用现有创作资源** " 选项，并提供以下信息：

    * **租户** - 与 Azure 订阅关联的租户。 你将不能从现有窗口切换租户。 可以通过在顶部栏中选择包含首字母缩写的最右侧头像来切换租户。
    * **订阅名称** -将与资源关联的订阅。 如果有多个订阅属于租户，请从下拉列表中选择所需的订阅。
    * **资源名称** -要将帐户关联到的创作资源。

    > [!Note]
    > 如果你正在查找的创作资源在下拉列表中灰显，则表示你已登录到其他区域门户。 [了解区域门户的概念](luis-reference-regions.md#luis-authoring-regions)。

1. 创建新的创作资源

    :::image type="content" source="media/sign-in/signup-create-resource.png" alt-text="创建创作资源":::

    **创建新的创作资源**时，请提供以下信息：

    * **租户** - 与 Azure 订阅关联的租户。 你将不能从现有窗口切换租户。 可以通过在顶部栏中选择包含首字母缩写的最右侧头像来切换租户。
    * **资源名称** -所选的自定义名称，用作创作事务的 URL 的一部分。 资源名称只能包含字母数字字符 "-"，并且不能以 "-" 开头或结尾。 如果名称中包含任何其他符号，则创建资源会失败。
    * **订阅名称** -将与资源关联的订阅。 如果有多个订阅属于租户，请从下拉列表中选择所需的订阅。
    * **资源组** -在订阅中选择的自定义资源组名称。 使用资源组可将 Azure 资源分组，以便进行访问和管理。 如果订阅中目前没有资源组，则不允许在 LUIS 门户中创建资源组。 转到 [Azure 门户](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) ，然后转到 LUIS 继续登录过程。

1. 选择路径后，可能需要几秒钟，直到出现 "已成功迁移你的帐户" 的符号。 完成后，选择 " **继续**"。

    :::image type="content" source="media/sign-in/signup-confirm-2.png" alt-text="确认创作资源":::

    > [!Note]
    > 如果在该区域中拥有与在门户中注册到的订阅相同的订阅和至少一个创作资源，则可能会自动登录到 LUIS 并将其与资源关联，而无需选择要传入的路径。


## <a name="sign-in-with-user-account-not-associated-with-an-azure-subscription"></a>使用未与 Azure 订阅关联的用户帐户登录

1. 登录到 [LUIS 门户](https://www.luis.ai) 并检查是否同意使用条款。

1. 完成后，选择 " **继续**"。 你将使用试用/starter 密钥自动登录。 这意味着，最终需要 [迁移帐户](luis-migration-authoring.md#migration-steps) 并将应用程序链接到创作资源。 若要执行迁移过程，需要登录 [Azure 免费试用版](https://azure.microsoft.com/free/)。

    :::image type="content" source="media/sign-in/signup-no-subscription.png" alt-text="无订阅方案":::

## <a name="troubleshooting"></a>疑难解答

* 如果从与登录到的门户不同的区域中的 Azure 门户创建创作资源，创作资源将灰显。
* 创建新资源时，请确保资源名称只包含字母数字字符 "-"，并且不能以 "-" 开头或结尾。 否则，操作将失败。
* 请确保对 [订阅拥有适当的权限来创建 Azure 资源](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)。 如果你没有适当的权限，请与你的订阅管理员联系，以获得足够的权限。

## <a name="next-steps"></a>后续步骤

* 了解如何 [启动新应用](luis-how-to-start-new-app.md)
