
给定 Function App 中所有函数的代码位于一个根文件夹中，该文件夹包含主机配置文件及一个或多个子文件夹，其中每个子文件夹又包含各个函数的代码，如以下示例中所示：

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

*host.json* 文件包含一些特定于运行时的配置，位于 Function App 的根文件夹中。 有关可用设置的信息，请参阅 WebJobs.Script 存储库 wiki 中的 [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)。

每个函数有一个对应的文件夹，其中包含一个或多个代码文件、function.json 配置和其他依赖项。



<!--HONumber=Nov16_HO4-->


