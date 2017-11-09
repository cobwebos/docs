---
title: "Azure 数据目录中支持的数据源 | Microsoft Docs"
description: "本文列出当前支持的数据源的规范。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: jstevens
editor: 
tags: 
ms.assetid: fd4345ca-2ed8-4c5e-9c4b-f954be2fc9f9
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 10/15/2017
ms.author: maroche
ms.openlocfilehash: 1f637acffdbf31f6c69124282f50dc5830ad9f5b
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2017
---
# <a name="supported-data-sources-in-azure-data-catalog"></a>Azure 数据目录中支持的数据源

要发布元数据，可使用公共 API、单击一次的注册工具或直接手动将信息输入到 Azure 数据目录 Web 门户。 下表汇总了目录当前支持的所有数据源以及每个数据源的发布功能。 还列出了外部数据工具（每个数据源都可从门户“open-in”体验启动它）。 第二个表包含每个数据源连接属性的详细技术规范。


## <a name="list-of-supported-data-sources"></a>受支持的数据源列表

<table>
    <tr>
       <td><b>数据源对象</b></td>
       <td><b>API</b></td>
       <td><b>手动输入</b></td>
       <td><b>注册工具</b></td>
       <td><b>Open-in 工具</b></td>
       <td><b>说明</b></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store 目录</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store 文件</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure Blob 存储</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure 存储目录</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure 存储表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>
    <tr>
      <td>HDFS 目录</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>HDFS 文件</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Hive 表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Hive 视图</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>MySQL 表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>MySQL 视图</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Oracle Database 表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Oracle Database 视图</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>其他（通用资产）</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure SQL 数据仓库表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、Power BI、SQL Server 数据工具</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL 数据仓库视图</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、Power BI、SQL Server 数据工具</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 维度</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services KPI</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 度量</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services 报表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>浏览器</font></td>
      <td><font size=2>仅本机模式服务器。不支持 SharePoint 模式。</font></td>
    </tr>
    <tr>
      <td>SQL Server 表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、Power BI、SQL Server 数据工具</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server 视图</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、Power BI、SQL Server 数据工具</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Teradata 表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Teradata 视图</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SAP HANA 视图</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>DB2 表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>DB2 视图</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>文件系统文件</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>FTP 目录</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>FTP 文件</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>HTTP 报表</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>HTTP 终结点</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>HTTP 文件</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>OData 实体集</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>OData 函数</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>PostgreSQL 表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>PostgreSQL 视图</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SAP HANA 视图</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td> Salesforce 对象</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SharePoint 列表 </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure Cosmos DB 集合</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>通用 ODBC 表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>通用 ODBC 视图</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Cassandra 表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>以通用 ODBC 资产形式发布</font></td>
    </tr>
    <tr>
      <td>Cassandra 视图</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>以通用 ODBC 资产形式发布</font></td>
    </tr>
    <tr>
      <td>Sybase 表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Sybase 视图</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>MongoDB 表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>以通用 ODBC 资产形式发布</font></td>
    </tr>
    <tr>
      <td>MongoDB 视图</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>以通用 ODBC 资产形式发布</font></td>
    </tr>
</table>

如果需要其他源的支持，请向 [Azure 数据目录论坛](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)提交功能请求。


## <a name="data-source-reference-specification"></a>数据源引用规范
> [!NOTE]
> 下表中的 **DSL 结构**列仅列出了 Azure 数据目录使用的“地址”属性包的连接属性。 也就是说，“地址”属性包可能包含 Azure 数据目录仍然保留但未使用的数据源的其他连接属性。

