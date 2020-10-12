---
title: 'Azure CDN 端点多源 (预览版) '
description: 开始 Azure CDN 终结点多个源。
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: how-to
ms.date: 9/06/2020
ms.author: allensu
ms.openlocfilehash: f9293206526778f8c3de8a368a1916a2cb3f88c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89504667"
---
# <a name="azure-cdn-endpoint-multi-origin-preview"></a>Azure CDN 端点多源 (预览版) 

多源支持可消除停机时间，并建立全局冗余。 

通过选择 Azure CDN 终结点内的多个源，提供的冗余会通过探测每个源的运行状况并在必要时进行故障转移来实现风险的传播。

设置一个或多个源组，并选择默认的源组。 每个源组都是一个或多个来源的集合，可以采用类似的工作负荷。

> [!NOTE]
> 目前，此功能仅适用于 Microsoft Azure CDN。 

> [!IMPORTANT]
> Azure CDN endpoint 多源目前处于公共预览版中。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="create-the-origin-group"></a>创建源组

1. 登录到 [Azure 门户](https://portal.azure.com)

2. 选择 Azure CDN 配置文件，然后选择要为多源配置的终结点。

3. 在终结点配置中，选择 "**设置**" 下的 "**源**"：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-1.png" alt-text="CDN 终结点" border="true":::

4. 若要启用多源，需要至少一个源组。 选择 " **创建原始组**"：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-2.png" alt-text="CDN 终结点" border="true":::

5. 在 " **添加源组** 配置" 中，输入或选择以下信息：

   | 设置           | 值                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | 源组名称 | 输入源组的名称。                                   |
   | 探测状态      | 选择“启用”。 </br> Azure CDN 将从全球不同点运行运行状况探测，以确定源运行状况。 如果当前源组不处于活动状态，请不要启用此组，以免产生额外的费用。
   | 探测路径        | 用于确定运行状况的源中的路径。 |
   | 探测间隔    | 选择探测间隔为1、2或4分钟。                        |
   | 探测协议    | 选择 **HTTP** 或 **HTTPS**。                                         |
   | 探测方法      | 选择 " **Head** " 或 " **获取**"。                                           |
   | 默认源组 | 选择要设置为默认源组的复选框。
    
   :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-3.png" alt-text="CDN 终结点" border="true":::

6. 选择“添加”  。

## <a name="add-multiple-origins"></a>添加多个源

1. 在终结点的源设置中，选择 " **+ 创建源**"：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-5.png" alt-text="CDN 终结点" border="true":::

2. 在 " **添加源** 配置：" 中输入或选择以下信息：

   | 设置           | 值                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | 名称        | 输入源的名称。        |
   | 原点类型 | 选择 " **存储**"、" **云服务**"、" **Web 应用**" 或 " **自定义源**"。                                   |
   | 源服务器主机名        | 选择或输入源主机名。  下拉列表列出了在上一个设置中指定的类型的所有可用来源。 如果选择了 " **自定义源** " 作为源类型，请输入客户源服务器的域。 |
   | 源主机标头    | 输入想要 Azure CDN 每个请求发送的主机头，或保留默认值。                        |
   | HTTP 端口   | 输入 HTTP 端口。                                         |
   | HTTPS 端口     | 输入 HTTPS 端口。                                           |
   | 优先度    | 输入一个介于1和5之间的数字。       |
   | 重量      | 输入一个介于1和1000之间的数字。   |

    > [!NOTE]
    > 在源组中创建原点时，必须将其 accorded 优先级和权重。 如果源组只有一个原点，则默认优先级和权重设置为1。 如果源正常，则流量将路由到最高优先级来源。 如果源被确定为不正常，则连接将按优先级顺序转移到另一个源。 如果两个源具有相同的优先级，则按为源指定的权重分配流量 

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-6.png" alt-text="CDN 终结点" border="true":::

3. 选择“添加”  。

4. 选择 " **配置源** " 可以设置所有来源的源路径：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-7.png" alt-text="CDN 终结点" border="true":::

5. 选择“确定”  。

## <a name="configure-origins-and-origin-group-settings"></a>配置源和源组设置

拥有多个来源和源组后，可以将该来源添加到不同的组或将其删除。 同一组中的源应为类似的工作负荷提供服务。 流量将根据其运行状况、优先级和权重值分发到这些来源。 

1. 在 Azure CDN 终结点的源设置中，选择要配置的源组的名称：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="CDN 终结点" border="true":::

2. 在 " **更新源组**" 中，选择 " **+ 选择源**"：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-9.png" alt-text="CDN 终结点" border="true":::

4. 在下拉框中选择要添加到组的源，然后选择 **"确定"**。

5. 验证源是否已添加到组中，然后选择 " **保存**"：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-10.png" alt-text="CDN 终结点" border="true":::

## <a name="remove-origin-from-origin-group"></a>从源组中删除源

1. 在 Azure CDN 终结点的源设置中，选择源组的名称：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="CDN 终结点" border="true":::

2. 若要从原始组中删除源，请选择源旁边的垃圾桶图标，然后选择 " **保存**"：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-11.png" alt-text="CDN 终结点" border="true":::

## <a name="override-origin-group-with-rules-engine"></a>用规则引擎覆盖源组

使用标准规则引擎自定义如何将流量分布到不同的源组。

根据请求 URL 将流量分配到不同的组。

1. 在 CDN 终结点中，选择 "**设置**" 下的**规则引擎**：

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-12.png" alt-text="CDN 终结点" border="true":::

2. 选择 " **+ 添加规则**"。

3. 在 " **名称**" 中输入规则的名称。

4. 选择 " **+ 条件**"，然后选择 " **URL 路径**"。

5. 在下拉 **运算符** 中，选择 " **包含**"。

6. 在 " **值**" 中，输入 **/images**。

7. 选择 " **+ 添加操作**"，然后选择 " **源组替代**"。

8. 在 " **源组**" 中，在下拉框中选择源组。

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-13.png" alt-text="CDN 终结点" border="true":::

对于所有传入请求，如果 URL 路径包含 **/images**，则会将该请求分配给 "操作" 部分中的源组 ** (myorigingroup) **。 

## <a name="next-steps"></a>后续步骤
在本文中，已启用 Azure CDN endpoint 多源。

有关 Azure CDN 和本文中所述的其他 Azure 服务的详细信息，请参阅：

* [Azure CDN](./cdn-overview.md)
* [Azure CDN 产品功能比较](./cdn-features.md)
