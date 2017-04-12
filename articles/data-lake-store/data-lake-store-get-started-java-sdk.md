---
title: "使用 Java SDK 在 Azure Data Lake Store 中开发应用程序 | Microsoft 文档"
description: "使用 Azure Data Lake Store Java SDK 创建 Data Lake Store 帐户，在 Data Lake Store 中执行基本操作"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d10e09db-5232-4e84-bb50-52efc2c21887
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/03/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: 7e1d596739e6c548349827ff79b76cc0312bc4df
ms.lasthandoff: 04/06/2017


---
# <a name="get-started-with-azure-data-lake-store-using-java"></a>通过 Java 实现 Azure Data Lake Store 入门
> [!div class="op_single_selector"]
> * [门户](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

了解如何使用 Azure Data Lake Store Java SDK 执行基本操作，如创建文件夹、上载和下载数据文件等。有关 Data Lake 的详细信息，请参阅 [Azure Data Lake Store](data-lake-store-overview.md)。

可以在 [Azure Data Lake Store Java API 文档](https://azure.github.io/azure-data-lake-store-java/javadoc/)网页中访问适用于 Data Lake Store 的 Java SDK API 文档。

## <a name="prerequisites"></a>先决条件
* Java 开发工具包（JDK 7 或更高版本，使用 Java 1.7 或更高版本）。
* Azure Data Lake Store 帐户。 遵循[使用 Azure 门户，实现 Azure Data Lake Store 入门](data-lake-store-get-started-portal.md)中的说明。
* [Maven](https://maven.apache.org/install.html)。 本教程使用 Maven 生成项目和项目依赖项。 尽管不使用 Maven 或 Gradle 等生成系统也能完成生成，但使用这些系统可以大大减化依赖项的管理。
* （可选）[IntelliJ IDEA](https://www.jetbrains.com/idea/download/)、[Eclipse](https://www.eclipse.org/downloads/) 或类似的 IDE。

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>如何使用 Azure Active Directory 进行身份验证？
本教程使用 Azure AD 应用程序客户端机密来检索 Azure Active Directory 令牌（服务到服务的身份验证）。 我们将使用此令牌创建一个 Data Lake Store 客户端对象用于执行文件和目录操作。 以下概要步骤说明了如何使用客户端机密在 Azure Data Lake Store 中进行身份验证：

1. 创建 Azure AD Web 应用程序
2. 检索 Azure AD Web 应用程序的客户端 ID、客户端机密和令牌终结点。
3. 配置 Azure AD Web 应用程序对你要从所创建的 Java 应用程序访问的 Data Lake Store 文件/文件夹的访问权限。

有关如何执行这些步骤的说明，请参阅[创建 Active Directory 应用程序](data-lake-store-authenticate-using-active-directory.md)。

Azure Active Directory 提供其他选项，也可以检索令牌。 可以根据自己的方案从多种不同的身份验证机制中选择，例如，在浏览器中运行的应用程序、作为桌面应用程序分发的应用程序，或者在本地或 Azure 虚拟机中运行的服务器应用程序。 还可以选择不同类型的凭据，例如密码、证书、双重身份验证，等等。此外，Azure Active Directory 允许将本地 Active Directory 用户同步到云。 有关详细信息，请参阅 [Authentication Scenarios for Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md)（Azure Active directory 的身份验证方案）。 

## <a name="create-a-java-application"></a>创建 Java 应用程序
[GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) 上的代码示例逐步讲解了在存储中创建文件、连接文件、下载文件以及在存储中删除某些文件的过程。 本文的此部分演练代码的主要组成部分。

1. 在命令行或 IDE 中使用 [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) 创建一个 Maven 项目。 有关如何使用 IntelliJ 创建 Java 项目的说明，请参阅[此文](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html)。 有关如何使用 Eclipse 创建项目的说明，请参阅[此文](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm)。 
2. 将以下依赖项添加到 Maven **pom.xml** 文件。 在 **\</version>** 标记与 **\</project>** 标记之间添加以下文本片段：
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.1.4</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    第一个依赖项从 maven 存储库使用 Data Lake Store SDK (`azure-data-lake-store-sdk`)。 第二个依赖项 (`slf4j-nop`) 指定此应用程序使用的记录框架。 Data Lake Store SDK 使用 [slf4j](http://www.slf4j.org/) 日志记录体系，允许用户从多种流行记录框架（如 log4j、Java 日志记录、logback 等）中进行选择，或者不使用日志记录。 本示例将禁用日志记录，因此我们使用了 **slf4j-nop** 绑定。 若要在应用中使用其他日志记录选项，请参阅[此文](http://www.slf4j.org/manual.html#projectDep)。

### <a name="add-the-application-code"></a>添加应用程序代码
要编写三个主要组成部分的代码。

1. 获取 Azure Active Directory 令牌
2. 使用令牌创建 Data Lake Store 客户端。
3. 使用 Data Lake Store 客户端执行操作。

#### <a name="step-1-obtain-an-azure-active-directory-token"></a>步骤 1：获取 Azure Active Directory 令牌。
Data Lake Store SDK 可让用户使用便利的方法管理所需的安全令牌来与 Data Lake Store 帐户通信。 但是，该 SDK 并未规定只能使用这些方法。 可以使用其他任何方法获取令牌，例如，使用 [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java) 或自己的自定义代码。

若要使用 Data Lake Store SDK 获取以前创建的 Active Directory Web 应用程序的令牌，请使用 `AccessTokenProvider` 的子类之一（以下示例使用 `ClientCredsTokenProvider`）。 令牌提供程序将缓存用于获取内存中令牌的凭据，在令牌即将过期时自动续订令牌。 你可以创建自己的 `AccessTokenProvider` 子类以便通过客户代码获取令牌，但我们暂时只使用 SDK 中提供的子类。

将 **FILL-IN-HERE** 替换为 Azure Active Directory Web 应用程序的实际值。

    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);

#### <a name="step-2-create-an-azure-data-lake-store-client-adlstoreclient-object"></a>步骤 2：创建 Azure Data Lake Store 客户端 (ADLStoreClient) 对象
创建 [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) 对象需要指定在上一步骤中生成的 Data Lake Store 帐户名和令牌提供程序。 请注意，Data Lake Store 帐户名需是完全限定的域名。 例如，可将 **FILL-IN-HERE** 替换为类似于 **mydatalakestore.azuredatalakestore.net** 的内容。

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);

### <a name="step-3-use-the-adlstoreclient-to-perform-file-and-directory-operations"></a>步骤 3：使用 ADLStoreClient 执行文件和目录操作
以下代码包含一些常见操作的示例代码片段。 可以参阅 **ADLStoreClient** 对象的完整 [Data Lake Store Java SDK API 文档](https://azure.github.io/azure-data-lake-store-java/javadoc/)来查看其他操作的示例代码。

请注意，文件是使用标准的 Java 流读取和写入的。 这意味着，可以将位于 Data Lake Store 流顶层的任何 Java 流分层，以便利用标准 Java 功能（例如，以带有格式的输出打印流，或者在顶层压缩或加密流以获得附加功能，等等）。

     // create file and write some content
     String filename = "/a/b/c.txt";
     OutputStream stream = client.createFile(filename, IfExists.OVERWRITE  );
     PrintStream out = new PrintStream(stream);
     for (int i = 1; i <= 10; i++) {
         out.println("This is line #" + i);
         out.format("This is the same line (%d), but using formatted output. %n", i);
     }
     out.close();
    
    // set file permission
    client.setPermission(filename, "744");

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();

    // Read File
    InputStream in = client.getReadStream(filename);
    byte[] b = new byte[64000];
    while (in.read(b) != -1) {
        System.out.write(b);
    }
    in.close();

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");

#### <a name="step-4-build-and-run-the-application"></a>步骤 4：生成并运行应用程序
1. 若要从 IDE 内部运行，请找到并按下“运行”按钮。 若要从 Maven 运行，请使用 [exec:exec](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html)。
2. 若要生成一个可从命令行运行的独立 jar，请使用 [Maven 程序集插件](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html)生成包含所有依赖项的 jar。 [github 上的示例源代码](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml)中的 pom.xml 举例说明了如何执行此操作。

## <a name="next-steps"></a>后续步骤
* [探索用于 Java SDK 的 JavaDoc](https://azure.github.io/azure-data-lake-store-java/javadoc/)
* [保护 Data Lake Store 中的数据](data-lake-store-secure-data.md)
* [配合使用 Azure Data Lake Analytic 和 Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [配合使用 Azure HDInsight 和 Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)