<table>
    <tr>
       <td><b>源类型</b></td>
       <td><b>资产类型</b></td>
       <td><b>对象类型</b></td>
       <td><b>DSL 结构<b></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>容器</td>
      <td>Data Lake</td>
      <td>
        <font size=2> 协议：webhdfs <br>身份验证：{基本、oauth} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; URL </font>
      </td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>表</td>
      <td>目录、文件</td>
      <td>
        <font size=2> 协议：webhdfs <br>身份验证：{基本、oauth} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; URL </font>
      </td>
    </tr>
    <tr>
      <td>Azure 存储</td>
      <td>容器</td>
      <td>容器</td>
      <td>
        <font size=2> 协议：azure-blob <br>身份验证：{azure-access-key} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 域 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 帐户 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 容器 </font>
      </td>
    </tr>
    <tr>
      <td>Azure 存储</td>
      <td>表</td>
      <td>Blob、目录</td>
      <td>
        <font size=2> 协议：azure-blob <br>身份验证：{azure-access-key} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 域 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 帐户 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 容器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 名称 </font>
      </td>
    </tr>
    <tr>
      <td>Azure 存储</td>
      <td>容器</td>
      <td>容器</td>
      <td>
        <font size=2> 协议：azure 表 <br>身份验证：{azure-access-key} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 域 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 帐户 </font>
      </td>
    </tr>
    <tr>
      <td>Azure 存储</td>
      <td>表</td>
      <td>表</td>
      <td>
        <font size=2> 协议：azure 表 <br>身份验证：{azure-access-key} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 域 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 帐户 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 名称 </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>容器</td>
      <td>虚拟群集</td>
      <td>
        <font size=2> 协议：cosmos <br>身份验证：{基本、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; URL </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>表</td>
      <td>流、流集、视图</td>
      <td>
        <font size=2> 协议：cosmos <br>身份验证：{基本、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; URL </font>
      </td>
    </tr>
    <tr>
      <td>Datazen</td>
      <td>容器</td>
      <td>站点</td>
      <td>
        <font size=2> 协议：http <br>身份验证：{无、基本、windows、oauth} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; URL </font>
      </td>
    </tr>
    <tr>
      <td>Datazen</td>
      <td>报表</td>
      <td>报表、仪表板</td>
      <td>
        <font size=2> 协议：http <br>身份验证：{无、基本、windows、oauth} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; URL </font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>容器</td>
      <td>数据库</td>
      <td>
        <font size=2> 协议：db2 <br>身份验证：{基本、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 </font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>表</td>
      <td>表、视图</td>
      <td>
        <font size=2> 协议：db2 <br>身份验证：{基本、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 架构 </font>
      </td>
    </tr>
    <tr>
      <td>文件系统</td>
      <td>表</td>
      <td>文件</td>
      <td>
        <font size=2> 协议：文件 <br>身份验证：{无、基本、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 路径 </font>
      </td>
    </tr>
    <tr>
      <td>FTP</td>
      <td>表</td>
      <td>目录、文件</td>
      <td>
        <font size=2> 协议：ftp <br>身份验证：{无、基本、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; URL </font>
      </td>
    </tr>
    <tr>
      <td>Hadoop 分布式文件系统</td>
      <td>容器</td>
      <td>群集</td>
      <td>
        <font size=2> 协议：webhdfs <br>身份验证：{基本、oauth} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; URL </font>
      </td>
    </tr>
    <tr>
      <td>Hadoop 分布式文件系统</td>
      <td>表</td>
      <td>目录、文件</td>
      <td>
        <font size=2> 协议：webhdfs <br>身份验证：{基本、oauth} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; URL </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>容器</td>
      <td>数据库</td>
      <td>
        <font size=2> 协议：hive <br>身份验证：{HDInsight、基本、用户名、无} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>connectionProperties： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器协议：{hive2} </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>表</td>
      <td>表、视图</td>
      <td>
        <font size=2> 协议：hive <br>身份验证：{HDInsight、基本、用户名、无} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 <br>connectionProperties： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器协议：{hive2} </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>容器</td>
      <td>站点</td>
      <td>
        <font size=2> 协议：http <br>身份验证：{无、基本、windows、oauth} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; URL </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>报表</td>
      <td>报表、仪表板</td>
      <td>
        <font size=2> 协议：http <br>身份验证：{无、基本、windows、oauth} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; URL </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>表</td>
      <td>终结点、文件</td>
      <td>
        <font size=2> 协议：http <br>身份验证：{无、基本、windows、oauth} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; URL </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>容器</td>
      <td>数据库</td>
      <td>
        <font size=2> 协议：mysql <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>表</td>
      <td>表、视图</td>
      <td>
        <font size=2> 协议：mysql <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 </font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>容器</td>
      <td>实体容器</td>
      <td>
        <font size=2> 协议：odata <br>身份验证：{无、基本、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; URL </font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>表</td>
      <td>实体集、函数</td>
      <td>
        <font size=2> 协议：odata <br>身份验证：{无、基本、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; URL <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 资源 </font>
      </td>
    </tr>
    <tr>
      <td>Oracle Database</td>
      <td>容器</td>
      <td>数据库</td>
      <td>
        <font size=2> 协议：oracle <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 </font>
      </td>
    </tr>
    <tr>
      <td>Oracle Database</td>
      <td>表</td>
      <td>表、视图</td>
      <td>
        <font size=2> 协议：oracle <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 架构 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 </font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>容器</td>
      <td>数据库</td>
      <td>
        <font size=2> 协议：postgresql <br>身份验证：{基本、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 </font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>表</td>
      <td>表、视图</td>
      <td>
        <font size=2> 协议：postgresql <br>身份验证：{基本、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 架构 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>容器</td>
      <td>站点</td>
      <td>
        <font size=2> 协议：http <br>身份验证：{无、基本、windows、oauth} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; URL </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>报表</td>
      <td>报表、仪表板</td>
      <td>
        <font size=2> 协议：http <br>身份验证：{无、基本、windows、oauth} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; URL </font>
      </td>
    </tr>
    <tr>
      <td>Power Query</td>
      <td>表</td>
      <td>数据混合</td>
      <td>
        <font size=2> 协议：power-query <br>身份验证：{oauth} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; URL </font>
      </td>
    </tr>
    <tr>
      <td>Salesforce</td>
      <td>表</td>
      <td>对象</td>
      <td>
        <font size=2> 协议：salesforce-com <br>身份验证：{基本、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 登录服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 类 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 项名称 </font>
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>容器</td>
      <td>服务器</td>
      <td>
        <font size=2> 协议：sap-hana-sql <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 </font>
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>表</td>
      <td>查看</td>
      <td>
        <font size=2> 协议：sap-hana-sql <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 架构 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 </font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>表</td>
      <td>列出</td>
      <td>
        <font size=2> 协议：sharepoint 列表 <br>身份验证：{基本、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; URL </font>
      </td>
    </tr>
    <tr>
      <td>SQL 数据仓库</td>
      <td>命令</td>
      <td>存储过程</td>
      <td>
        <font size=2> 协议：tds <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 架构 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 </font>
      </td>
    </tr>
    <tr>
      <td>SQL 数据仓库</td>
      <td>TableValuedFunction</td>
      <td>Table-valued 函数</td>
      <td>
        <font size=2> 协议：tds <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 架构 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 </font>
      </td>
    </tr>
    <tr>
      <td>SQL 数据仓库</td>
      <td>容器</td>
      <td>数据库</td>
      <td>
        <font size=2> 协议：tds <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 </font>
      </td>
    </tr>
    <tr>
      <td>SQL 数据仓库</td>
      <td>表</td>
      <td>表、视图</td>
      <td>
        <font size=2> 协议：tds <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 架构 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>命令</td>
      <td>存储过程</td>
      <td>
        <font size=2> 协议：tds <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 架构 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>Table-valued 函数</td>
      <td>
        <font size=2> 协议：tds <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 架构 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>容器</td>
      <td>数据库</td>
      <td>
        <font size=2> 协议：tds <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>表</td>
      <td>表、视图</td>
      <td>
        <font size=2> 协议：tds <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 架构 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 多维度</td>
      <td>容器</td>
      <td>模型</td>
      <td>
        <font size=2> 协议：分析服务 <br>身份验证：{windows、基本、匿名、无} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 模型 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 多维度</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> 协议：分析服务 <br>身份验证：{windows、基本、匿名、无} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 模型 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象类型：{KPI} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 多维度</td>
      <td>度量</td>
      <td>度量</td>
      <td>
        <font size=2> 协议：分析服务 <br>身份验证：{windows、基本、匿名、无} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 模型 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象类型：{度量值} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 多维度</td>
      <td>表</td>
      <td>维度</td>
      <td>
        <font size=2> 协议：分析服务 <br>身份验证：{windows、基本、匿名、无} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 模型 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象类型：{维度} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 表格</td>
      <td>容器</td>
      <td>模型</td>
      <td>
        <font size=2> 协议：分析服务 <br>身份验证：{windows、基本、匿名、无} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 模型 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 表格</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> 协议：分析服务 <br>身份验证：{windows、基本、匿名、无} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 模型 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象类型：{KPI} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 表格</td>
      <td>度量</td>
      <td>度量</td>
      <td>
        <font size=2> 协议：分析服务 <br>身份验证：{windows、基本、匿名、无} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 模型 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象类型：{度量值} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 表格</td>
      <td>表</td>
      <td>表</td>
      <td>
        <font size=2> 协议：分析服务 <br>身份验证：{windows、基本、匿名、无} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 模型 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象类型：{表} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>容器</td>
      <td>服务器</td>
      <td>
        <font size=2> 协议：报表服务 <br>身份验证：{windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 版本：{ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>报表</td>
      <td>报表</td>
      <td>
        <font size=2> 协议：报表服务 <br>身份验证：{windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 路径 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 版本：{ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>容器</td>
      <td>数据库</td>
      <td>
        <font size=2> 协议：teradata <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>表</td>
      <td>表、视图</td>
      <td>
        <font size=2> 协议：teradata <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>容器</td>
      <td>模型</td>
      <td>
        <font size="2"> 协议：mssql-mds <br>身份验证：{windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; URL <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 模型 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 版本 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>表</td>
      <td>实体</td>
      <td>
        <font size="2"> 协议：mssql-mds <br>身份验证：{windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; URL <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 模型 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 版本 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 实体 </font>
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>容器</td>
      <td>数据库</td>
      <td>
        <font size=2> 协议：document-db <br>身份验证：{azure-access-key} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; URL <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 </font>
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>集合</td>
      <td>集合</td>
      <td>
        <font size=2> 协议：document-db <br>身份验证：{azure-access-key} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; URL <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 集合 </font>
      </td>
    </tr>
    <tr>
      <td>泛型 ODBC</td>
      <td>容器</td>
      <td>数据库</td>
      <td>
        <font size=2> 协议：odbc <br>身份验证：{基本、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 选项 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 </font>
      </td>
    </tr>
    <tr>
      <td>泛型 ODBC</td>
      <td>表</td>
      <td>表、视图</td>
      <td>
        <font size=2> 协议：odbc <br>身份验证：{基本、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 选项 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 架构 </font>
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>容器</td>
      <td>数据库</td>
      <td>
        <font size=2> 协议：sybase <br>身份验证：{基本，windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 </font>
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>表</td>
      <td>表、视图</td>
      <td>
        <font size=2> 协议：sybase <br>身份验证：{基本，windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 架构 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 </font>
      </td>
    </tr>
    <tr>
      <td>其他（以上均不是）</td>
      <td>\*</td>
      <td>\*</td>
      <td>
        <font size=2> 协议：通用资产 <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 资产 ID </font>
      </td>
    </tr>
</table>
