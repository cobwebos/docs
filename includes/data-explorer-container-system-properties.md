---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/08/2020
ms.author: orspodek
ms.openlocfilehash: f9788e4623ce60ad55d79558d1d77a17eb2a9f26
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779938"
---
### <a name="event-system-properties-mapping"></a>事件系统属性映射

如果在上表的 "**数据源**" 部分中选择了 "**事件系统属性**"，请在[Web UI](https://dataexplorer.azure.com/)中运行相关的 KQL 命令，以便创建正确的映射。

   **对于 csv 映射：**

    ```kusto
    .create table MyTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "messageid", "DataType":"string", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "userid", "DataType":"string", "Properties":{"Ordinal":"1"}},'
    '   { "column" : "other", "DataType":"int", "Properties":{"Ordinal":"2"}}'
    ']'
    ```
 
   **对于 json 映射：**

    ```kusto
    .create table MyTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "messageid", "datatype" : "string", "Properties":{"Path":"$.message-id"}},'
    '    { "column" : "userid", "Properties":{"Path":"$.user-id"}},'
    '    { "column" : "other", "Properties":{"Path":"$.other"}}'
    ']'
    ```

   > [!TIP]
   > * 必须在映射中包含所有所选属性。 
   > * 在 csv 映射中，属性顺序很重要。 系统属性必须在所有其他属性之前和其在 "**事件系统属性**" 下拉列表中的显示顺序列出。