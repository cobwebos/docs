---
title: "开发 U-SQL 用户定义的运算符 (UDO) | Microsoft Docs"
description: "了解如何开发可在 Data Lake Analytics 作业中使用和重复使用的用户定义的运算符。 "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: ef310a094667f390addd5d0df3dc68d67100d2f4
ms.contentlocale: zh-cn
ms.lasthandoff: 05/09/2017


---
# <a name="develop-u-sql-user-defined-operators-udos"></a>开发 U-SQL 用户定义的运算符 (UDO)
了解如何开发用户定义的运算符以处理 U-SQL 作业中的数据。

有关为 U-SQL 开发通用程序集的说明，请参阅[为 Azure Data Lake Analytics 作业开发 U-SQL 程序集](data-lake-analytics-u-sql-develop-assemblies.md)

## <a name="define-and-use-user-defined-operator-in-u-sql"></a>在 U-SQL 中定义和使用用户定义的运算符
**创建和提交 U-SQL 作业**

1. 在“文件”菜单中，单击“新建”，然后单击“项目”。
2. 选择“U-SQL 项目”类型。

    ![新建 U-SQL Visual Studio 项目](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. 单击 **“确定”**。 Visual Studio 将创建包含 Script.usql 文件的解决方案。
4. 在“解决方案资源管理器”中，展开 Script.usql，然后单击 “Script.usql.cs”。
5. 将以下代码粘贴到文件中：

        using Microsoft.Analytics.Interfaces;
        using System.Collections.Generic;

        namespace USQL_UDO
        {
            public class CountryName : IProcessor
            {
                private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
                {
                    {
                        "Deutschland", "Germany"
                    },
                    {
                        "Schwiiz", "Switzerland"
                    },
                    {
                        "UK", "United Kingdom"
                    },
                    {
                        "USA", "United States of America"
                    },
                    {
                        "中国", "PR China"
                    }
                };

                public override IRow Process(IRow input, IUpdatableRow output)
                {

                    string UserID = input.Get<string>("UserID");
                    string Name = input.Get<string>("Name");
                    string Address = input.Get<string>("Address");
                    string City = input.Get<string>("City");
                    string State = input.Get<string>("State");
                    string PostalCode = input.Get<string>("PostalCode");
                    string Country = input.Get<string>("Country");
                    string Phone = input.Get<string>("Phone");

                    if (CountryTranslation.Keys.Contains(Country))
                    {
                        Country = CountryTranslation[Country];
                    }
                    output.Set<string>(0, UserID);
                    output.Set<string>(1, Name);
                    output.Set<string>(2, Address);
                    output.Set<string>(3, City);
                    output.Set<string>(4, State);
                    output.Set<string>(5, PostalCode);
                    output.Set<string>(6, Country);
                    output.Set<string>(7, Phone);

                    return output.AsReadOnly();
                }
            }
        }
6. 打开 **Script.usql**，然后粘贴以下 U-SQL 脚本：

        @drivers =
            EXTRACT UserID      string,
                    Name        string,
                    Address     string,
                    City        string,
                    State       string,
                    PostalCode  string,
                    Country     string,
                    Phone       string
            FROM "/Samples/Data/AmbulanceData/Drivers.txt"
            USING Extractors.Tsv(Encoding.Unicode);

        @drivers_CountryName =
            PROCESS @drivers
            PRODUCE UserID string,
                    Name string,
                    Address string,
                    City string,
                    State string,
                    PostalCode string,
                    Country string,
                    Phone string
            USING new USQL_UDO.CountryName();    

        OUTPUT @drivers_CountryName
            TO "/Samples/Outputs/Drivers.csv"
            USING Outputters.Csv(Encoding.Unicode);
7. 指定 Data Lake Analytics 帐户、数据库和架构。
8. 在“解决方案资源管理器”中，右键单击“Script.usql”，然后单击“生成脚本”。
9. 在“解决方案资源管理器”中，右键单击“Script.usql”，然后单击“提交脚本”。
10. 如果尚未连接到 Azure 订阅，系统将提示输入 Azure 帐户凭据。
11. 单击“提交”。 完成提交后，“结果”窗口中会出现提交结果和作业链接。
12. 单击“刷新”按钮以查看最新的作业状态和刷新屏幕。

**查看输出**

1. 在“服务器资源管理器”中依次展开 “Azure”、“Data Lake Analytics”、Data Lake Analytics 帐户、“存储帐户”，右键单击“默认存储”，然后单击“资源管理器”。
2. 展开示例、展开输出，然后双击 “Drivers.csv”。

## <a name="see-also"></a>另请参阅
* [通过 PowerShell 实现 Data Lake Analytics 入门](data-lake-analytics-get-started-powershell.md)
* [通过 Azure 门户实现 Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md)
* [使用适用于 Visual Studio 的工具开发 U-SQL 应用程序](data-lake-analytics-data-lake-tools-get-started.md)

