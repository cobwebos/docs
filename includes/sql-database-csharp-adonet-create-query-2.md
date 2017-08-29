
<a name="cs_0_csharpprogramexample_h2"/>

## <a name="c-program-example"></a>C# 程序示例

本文的后续部分介绍了一个 C# 程序，该程序使用 ADO.NET 将 Transact-SQL 语句发送到 SQL 数据库。 该 C# 程序执行以下操作：

1. [使用 ADO.NET 连接到 SQL 数据库](#cs_1_connect)。
2. [创建表](#cs_2_createtables)。
3. [通过发出 T-SQL INSERT 语句为表填充数据](#cs_3_insert)。
4. [使用联接来更新数据](#cs_4_updatejoin)。
5. [使用联接来删除数据](#cs_5_deletejoin)。
6. [使用联接来选择数据行](#cs_6_selectrows)。
7. 关闭连接（这样会从 tempdb 中删除任何临时表）。

C# 程序包含：

- 连接到数据库所需的 C# 代码。
- 返回 T-SQL 源代码的方法。
- 两个将 T-SQL 提交到数据库的方法。

#### <a name="to-compile-and-run"></a>编译和运行步骤

此 C# 程序在逻辑上是一个 .cs 文件。 但这里将此程序以物理方式划分成多个代码块，使每个块更易于查看和理解。 若要编译和运行此程序，请执行以下操作：

1. 在 Visual Studio 中创建 C# 项目。
    - 项目类型应该是“控制台”应用程序，源自如下所示的层次结构：“模板” > “Visual C#” > “Windows 经典桌面” > “控制台应用(.NET Framework)”。
3. 在 Program.cs 文件中，擦除小的起始代码行。
3. 将下述每个块按此处所示顺序复制并粘贴到 Program.cs 中。
4. 在 Program.cs 的 Main 方法中编辑以下值：

   - cb.DataSource
   - cd.UserID
   - cb.Password
   - InitialCatalog

5. 验证是否已引用程序集 System.Data.dll。 若要进行验证，请在“解决方案资源管理器”窗格中展开“引用”节点。
6. 若要在 Visual Studio 中生成程序，请单击“生成”菜单。
7. 若要在 Visual Studio 中运行程序，请单击“启动”按钮。 报表输出显示在 cmd.exe 窗口中。

> [!NOTE]
> 可以选择编辑 T-SQL，以便向表名添加前导 **#**，从而在 tempdb 中以临时表的形式创建这些表。 在没有测试数据库可用时，可以通过这种方法进行演示。 关闭连接时，会自动删除临时表。 就临时表来说，不会强制外键的任何 REFERENCE。
>

<a name="cs_1_connect"/>
### <a name="c-block-1-connect-by-using-adonet"></a>C# 块 1：通过 ADO.NET 进行连接

- [下一步](#cs_2_createtables)


```csharp
using System;
using System.Data.SqlClient;   // System.Data.dll 
//using System.Data;           // For:  SqlDbType , ParameterDirection

namespace csharp_db_test
{
   class Program
   {
      static void Main(string[] args)
      {
         try
         {
            var cb = new SqlConnectionStringBuilder();
            cb.DataSource = "your_server.database.windows.net";
            cb.UserID = "your_user";
            cb.Password = "your_password";
            cb.InitialCatalog = "your_database";

            using (var connection = new SqlConnection(cb.ConnectionString))
            {
               connection.Open();

               Submit_Tsql_NonQuery(connection, "2 - Create-Tables",
                  Build_2_Tsql_CreateTables());

               Submit_Tsql_NonQuery(connection, "3 - Inserts",
                  Build_3_Tsql_Inserts());

               Submit_Tsql_NonQuery(connection, "4 - Update-Join",
                  Build_4_Tsql_UpdateJoin(),
                  "@csharpParmDepartmentName", "Accounting");

               Submit_Tsql_NonQuery(connection, "5 - Delete-Join",
                  Build_5_Tsql_DeleteJoin(),
                  "@csharpParmDepartmentName", "Legal");

               Submit_6_Tsql_SelectEmployees(connection);
            }
         }
         catch (SqlException e)
         {
            Console.WriteLine(e.ToString());
         }
         Console.WriteLine("View the report output here, then press any key to end the program...");
         Console.ReadKey();
      }
```


<a name="cs_2_createtables"/>
### <a name="c-block-2-t-sql-to-create-tables"></a>C# 块 2：用于创建表的 T-SQL

- [上一步](#cs_1_connect) &nbsp; / &nbsp; [下一步](#cs_3_insert)

```csharp
      static string Build_2_Tsql_CreateTables()
      {
         return @"
DROP TABLE IF EXISTS tabEmployee;
DROP TABLE IF EXISTS tabDepartment;  -- Drop parent table last.


CREATE TABLE tabDepartment
(
   DepartmentCode  nchar(4)          not null
      PRIMARY KEY,
   DepartmentName  nvarchar(128)     not null
);

CREATE TABLE tabEmployee
(
   EmployeeGuid    uniqueIdentifier  not null  default NewId()
      PRIMARY KEY,
   EmployeeName    nvarchar(128)     not null,
   EmployeeLevel   int               not null,
   DepartmentCode  nchar(4)              null
      REFERENCES tabDepartment (DepartmentCode)  -- (REFERENCES would be disallowed on temporary tables.)
);
";
      }
```

#### <a name="entity-relationship-diagram-erd"></a>实体关系图 (ERD)

前述 CREATE TABLE 语句涉及 REFERENCES 关键字，该关键字用于在两个表之间创建外键 (FK) 关系。  如果使用 tempdb，请通过一对前导短划线注释掉 `--REFERENCES` 关键字。

接下来是一个 ERD，显示了这两个表之间的关系。 #tabEmployee.DepartmentCode 子列中的值仅限 #tabDepartment.Department 父列中存在的值。

![ERD，显示外键](./media/sql-database-csharp-adonet-create-query-2/erd-dept-empl-fky-2.png)


<a name="cs_3_insert"/>
### <a name="c-block-3-t-sql-to-insert-data"></a>C# 块 3：用于插入数据的 T-SQL

- [上一步](#cs_2_createtables) &nbsp; / &nbsp; [下一步](#cs_4_updatejoin)


```csharp
      static string Build_3_Tsql_Inserts()
      {
         return @"
-- The company has these departments.
INSERT INTO tabDepartment
   (DepartmentCode, DepartmentName)
      VALUES
   ('acct', 'Accounting'),
   ('hres', 'Human Resources'),
   ('legl', 'Legal');

-- The company has these employees, each in one department.
INSERT INTO tabEmployee
   (EmployeeName, EmployeeLevel, DepartmentCode)
      VALUES
   ('Alison'  , 19, 'acct'),
   ('Barbara' , 17, 'hres'),
   ('Carol'   , 21, 'acct'),
   ('Deborah' , 24, 'legl'),
   ('Elle'    , 15, null);
";
      }
```


<a name="cs_4_updatejoin"/>
### <a name="c-block-4-t-sql-to-update-join"></a>C# 块 4：用于更新-联接的 T-SQL

- [上一步](#cs_3_insert) &nbsp; / &nbsp; [下一步](#cs_5_deletejoin)


```csharp
      static string Build_4_Tsql_UpdateJoin()
      {
         return @"
DECLARE @DName1  nvarchar(128) = @csharpParmDepartmentName;  --'Accounting';


-- Promote everyone in one department (see @parm...).
UPDATE empl
   SET
      empl.EmployeeLevel += 1
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName1;
";
      }
```


<a name="cs_5_deletejoin"/>
### <a name="c-block-5-t-sql-to-delete-join"></a>C# 块 5：用于删除-联接的 T-SQL

- [上一步](#cs_4_updatejoin) &nbsp; / &nbsp; [下一步](#cs_6_selectrows)


```csharp
      static string Build_5_Tsql_DeleteJoin()
      {
         return @"
DECLARE @DName2  nvarchar(128);
SET @DName2 = @csharpParmDepartmentName;  --'Legal';


-- Right size the Legal department.
DELETE empl
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName2

-- Disband the Legal department.
DELETE tabDepartment
   WHERE DepartmentName = @DName2;
";
      }
```



<a name="cs_6_selectrows"/>
### <a name="c-block-6-t-sql-to-select-rows"></a>C# 块 6：用于选择行的 T-SQL

- [上一步](#cs_5_deletejoin) &nbsp; / &nbsp; [下一步](#cs_6b_datareader)


```csharp
      static string Build_6_Tsql_SelectEmployees()
      {
         return @"
-- Look at all the final Employees.
SELECT
      empl.EmployeeGuid,
      empl.EmployeeName,
      empl.EmployeeLevel,
      empl.DepartmentCode,
      dept.DepartmentName
   FROM
      tabEmployee   as empl
      LEFT OUTER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   ORDER BY
      EmployeeName;
";
      }
```


<a name="cs_6b_datareader"/>
### <a name="c-block-6b-executereader"></a>C# 块 6b：ExecuteReader

- [上一步](#cs_6_selectrows) &nbsp; / &nbsp; [下一步](#cs_7_executenonquery)

此方法旨在运行 T-SQL SELECT 语句，该语句通过 Build_6_Tsql_SelectEmployees 方法生成。


```csharp
      static void Submit_6_Tsql_SelectEmployees(SqlConnection connection)
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("Now, SelectEmployees (6)...");

         string tsql = Build_6_Tsql_SelectEmployees();

         using (var command = new SqlCommand(tsql, connection))
         {
            using (SqlDataReader reader = command.ExecuteReader())
            {
               while (reader.Read())
               {
                  Console.WriteLine("{0} , {1} , {2} , {3} , {4}",
                     reader.GetGuid(0),
                     reader.GetString(1),
                     reader.GetInt32(2),
                     (reader.IsDBNull(3)) ? "NULL" : reader.GetString(3),
                     (reader.IsDBNull(4)) ? "NULL" : reader.GetString(4));
               }
            }
         }
      }
```


<a name="cs_7_executenonquery"/>
### <a name="c-block-7-executenonquery"></a>C# 块 7：ExecuteNonQuery

- [上一步](#cs_6b_datareader) &nbsp; / &nbsp; [下一步](#cs_8_output)

如果操作需修改表的数据内容而不返回任何数据行，则会调用此方法。


```csharp
      static void Submit_Tsql_NonQuery(
         SqlConnection connection,
         string tsqlPurpose,
         string tsqlSourceCode,
         string parameterName = null,
         string parameterValue = null
         )
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("T-SQL to {0}...", tsqlPurpose);

         using (var command = new SqlCommand(tsqlSourceCode, connection))
         {
            if (parameterName != null)
            {
               command.Parameters.AddWithValue(  // Or, use SqlParameter class.
                  parameterName,
                  parameterValue);
            }
            int rowsAffected = command.ExecuteNonQuery();
            Console.WriteLine(rowsAffected + " = rows affected.");
         }
      }
   } // EndOfClass
}
```


<a name="cs_8_output"/>
### <a name="c-block-8-actual-test-output-to-the-console"></a>C# 块 8：控制台的实际测试输出

- [上一步](#cs_7_executenonquery)

此部分捕获程序发送到控制台的输出。 多个测试运行仅 GUID 值存在差异。


```text
[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>> csharp_db_test.exe

=================================
Now, CreateTables (10)...

=================================
Now, Inserts (20)...

=================================
Now, UpdateJoin (30)...
2 rows affected, by UpdateJoin.

=================================
Now, DeleteJoin (40)...

=================================
Now, SelectEmployees (50)...
0199be49-a2ed-4e35-94b7-e936acf1cd75 , Alison , 20 , acct , Accounting
f0d3d147-64cf-4420-b9f9-76e6e0a32567 , Barbara , 17 , hres , Human Resources
cf4caede-e237-42d2-b61d-72114c7e3afa , Carol , 22 , acct , Accounting
cdde7727-bcfd-4f72-a665-87199c415f8b , Elle , 15 , NULL , NULL

[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>>
```
