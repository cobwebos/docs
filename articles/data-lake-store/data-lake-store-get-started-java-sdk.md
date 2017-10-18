---
title: "Java SDK：Azure Data Lake Store 中的文件系统操作 | Microsoft Docs"
description: "使用 Azure Data Lake Store Java SDK 在 Data Lake Store 上执行文件系统操作，例如创建文件夹等。"
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
ms.date: 09/29/2017
ms.author: nitinme
ms.openlocfilehash: e8c7b788061b3eb18b3e6c282339a03d93ab8b1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="filesystem-operations-on-data-lake-store-using-java-sdk"></a>使用 Java SDK 在 Data Lake Store 上进行的文件系统操作
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

了解如何使用 Azure Data Lake Store Java SDK 执行基本操作，如创建文件夹、上传和下载数据文件等。有关 Data Lake 的详细信息，请参阅 [Azure Data Lake Store](data-lake-store-overview.md)。

可以在 [Azure Data Lake Store Java API 文档](https://azure.github.io/azure-data-lake-store-java/javadoc/)网页中访问适用于 Data Lake Store 的 Java SDK API 文档。

## <a name="prerequisites"></a>先决条件
* Java 开发工具包（JDK 7 或更高版本，使用 Java 1.7 或更高版本）。
* Azure Data Lake Store 帐户。 遵照[通过 Azure 门户使用 Azure Data Lake Store](data-lake-store-get-started-portal.md) 中的说明。
* [Maven](https://maven.apache.org/install.html)。 本教程使用 Maven 生成项目和项目依赖项。 尽管不使用 Maven 或 Gradle 等生成系统也能完成生成，但使用这些系统可以大大减化依赖项的管理。
* （可选）[IntelliJ IDEA](https://www.jetbrains.com/idea/download/)、[Eclipse](https://www.eclipse.org/downloads/) 或类似的 IDE。

## <a name="create-a-java-application"></a>创建 Java 应用程序
[GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) 上的代码示例逐步讲解了在存储中创建文件、连接文件、下载文件以及在存储中删除某些文件的过程。 本文的此部分演练代码的主要组成部分。

1. 在命令行或 IDE 中使用 [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) 创建一个 Maven 项目。 有关如何使用 IntelliJ 创建 Java 项目的说明，请参阅[此文](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html)。 有关如何使用 Eclipse 创建项目的说明，请参阅[此文](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm)。 

2. 将以下依赖项添加到 Maven **pom.xml** 文件。 在 **\</project>** 标记前添加以下代码片段：
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.1.5</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    第一个依赖项从 maven 存储库使用 Data Lake Store SDK (`azure-data-lake-store-sdk`)。 第二个依赖项指定此应用程序使用的日志记录框架 (`slf4j-nop`)。 Data Lake Store SDK 使用 [slf4j](http://www.slf4j.org/) 日志记录体系，允许用户从多种流行记录框架（如 log4j、Java 日志记录、logback 等）中进行选择，或者不使用日志记录。 本示例禁用日志记录，因此我们使用了 **slf4j-nop** 绑定。 若要在应用中使用其他日志记录选项，请参阅[此文](http://www.slf4j.org/manual.html#projectDep)。

3. 将以下导入语句添加到应用程序。

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

        import java.io.*;
        import java.util.Arrays;
        import java.util.List;

## <a name="authentication"></a>身份验证

* 有关应用程序的最终用户身份验证，请参阅[使用 Java 通过 Data Lake Store 进行最终用户身份验证](data-lake-store-end-user-authenticate-java-sdk.md)。
* 有关应用程序的服务到服务身份验证，请参阅[使用 Java 通过 Data Lake Store 进行服务到服务身份验证](data-lake-store-service-to-service-authenticate-java.md)。

## <a name="create-an-azure-data-lake-store-client"></a>创建 Azure Data Lake Store 客户端
创建 [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) 对象需要指定 Data Lake Store 帐户名和令牌提供程序，这是在通过 Data Lake Store 进行身份验证（参见[身份验证](#authentication)部分）时生成的。 Data Lake Store 帐户名需是完全限定的域名。 例如，可将 **FILL-IN-HERE** 替换为类似于 **mydatalakestore.azuredatalakestore.net** 的内容。

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);

以下部分的代码片段包含某些常用文件系统操作的示例。 可以参阅 **ADLStoreClient** 对象的完整 [Data Lake Store Java SDK API 文档](https://azure.github.io/azure-data-lake-store-java/javadoc/)来查看其他操作的示例代码。

## <a name="create-a-directory"></a>创建目录

以下代码片段在指定的 Data Lake Store 帐户的根目录中创建目录结构。

    // create directory
    client.createDirectory("/a/b/w");
    System.out.println("Directory created.");

## <a name="create-a-file"></a>创建文件

以下代码片段在目录结构中创建一个文件 (c.txt)，并向该文件写入一些数据。

    // create file and write some content
    String filename = "/a/b/c.txt";
    OutputStream stream = client.createFile(filename, IfExists.OVERWRITE  );
    PrintStream out = new PrintStream(stream);
    for (int i = 1; i <= 10; i++) {
        out.println("This is line #" + i);
        out.format("This is the same line (%d), but using formatted output. %n", i);
    }
    out.close();
    System.out.println("File created.");

也可使用字节数组创建一个文件 (d.txt)。

    // create file using byte arrays
    stream = client.createFile("/a/b/d.txt", IfExists.OVERWRITE);
    byte[] buf = getSampleContent();
    stream.write(buf);
    stream.close();
    System.out.println("File created using byte array.");

有关前述代码片段中使用的 `getSampleContent` 函数的定义，请参阅 [GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) 上的示例。 

## <a name="append-to-a-file"></a>附加到文件

以下代码片段将内容追加到现有文件。

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();
    System.out.println("File appended.");

有关前述代码片段中使用的 `getSampleContent` 函数的定义，请参阅 [GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) 上的示例。

## <a name="read-a-file"></a>读取文件

以下代码片段从 Data Lake Store 帐户中的文件读取内容。

    // Read File
    InputStream in = client.getReadStream(filename);
    BufferedReader reader = new BufferedReader(new InputStreamReader(in));
    String line;
    while ( (line = reader.readLine()) != null) {
        System.out.println(line);
    }
    reader.close();
    System.out.println();
    System.out.println("File contents read.");

## <a name="concatenate-files"></a>连接文件

以下代码片段将 Data Lake Store 帐户中的两个文件连接到一起。 如果成功，连接的文件会替换两个现有的文件。

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);
    System.out.println("Two files concatenated into a new file.");

## <a name="rename-a-file"></a>重命名文件

以下代码片段重命名 Data Lake Store 帐户中的一个文件。

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");
    System.out.println("New file renamed.");

## <a name="get-metadata-for-a-file"></a>获取文件的元数据

以下代码片段检索 Data Lake Store 帐户中某个文件的元数据。

    // get file metadata
    DirectoryEntry ent = client.getDirectoryEntry(filename);
    printDirectoryInfo(ent);
    System.out.println("File metadata retrieved.");

## <a name="set-permissions-on-a-file"></a>设置文件权限

以下代码片段设置在前一部分创建的文件的权限。

    // set file permission
    client.setPermission(filename, "744");
    System.out.println("File permission set.");

## <a name="list-directory-contents"></a>列出目录内容

以下代码片段以递归方式列出目录的内容。

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }
    System.out.println("Directory contents listed.");

有关前述代码片段中使用的 `printDirectoryInfo` 函数的定义，请参阅 [GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) 上的示例。

## <a name="delete-files-and-folders"></a>删除文件和文件夹

以下代码片段以递归方式删除 Data Lake Store 帐户中的指定文件和文件夹。

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");
    System.out.println("All files and folders deleted recursively");
    promptEnterKey();

## <a name="build-and-run-the-application"></a>构建并运行应用程序
1. 若要从 IDE 内部运行，请找到并按下“运行”按钮。 若要从 Maven 运行，请使用 [exec:exec](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html)。
2. 若要生成一个可从命令行运行的独立 jar，请使用 [Maven 程序集插件](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html)生成包含所有依赖项的 jar。 [github 上的示例源代码](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml)中的 pom.xml 有一个示例。

## <a name="next-steps"></a>后续步骤
* [探索用于 Java SDK 的 JavaDoc](https://azure.github.io/azure-data-lake-store-java/javadoc/)
* [保护 Data Lake Store 中的数据](data-lake-store-secure-data.md)


