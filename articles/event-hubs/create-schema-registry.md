---
title: 创建 Azure 事件中心架构注册表
description: 本文介绍如何在 Azure 事件中心命名空间中创建架构注册表。
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 90556e0843cda94dc79330321d027f8d28eb7d20
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91652187"
---
# <a name="create-an-azure-event-hubs-schema-registry-preview"></a> (预览创建 Azure 事件中心架构注册表) 
本文介绍如何在 Azure 事件中心托管的架构注册表中创建架构组。 有关 Azure 事件中心的架构注册表功能的概述，请参阅 [事件中心中的 Azure 架构注册表](schema-registry-overview.md)。

> [!NOTE]
> - **架构注册表**功能当前以**预览版**提供，不建议用于生产工作负荷。
> - 此功能仅在 **标准** 层和 **专用** 层中提供，而不是在 **基本** 层中提供。

## <a name="prerequisites"></a>必备条件
[创建事件中心命名空间](event-hubs-create.md#create-an-event-hubs-namespace)。 你还可以使用现有的命名空间。 

## <a name="create-a-schema-group"></a>创建架构组
1. 导航到 " **事件中心命名空间** " 页。 
1. 在左侧菜单中选择 " **架构注册表** "。 若要创建架构组，请在工具栏上选择 " **+ 架构组** "。 

    :::image type="content" source="./media/create-schema-registry/namespace-page.png" alt-text="架构注册表页&quot;:::
1. 在 &quot; **创建架构组** &quot; 页上，执行以下步骤：
    1. 输入架构组的 **名称** 。
    1. 对于 **序列化类型**，请选择应用于架构组中的所有架构的序列化格式。 目前， **avro** 是唯一受支持的类型，因此请选择 **avro**。 
    1. 为组中的所有架构选择 **兼容性模式** 。 对于 **Avro**，支持向前和向后兼容性模式。 
    1. 然后，选择 &quot; **创建** " 以创建架构组。 
1. 在架构组列表中选择 **架构组** 的名称。

    :::image type="content" source="./media/create-schema-registry/select-schema-group.png" alt-text="架构注册表页&quot;:::
1. 在 &quot; **创建架构组** &quot; 页上，执行以下步骤：
    1. 输入架构组的 **名称** 。
    1. 对于 **序列化类型**，请选择应用于架构组中的所有架构的序列化格式。 目前， **avro** 是唯一受支持的类型，因此请选择 **avro**。 
    1. 为组中的所有架构选择 **兼容性模式** 。 对于 **Avro**，支持向前和向后兼容性模式。 
    1. 然后，选择 &quot; **创建** " 页。

    :::image type="content" source="./media/create-schema-registry/schema-group-page.png" alt-text="架构注册表页&quot;:::
1. 在 &quot; **创建架构组** &quot; 页上，执行以下步骤：
    1. 输入架构组的 **名称** 。
    1. 对于 **序列化类型**，请选择应用于架构组中的所有架构的序列化格式。 目前， **avro** 是唯一受支持的类型，因此请选择 **avro**。 
    1. 为组中的所有架构选择 **兼容性模式** 。 对于 **Avro**，支持向前和向后兼容性模式。 
    1. 然后，选择 &quot; **创建** ":::
    

## <a name="add-a-schema-to-the-schema-group"></a>将架构添加到架构组
在本部分中，将使用 Azure 门户向架构组添加架构。 

1. 在 " **架构组** " 页上，选择工具栏上的 " **+ 架构** "。 
1. 在 " **创建架构** " 页上，执行以下步骤：
    1. 输入架构的 **名称** 。
    1. 在文本框中输入以下 **架构** 。 你还可以选择包含架构的文件。
    
        ```json
        {
            "type": "record",
            "name": "AvroUser",
            "namespace": "com.azure.schemaregistry.samples",
            "fields": [
                {
                    "name": "name",
                    "type": "string"
                },
                {
                    "name": "favoriteNumber",
                    "type": "int"
                }
            ]
        }
        ```
    1. 选择“创建”。 
1. 从架构列表中选择 **架构** 。 

    :::image type="content" source="./media/create-schema-registry/select-schema.png" alt-text="架构注册表页&quot;:::
1. 在 &quot; **创建架构组** &quot; 页上，执行以下步骤：
    1. 输入架构组的 **名称** 。
    1. 对于 **序列化类型**，请选择应用于架构组中的所有架构的序列化格式。 目前， **avro** 是唯一受支持的类型，因此请选择 **avro**。 
    1. 为组中的所有架构选择 **兼容性模式** 。 对于 **Avro**，支持向前和向后兼容性模式。 
    1. 然后，选择 &quot; **创建** " 页。 

    :::image type="content" source="./media/create-schema-registry/schema-overview-page.png" alt-text="架构注册表页&quot;:::
1. 在 &quot; **创建架构组** &quot; 页上，执行以下步骤：
    1. 输入架构组的 **名称** 。
    1. 对于 **序列化类型**，请选择应用于架构组中的所有架构的序列化格式。 目前， **avro** 是唯一受支持的类型，因此请选择 **avro**。 
    1. 为组中的所有架构选择 **兼容性模式** 。 对于 **Avro**，支持向前和向后兼容性模式。 
    1. 然后，选择 &quot; **创建** " 下拉列表中看到它们。 选择要切换到该版本架构的版本。 

## <a name="create-a-new-version-of-schema"></a>创建新的架构版本

1. 在文本框中更新架构，然后选择 " **验证**"。 在下面的示例中，已将一个新字段 `id` 添加到该架构中。 

    :::image type="content" source="./media/create-schema-registry/update-schema.png" alt-text="架构注册表页&quot;:::
1. 在 &quot; **创建架构组** &quot; 页上，执行以下步骤：
    1. 输入架构组的 **名称** 。
    1. 对于 **序列化类型**，请选择应用于架构组中的所有架构的序列化格式。 目前， **avro** 是唯一受支持的类型，因此请选择 **avro**。 
    1. 为组中的所有架构选择 **兼容性模式** 。 对于 **Avro**，支持向前和向后兼容性模式。 
    1. 然后，选择 &quot; **创建** " **保存**"。 

    :::image type="content" source="./media/create-schema-registry/compare-save-schema.png" alt-text="架构注册表页&quot;:::
1. 在 &quot; **创建架构组** &quot; 页上，执行以下步骤：
    1. 输入架构组的 **名称** 。
    1. 对于 **序列化类型**，请选择应用于架构组中的所有架构的序列化格式。 目前， **avro** 是唯一受支持的类型，因此请选择 **avro**。 
    1. 为组中的所有架构选择 **兼容性模式** 。 对于 **Avro**，支持向前和向后兼容性模式。 
    1. 然后，选择 &quot; **创建** " 页上为**版本**选择了。 

    :::image type="content" source="./media/create-schema-registry/new-version.png" alt-text="架构注册表页&quot;:::
1. 在 &quot; **创建架构组** &quot; 页上，执行以下步骤：
    1. 输入架构组的 **名称** 。
    1. 对于 **序列化类型**，请选择应用于架构组中的所有架构的序列化格式。 目前， **avro** 是唯一受支持的类型，因此请选择 **avro**。 
    1. 为组中的所有架构选择 **兼容性模式** 。 对于 **Avro**，支持向前和向后兼容性模式。 
    1. 然后，选择 &quot; **创建** ":::    
1. 选择 `1` 查看架构的版本1。 

    :::image type="content" source="./media/create-schema-registry/select-version.png" alt-text="架构注册表页&quot;:::
1. 在 &quot; **创建架构组** &quot; 页上，执行以下步骤：
    1. 输入架构组的 **名称** 。
    1. 对于 **序列化类型**，请选择应用于架构组中的所有架构的序列化格式。 目前， **avro** 是唯一受支持的类型，因此请选择 **avro**。 
    1. 为组中的所有架构选择 **兼容性模式** 。 对于 **Avro**，支持向前和向后兼容性模式。 
    1. 然后，选择 &quot; **创建** ":::    


## <a name="next-steps"></a>后续步骤
有关架构注册表的详细信息，请参阅 [事件中心中的 Azure 架构注册表](schema-registry-overview.md)。

