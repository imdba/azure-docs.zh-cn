---
title: "Azure Cosmos DB 常见问题解答 | Microsoft Docs"
description: "获取与 Azure Cosmos DB（一种全球分布式多模型数据库服务）相关的常见问题答案。 了解容量、性能级别和缩放。"
keywords: "数据库问题, 常见问题, documentdb, azure, Microsoft azure"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b68d1831-35f9-443d-a0ac-dad0c89f245b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: f7949fe4acbc4efa394bf8bfb34e912a6c861ba3
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="azure-cosmos-db-faq"></a>Azure Cosmos DB 常见问题解答
## <a name="azure-cosmos-db-fundamentals"></a>Azure Cosmos DB 基础知识
### <a name="what-is-azure-cosmos-db"></a>什么是 Azure Cosmos DB？
Azure Cosmos DB 是一种全局复制的多模型数据库服务，可针对无架构数据提供丰富的查询，帮助提供可靠的可配置性能，并支持快速开发。 这一切都通过一个托管平台来实现，而该平台有 Microsoft Azure 强大的功能与影响力作为后盾。 

如果 Web、移动、游戏和 IoT 应用程序的关键要求是可预测的吞吐量、高可用性、低延迟和无架构数据模型，那么，Azure Cosmos DB 无疑是合适的解决方案。 它提供架构灵活性和丰富索引，并且对集成的 JavaScript 提供多文档事务性支持。 

有关部署和使用此服务的更多数据库问题、解答及说明，请参阅 [Azure Cosmos DB 文档页面]((https://docs.microsoft.com/azure/cosmos-db/)。

### <a name="what-happened-to-documentdb"></a>DocumentDB 发生了什么情况？
DocumentDB API 是 Azure Cosmos DB 所支持的其中一个 API 和数据模型。 此外，Azure Cosmos DB 还支持图形 API（预览版）、表 API 和 MongoDB API。 有关详细信息，请参阅 [DocumentDB 客户提出的问题](#moving-to-cosmos-db)。

### <a name="how-do-i-get-to-my-documentdb-account-in-the-azure-portal"></a>如何在 Azure 门户中登录到 DocumentDB 帐户？
在 Azure 门户中，单击左侧窗格中的“Azure Cosmos DB”图标。 如果之前已有 DocumentDB 帐户，现在则拥有 Azure Cosmos DB 帐户，并且不会更改计费。

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Azure Cosmos DB 的典型用例有哪些？
对于侧重于以下要求的新 Web、移动、游戏和 IoT 应用程序而言，Azure Cosmos DB 是一个不错的选择：自动缩放、可预测的性能、毫秒响应时间的快速排序，以及查询无架构数据的能力。 Azure Cosmos DB 有助于快速开发，且支持应用程序数据模型的连续迭代。 管理用户生成的内容和数据的应用程序就是 [Azure Cosmos DB 的常见用例](use-cases.md)。 

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Azure Cosmos DB 如何提供可预测的性能？
[请求单位](request-units.md) (RU) 是 Azure Cosmos DB 中吞吐量的衡量单位。 1 RU 吞吐量相当于获取 1 KB 文档的吞吐量。 在 Azure Cosmos DB 中进行的每个操作（包括读取、写入、SQL 查询和存储过程执行）都具有一个确定性的 RU 值，该值基于完成该操作所需的吞吐量。 无需考虑 CPU、IO 和内存，以及它们会怎样影响应用程序吞吐量，而是从单个 RU 度量值的角度进行考虑。

可以为每个 Azure Cosmos DB 容器保留以每秒 RU 表示的预配吞吐量。 对于任何规模的应用程序，都可以将单个请求设为基准以测量其 RU 值，并预配容器以处理所有请求的请求单位总和。 也可以根据应用程序的发展需求，相应地增加或减少容器的吞吐量。 如需请求单位的详细信息以及帮助确定容器需求，请参阅[估计吞吐量需求](request-units.md#estimating-throughput-needs)并尝试使用[吞吐量计算器](https://www.documentdb.com/capacityplanner)。 此处的*容器*一词是指 DocumentDB API 的集合、图形 API 的图、MongoDB API 的集合以及表 API 的表。 

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Azure Cosmos DB 如何支持各种数据模型（例如键/值、纵栏表、文档和图形）？

键/值（表）、纵栏表、文档和图形数据模型都是原本就支持的，因为 Azure Cosmos DB 是基于 ARS（原子、记录和序列）设计构建的。 原子、记录和序列可以轻松映射和投影到各种数据模型。 现在提供了适用于一部分模型（DocumentDB、MongoDB、表和 Graph API）的 API，将来会提供特定于其他数据模型的其他 API。

Azure Cosmos DB 有一个不受架构影响的索引编制引擎，能够自动为它引入的所有数据编制索引，不会要求开发者提供任何架构或辅助索引。 该引擎依赖于一组逻辑索引布局（倒置、纵栏表、树形），这些布局将存储布局从索引和查询处理子系统中分离出来。 Cosmos DB 还能够以可扩展方式支持一组连网协议和 API 并将它们高效地转换为核心数据模型 (1) 和逻辑索引布局 (2)，这使得它具有原本就支持多个数据模型的独特功能。

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Azure Cosmos DB 是否符合 HIPAA？
是，Azure Cosmos DB 符合 HIPAA。 HIPAA 针对可识别个人身份的健康信息的使用、泄露与保护制定了要求。 有关详细信息，请参阅 [Microsoft Trust Center](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA)（Microsoft 信任中心）。

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Azure Cosmos DB 的存储限制有哪些？
对于容器可以存储在 Azure Cosmos DB 中的数据总量并没有任何限制。

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Azure Cosmos DB 的吞吐量限制有哪些？
对于 Azure Cosmos DB 中容器可以支持的吞吐量总量并没有任何限制。 关键是要将工作负荷大致均匀地分布于数量足够多的分区键上。

### <a name="how-much-does-azure-cosmos-db-cost"></a>Azure Cosmos DB 的费用是多少？
有关详细信息，请参阅 [Azure Cosmos DB 定价详细信息](https://azure.microsoft.com/pricing/details/cosmos-db/)页面。 Azure Cosmos DB 使用费取决于预配的容器数目、容器的联机小时数，以及每个容器的预配吞吐量。 此处的*容器*一词是指 DocumentDB API 的集合、图形 API 的图、MongoDB API 的集合以及表 API 的表。 

### <a name="is-a-free-account-available"></a>有免费的帐户吗？
是的，可免费注册一个限时的帐户，且无需任何承诺费。 若要注册，请访问[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 或参阅 [Azure Cosmos DB 试用常见问题解答](#try-cosmos-db)了解详细信息。

如果不熟悉 Azure，可以注册 [Azure 免费帐户](https://azure.microsoft.com/free/)，这样可以得到 30 天试用期和信用额度，让你试用所有 Azure 服务。 如果你有 Visual Studio 订阅，则还有资格[免费获取 Azure 信用额度](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)，可用于任何 Azure 服务。 

也可以使用 [Azure Cosmos DB 模拟器](local-emulator.md)在本地免费开发和测试应用程序，而无需创建 Azure 订阅。 如果对应用程序在 Azure Cosmos DB 模拟器中的工作情况感到满意，则可以切换到在云中使用 Azure Cosmos DB 帐户。

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>如何获取与 Azure Cosmos DB 相关的其他帮助？
如需任何帮助，请在 [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb) 或 [MSDN 论坛](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)上联系我们，或者通过向 [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) 发送邮件安排与 Azure Cosmos DB 工程团队进行一对一交谈。 

<a id="try-cosmos-db"></a>
## <a name="try-azure-cosmos-db-subscriptions"></a>试用 Azure Cosmos DB 订阅

现在无需订阅和承诺，即可限时免费体验 Azure Cosmos DB。 若要注册试用 Azure Cosmos DB 订阅，请转到[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)。 此订阅独立于 [Azure 免费试用版](https://azure.microsoft.com/free/)，可在使用 Azure 免费试用版或 Azure 付费订阅的同时进行使用。 

“试用 Azure Cosmos DB”订阅显示在 Azure 门户中与用户 ID 关联的其他订阅旁。 

以下条件适用于“试用 Azure Cosmos DB”订阅：

* 对于 SQL (DocumentDB API)、Gremlin（图形 API）和表格帐户，每个订阅一个容器。
* 对于 MongoDB 帐户，每个订阅最多 3 个集合。
* 10 GB 存储容量。
* 全球复制面向后列 [Azure 区域](https://azure.microsoft.com/regions/)提供：美国中部、北欧和东南亚
* 最大吞吐量为 5K RU/s。
* 订阅在 24 小时后到期，有效期最多延长 48 小时。
* 不能为“试用 Azure Cosmos DB”帐户创建 Azure 支持票据；但会为拥有现有支持计划的订阅者提供支持。 

## <a name="set-up-azure-cosmos-db"></a>设置 Azure Cosmos DB
### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>如何注册 Azure Cosmos DB？
Azure 门户中提供了 Azure Cosmos DB。 首先注册 Azure 订阅。 注册成功后，便可将 DocumentDB API、图形 API（预览版）、表 API 或 MongoDB API 帐户添加到 Azure 订阅。

### <a name="what-is-a-master-key"></a>什么是主密钥？
主密钥是用于访问帐户的所有资源的安全令牌。 拥有此密钥的人对数据库帐户中的所有资源具有读取和写入访问权。 分发主密钥时要格外小心。 [Azure 门户][azure-portal]的“密钥”边栏选项卡中提供了主要主密钥和辅助主密钥。 有关密钥的详细信息，请参阅[查看、复制和重新生成访问密钥](manage-account.md#keys)。

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>可以将 PreferredLocations 设置为哪些区域？ 
可以将 PreferredLocations 值设置为推出了 Cosmos DB 的任意 Azure 区域。 有关可用区域的列表，请参阅 [Azure 区域](https://azure.microsoft.com/regions/)。

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>通过 Azure 数据中心在全球范围内分发数据时是否需要注意什么？ 
如 [Azure 区域](https://azure.microsoft.com/regions/)页面中指定的那样，Azure Cosmos DB 遍布于所有 Azure 区域。 由于它属于核心服务，因此每个新的数据中心都有 Azure Cosmos DB。 

设置区域时，请记住，Azure Cosmos DB 遵从 主权和政府云的要求。 也就是说，如果帐户是在主权区域创建的，则不能在该主权区域外进行复制。 同样，也无法通过外部帐户启用到其他主权位置的复制。 

## <a name="develop-against-the-documentdb-api"></a>针对 DocumentDB API 进行开发

### <a name="how-do-i-start-developing-against-the-documentdb-api"></a>如何开始针对 DocumentDB API 进行开发？
[Azure 门户][azure-portal]中提供了 Microsoft DocumentDB API。 首先必须注册 Azure 订阅。 注册 Azure 订阅后，可将 DocumentDB API 容器添加到 Azure 订阅。 有关添加 Azure Cosmos DB 帐户的说明，请参阅[创建 Azure Cosmos DB 数据库帐户](create-documentdb-dotnet.md#create-account)。 如果之前已有 DocumentDB 帐户，现在则拥有 Azure Cosmos DB 帐户。 

[SDK](documentdb-sdk-dotnet.md) 适用于 .NET、Python、Node.js、JavaScript 和 Java。 开发人员也可以使用 [RESTful HTTP API](/rest/api/documentdb/)，从各种平台使用各种语言与 Azure Cosmos DB 资源进行交互。

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>是否可以访问一些现成的示例来帮助自己入门？
GitHub 上提供了 DocumentDB API [.NET](documentdb-dotnet-samples.md)、[Java](https://github.com/Azure/azure-documentdb-java)、[Node.js](documentdb-nodejs-samples.md) 和 [Python](documentdb-python-samples.md) SDK 的示例。


### <a name="does-the-documentdb-api-database-support-schema-free-data"></a>DocumentDB API 数据库是否支持无架构数据？
是，DocumentDB API 允许应用程序存储任意的 JSON 文档，而不需要架构定义或提示。 通过 Azure Cosmos DB SQL 查询界面便可立即查询数据。  

### <a name="does-the-documentdb-api-support-acid-transactions"></a>DocumentDB API 是否支持 ACID 事务？
是，DocumentDB API 支持以 JavaScript 存储过程和触发器形式表示的跨文档事务。 事务以每个集合内的单个分区为范围，且以 ACID 语义执行，也就是“全有或全无”，与其他并发执行的代码和用户请求隔离。 如果在服务器端执行 JavaScript 应用程序代码的过程中引发了异常，则会回滚整个事务。 有关事务的详细信息，请参阅[数据库程序事务](programming.md#database-program-transactions)。

### <a name="what-is-a-collection"></a>什么是集合？
集合是文档和其关联的 JavaScript 应用程序逻辑的组。 集合是一个计费实体，其[成本](performance-levels.md)由吞吐量和已用存储确定。 集合可以跨一个或多个分区或服务器，并且能缩放以处理几乎无限制增长的存储或吞吐量。

集合也是 Azure Cosmos DB 的计费实体。 每个集合根据预配的吞吐量和已用的存储空间按小时计费。 有关详细信息，请参阅 [Azure Cosmos DB 定价](https://azure.microsoft.com/pricing/details/cosmos-db/)。 

### <a name="how-do-i-create-a-database"></a>我如何创建数据库？
可以按照[添加集合](create-documentdb-dotnet.md#create-collection)中所述使用 [Azure 门户](https://portal.azure.com)，使用某个 [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) 或使用 [REST API](/rest/api/documentdb/) 来创建数据库。 

### <a name="how-do-i-set-up-users-and-permissions"></a>我如何设置用户和权限？
可以使用某个 [Cosmos DB API SDK](documentdb-sdk-dotnet.md) 或 [REST API](/rest/api/documentdb/) 来创建用户和权限。  

### <a name="does-the-documentdb-api-support-sql"></a>DocumentDB API 是否支持 SQL？
SQL 查询语言是 SQL 所支持的查询功能的增强子集。 Azure Cosmos DB SQL 查询语言通过基于 JavaScript 的用户定义函数 (UDF)，提供丰富的分层和关系运算符以及扩展性。 JSON 语法允许将 JSON 文档模型化为具有标签化节点的树状，由 Azure Cosmos DB 自动索引技术及 Azure Cosmos DB 的 SQL 查询方言使用。 有关使用 SQL 语法的信息，请参阅[查询 DocumentDB][query] 一文。

### <a name="does-the-documentdb-api-support-sql-aggregation-functions"></a>DocumentDB API 是否支持 SQL 聚合函数？
DocumentDB API 支持通过聚合函数 `COUNT`、`MIN`、`MAX`、`AVG` 和 `SUM` 使用 SQL 语法实现的任何规模的低延迟聚合。 有关详细信息，请参阅[聚合函数](documentdb-sql-query.md#Aggregates)。

### <a name="how-does-the-documentdb-api-provide-concurrency"></a>DocumentDB API 如何提供并发？
DocumentDB API 通过 HTTP 实体标记或 ETag 支持乐观并发控制 (OCC)。 每个 DocumentDB API 资源都有一个 ETag，并且会在每次更新文档时，在服务器上设置此 ETag。 ETag 标头和当前值会包含在所有响应消息中。 ETag 可与 If-Match 标头配合使用，让服务器能够决定是否应该更新资源。 If-Match 值是用作检查依据的 ETag 值。 如果 ETag 值与服务器的 ETag 值匹配，就会更新资源。 如果 ETag 不再是最新状态，则服务器会拒绝该操作，并提供“HTTP 412 前置条件失败”响应代码。 客户端接着必须重新提取资源，以获取该资源当前的 ETag 值。 此外，ETag 可以与 If-None-Match 标头配合使用，以确定是否需要重新提取资源。

若要在 .NET 中使用乐观并发，可使用 [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) 类。 相关 .NET 示例请参阅 GitHub 上 DocumentManagement 示例中的 [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs)。

### <a name="how-do-i-perform-transactions-in-the-documentdb-api"></a>如何在 DocumentDB API 中执行事务？
DocumentDB API 通过 JavaScript 存储过程和触发器支持语言集成式事务。 脚本内的所有数据库操作均在快照隔离下执行。 如果是单分区集合，则在集合范围内执行。 如果集合已分区，则在集合内具有相同分区键值的文档下执行。 文档版本 (ETag) 的快照是在事务开始时获取的，且只有当脚本成功运行时才会提交。 如果 JavaScript 引发错误，则会回滚事务。 有关详细信息，请参阅 [Azure Cosmos DB 的服务器端 JavaScript 编程](programming.md)。

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>如何将文档批量插入到 Document DB 中？
可以通过以下两种方法之一，将文档批量插入到 Azure Cosmos DB 中：

* 数据集成工具，如 [Azure Cosmos DB 的数据迁移工具](import-data.md)中所述。
* 存储过程，如 [Azure Cosmos DB 的服务器端 JavaScript 编程](programming.md)中所述。

### <a name="does-the-documentdb-api-support-resource-link-caching"></a>DocumentDB API 是否支持资源链接缓存？
是，因为 Azure Cosmos DB 是 RESTful 服务，而资源链接固定不变，所以可以缓存。 DocumentDB API 客户端可以指定“If-None-Match”标头来读取任何资源，例如文档或集合，然后在服务器版本更改后更新其本地副本。

### <a name="is-a-local-instance-of-documentdb-api-available"></a>DocumentDB API 的本地实例是否可用？
是的。 [Azure Cosmos DB 模拟器](local-emulator.md)提供对 Cosmos DB 服务的高保真模拟。 它支持与 Azure Cosmos DB 相同的功能，包括对创建和查询 JSON 文档、预配和缩放集合，以及执行存储过程和触发器的支持。 可以使用 Azure Cosmos DB 模拟器开发和测试应用程序，然后对 Azure Cosmos DB 的连接终结点进行单一配置更改，可以将这些应用程序部署到全局范围的 Azure。

## <a name="develop-against-the-api-for-mongodb"></a>针对 API for MongoDB 进行开发
### <a name="what-is-the-azure-cosmos-db-api-for-mongodb"></a>什么是 Azure Cosmos DB API for MongoDB？
Azure Cosmos DB API for MongoDB 是一个兼容层，使应用程序可以使用社区支持的现有 Apache MongoDB API 和驱动程序轻松透明地与本机 Azure Cosmos DB 数据库引擎进行通信。 现在，开发人员可以使用现有 MongoDB 工具链和技能构建利用 Azure Cosmos DB 的应用程序。 开发人员将受益于 Azure Cosmos DB 的独有功能，包括自动索引、备份维护、得到资金支持的服务级别协议 (SLA)，等等。

### <a name="how-do-i-connect-to-my-api-for-mongodb-database"></a>如何连接到 API for MongoDB 数据库？
连接到 Azure Cosmos DB API for MongoDB 的最快捷方法是前往 [Azure 门户](https://portal.azure.com)。 转到你的帐户，然后在左侧导航菜单上单击“快速启动”。 “快速启动”是获取代码片段以连接到数据库的最佳方式。 

Azure Cosmos DB 强制实施严格的安全要求和标准。 Azure Cosmos DB 帐户需要通过 SSL 进行身份验证和安全通信，因此请确保使用 TLSv1.2。

有关详细信息，请参阅[连接到 API for MongoDB 数据库](connect-mongodb-account.md)。

### <a name="are-there-additional-error-codes-for-an-api-for-mongodb-database"></a>MongoDB 的 API 数据库是否有其他错误代码？
除了常见的 MongoDB 错误代码外，MongoDB API 还具有自己的特定错误代码：


| 错误               | 代码  | 说明  | 解决方案  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | 使用的请求单位总数超过了集合的预配请求单位率，已达到限制。 | 请考虑从 Azure 门户缩放集合的吞吐量或重试。 |
| ExceededMemoryLimit | 16501 | 作为一种多租户服务，操作已超出客户端的内存配额。 | 通过限制性更强的查询条件缩小操作的作用域，或者通过 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)联系支持人员。 <br><br>示例：*&nbsp;&nbsp;&nbsp;&nbsp;db.getCollection('users').aggregate([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {name: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {age: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])*) |

## <a name="develop-with-the-table-api"></a>使用表 API 进行开发

### <a name="how-can-i-use-the-table-api-offering"></a>如何使用表 API 产品/服务？ 
[Azure 门户][azure-portal]中提供了 Azure Cosmos DB 表 API。 首先必须注册 Azure 订阅。 注册成功后，可将 Azure Cosmos DB 表 API 帐户添加到 Azure 订阅，然后向帐户添加表。 

可以在 [Azure Cosmos DB 表 API 简介](table-introduction.md)中找到支持的语言和相关的快速入门。

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>是否需要新的 SDK 才能使用表 API？ 
不是，现有的存储 SDK 仍然适用。 但是，我们建议始终使用最新的 SDK，以获得最佳支持，并在许多场合下获得优异的性能。 请参阅 [Azure Cosmos DB 表 API 简介](table-introduction.md)中的可用语言列表。

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>表 API 与 Azure 表存储的行为有哪些不同之处？
想要使用 Azure Cosmos DB 表 API 创建表的 Azure 表存储用户应注意以下这些行为差异：

* Azure Cosmos DB 表 API 使用保留容量模型来保障性能，但这意味着，一旦创建了表，就必须立即支付容量费用，即使容量未被使用。 使用 Azure 表存储时，只需为实际使用的容量付费。 这也说明了，表 API 在 99% 的时间里为何能够提供 10 毫秒的读取延迟和 15 毫秒的写入延迟 SLA，而 Azure 表存储提供 10 秒延迟 SLA。 因此，使用表 API 表（即使是不带任何请求的空表）时，要达到 Azure Cosmos DB 所提供的 SLA，必须支付费用来确保提供所需的容量来处理对这些表发出的所有请求。
* 表 API 返回的查询结果未按分区键/行键顺序排序，因为它们在 Azure 表存储中。
* 行键最多只能包含 255 个字节
* CreateIfNotExists 调用受管理限制的约束，该限制是固定的，不同于 RU 涵盖的其他表操作。 这意味着，发出大量 CreateIfNotExists 调用的对象会受到限制，无法执行相关的任何操作，因为限制不是来自其 RU。
* 目前不支持 CORS
* Azure 表存储中的表名不区分大小写，但出现在 Azure Cosmos DB 表 API 中

对于 REST API，有大量的终结点/查询选项不受 Azure Cosmos DB 表 API 的支持：
| REST 方法 | REST 终结点/查询选项 | 文档 URL | 说明 |
| ------------| ------------- | ---------- | ----------- |
| GET、PUT | /?restype=service@comp=properties| [设置表服务属性](https://docs.microsoft.com/en-us/rest/api/storageservices/set-table-service-properties)和[获取表服务属性](https://docs.microsoft.com/en-us/rest/api/storageservices/get-table-service-properties) | 此终结点用于设置 CORS 规则、存储分析配置和日志记录设置。 CORS 目前不受支持，Azure Cosmos DB 与 Azure 存储表中以不同的方式处理分析和日志记录 |
| OPTIONS | /<table-resource-name> | [预检 CORS 表请求](https://docs.microsoft.com/en-us/rest/api/storageservices/preflight-table-request) | 这是 Azure Cosmos DB 目前不支持的 CORS 部分。 |
| GET | /?restype=service@comp=stats | [获取表服务统计信息](https://docs.microsoft.com/en-us/rest/api/storageservices/get-table-service-stats) | 提供有关主节点与辅助节点之间的数据复制速度的信息。 由于复制是写入的一部分，因此在 Cosmos DB 中不需要此选项。 |
| GET、PUT | /mytable?comp=acl | [获取表 ACL](https://docs.microsoft.com/en-us/rest/api/storageservices/get-table-acl) 和[设置表 ACL](https://docs.microsoft.com/en-us/rest/api/storageservices/set-table-acl) | 获取和设置用于管理共享访问签名 (SAS) 的存储访问策略。 尽管支持 SAS，但其设置和管理方式不同。 |

此外，Azure Cosmos DB 表 API 仅支持 JSON 格式，而不支持 ATOM。

尽管 Azure Cosmos DB 支持共享访问签名 (SAS)，但它不支持某些策略，具体而言，是与管理操作相关的策略，例如创建新表的权限。

对于特定的 .NET SDK，Azure Cosmos DB 目前不支持某些类和方法。

| 类 | 不支持的方法 |
|-------|-------- |
| CloudTableClient | \*ServiceProperties* |
|                  | \*ServiceStats* |
| CloudTable | SetPermissions* |
|            | GetPermissions* |
| TableServiceContext | *（实际上此类已弃用） |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

如果其中的任何差异会给项目造成问题，请通过 [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) 联系并告诉我们。

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>如何提供有关 SDK 的反馈或 Bug？
可以通过以下任一方式分享反馈：

* [Uservoice](https://feedback.azure.com/forums/599062-azure-cosmos-db-table-api)
* [MSDN 论坛](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)
* [堆栈溢出](http://stackoverflow.com/questions/tagged/azure-cosmosdb)

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>连接到表 API 需要使用哪个连接字符串？
连接字符串为：
```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountNameFromDocumentDB>.table.cosmosdb.azure.com
```
可以通过 Azure 门户中的“连接字符串”页获取连接字符串。 

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>如何在表 API 的 .NET SDK 中替代请求选项的配置设置？
有关配置设置的信息，请参阅 [Azure Cosmos DB 功能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)。 有些设置是通过 CreateCloudTableClient 方法处理的，还有一些设置是通过客户端应用程序中 appSettings 节的 app.config 处理的。

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>使用现有 Azure 表存储 SDK 的客户是否需要进行任何更改？
无。 使用现有 Azure 表存储 SDK 的现有客户或新客户无需进行任何更改。 

### <a name="how-do-i-view-table-data-that-is-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>如何查看 Azure Cosmos DB 中存储的用于表 API 的表数据？ 
可以使用 Azure 门户浏览此数据。 也可以使用表 API 代码或下一个问题答案中提到的工具。 

### <a name="which-tools-work-with-the-table-api"></a>哪些工具适用于表 API？ 
可以使用 [Azure 存储资源管理器](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer)。

灵活地采用之前指定格式的连接字符串的工具可以支持新的表 API。 [Azure 存储客户端工具](../storage/common/storage-explorers.md)页上提供了表工具的列表。 

### <a name="do-powershell-or-azure-cli-work-with-the-table-api"></a>PowerShell 或 Azure CLI 是否适用于表 API？
支持 [PowerShell](table-powershell.md)。 目前不支持 Azure CLI。

### <a name="is-the-concurrency-on-operations-controlled"></a>是否能控制并发操作？
是，乐观并发是通过使用 ETag 机制提供的。 

### <a name="is-the-odata-query-model-supported-for-entities"></a>实体是否支持 OData 查询模型？ 
是，表 API 支持 OData 查询和 LINQ 查询。 

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>是否可以在同一应用程序中同时连接到 Azure 表存储和 Azure Cosmos DB 表 API？ 
是，可以创建 CloudTableClient 的两个不同实例并使其通过连接字符串指向其自身的 URI 来实现此目的。

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>如何将现有 Azure 表存储应用程序迁移到此服务？
支持使用 [AzCopy](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy) 和 [Azure Cosmos DB 数据迁移工具](import-data.md)。

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>如何为此服务扩展存储大小，比如，最初我有 *n* GB 的数据，但一段时间后我的数据会增长到 1 TB？ 
Azure Cosmos DB 旨在通过横向缩放提供无限的存储。 该服务可以监视并有效地增加存储。 

### <a name="how-do-i-monitor-the-table-api-offering"></a>如何监视表 API 服务？
可以使用表 API 的“指标”窗格来监视请求和存储使用情况。 

### <a name="how-do-i-calculate-the-throughput-i-require"></a>如何计算所需的吞吐量？
可以使用容量估算器计算操作所需的 TableThroughput。 有关详细信息，请参阅[估算请求单位和数据存储](https://www.documentdb.com/capacityplanner)。 通常，可以将实体表示为 JSON 并且为操作提供所需数量。 

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>是否可以在本地将表 API SDK 用于模拟器？
现在不行。

### <a name="can-my-existing-application-work-with-the-table-api"></a>现有的应用程序是否适用于表 API？ 
是的，支持相同的 API。

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-do-not-want-to-use-the-table-api-features"></a>如果我不想使用表 API 功能，是否需要将现有 Azure 表存储应用程序迁移到该 SDK？
否，可以在没有任何干扰的情况下创建和使用现有 Azure 表存储资产。 但是，如果不使用表 API，则无法从自动索引、其他一致性选项或全局分发中受益。 

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-multiple-regions-of-azure"></a>如何在跨多个 Azure 区域的表 API 中添加数据复制？
可以使用 Azure Cosmos DB 门户的[全局复制设置](tutorial-global-distribution-documentdb.md#portal)添加适合应用程序的区域。 若要开发全球分布的应用程序，还应该将应用程序的 PreferredLocation 信息设置为本地区域，以实现较低的读取延迟。 

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>如何在表 API 中更改帐户的主要写入区域？
可以使用 Azure Cosmos DB 的全局复制门户窗格添加区域，然后故障转移到所需区域。 有关说明，请参阅[使用多区域 Azure Cosmos DB 帐户进行开发](regional-failover.md)。 

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>在分发数据时，如何配置首选读取区域以实现较低的延迟？ 
可使用 app.config 文件中的 PreferredLocation 键，帮助从本地位置进行读取。 对于现有应用程序，如果设置 LocationMode，表 API 会引发错误。 请删除该代码，因为表 API 会从 app.config 文件中选取此信息。 有关详细信息，请参阅 [Azure Cosmos DB 功能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)。

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>如何理解表 API 中的一致性级别？ 
Azure Cosmos DB 在一致性、可用性和延迟之间提供合理的平衡。 Azure Cosmos DB 为表 API 开发人员提供五个一致性级别，因此可以在表级别选择合适的一致性模型，并在查询数据时发出相应的请求。 客户端建立连接后，可以指定一致性级别。 可以通过 CreateCloudTableClient 的 consistencyLevel 参数更改级别。 

如果将“有限过时”一致性设置为默认值，表 API 可通过“读取自己写入的数据”提供低延迟的读取。 有关详细信息，请参阅[一致性级别](consistency-levels.md)。 

默认情况下，Azure 表存储在区域内提供非常一致性，在辅助位置中提供最终一致性。 

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Azure Cosmos DB 表 API 是否比 Azure 表存储提供更多的一致性级别？
是，有关如何受益于 Azure Cosmos DB 的分布式特性的信息，请参阅[一致性级别](consistency-levels.md)。 由于为一致性级别提供了保障，因此可以放心使用它们。 有关详细信息，请参阅 [Azure Cosmos DB 功能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)。

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>启用全局分发后，完成数据的复制需要多久？
Azure Cosmos DB 会在本地区域持续提交数据，然后在几毫秒内将数据立即推送到其他区域。 此复制仅依赖于数据中心的往返时间 (RTT)。 若要详细了解 Azure Cosmos DB 的全局分发功能，请参阅 [Azure Cosmos DB：Azure 上的全球分布式数据库服务](distribute-data-globally.md)。

### <a name="can-the-read-request-consistency-level-be-changed"></a>是否可以更改读取请求一致性级别？
使用 Azure Cosmos DB 时，可以在容器级别（在表上）设置一致性级别。 使用 .NET SDK，可以通过在 app.config 文件中提供 TableConsistencyLevel 键值来更改级别。 可能的值包括：非常、有限过期、会话、一致前缀和最终。 有关详细信息，请参阅 [Azure Cosmos DB 中的可优化数据一致性级别](consistency-levels.md)。 关键是不能将请求的一致性级别设置为高于表的一致性级别。 例如，不能将表的一致性级别设置为“最终”，而将请求的一致性级别设置为“非常”。 

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>在某个区域出现故障时，表 API 如何处理故障转移？ 
表 API 利用 Cosmos DB 的全球分布式平台。 若要确保应用程序能够容许数据中心停机，需在 Azure Cosmos DB 门户中至少再为帐户启用一个区域（[使用多区域 Azure Cosmos DB 帐户进行开发](regional-failover.md)）。 可以通过使用门户设置区域的优先级（[使用多区域 Azure Cosmos DB 帐户进行开发](regional-failover.md)）。 

可为帐户添加任意数目的区域，并通过提供故障转移优先级控制可将帐户故障转移到的区域。 当然，还需要在帐户中提供一个应用程序，以利用数据库。 这样，客户就不会遇到停机情况。 [最新的 .NET 客户端 SDK](table-sdk-dotnet.md)可自动寻址，但其他 SDK 则不可以。 也就是说，它能够检测到有故障的区域，并自动故障转移到新区域。

### <a name="is-the-table-api-enabled-for-backups"></a>是否能够为表 API 启用备份？
可以，表 API 利用 Azure Cosmos DB 的平台进行备份。 可自动创建备份。 有关详细信息，请参阅[使用 Azure Cosmos DB 联机备份和还原](online-backup-and-restore.md)。

 
### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>表 API 是否默认对实体的所有属性编制索引？
是，默认情况下会对实体的所有属性进行索引。 有关详细信息，请参阅 [Azure Cosmos DB：索引策略](indexing-policies.md)。 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>这是否意味着无需创建多个索引即可满足查询要求？ 
是，Azure Cosmos DB 表 API 针对所有属性提供自动索引，无需任何架构定义。 此自动化功能使开发人员能够将重心放在应用程序上，而不必担心索引的创建和管理。 有关详细信息，请参阅 [Azure Cosmos DB：索引策略](indexing-policies.md)。

### <a name="can-i-change-the-indexing-policy"></a>是否可以更改索引策略？
是，可以提供索引定义来更改索引策略。 有关详细信息，请参阅 [Azure Cosmos DB 功能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)。 需要适当地将这些设置编码并转义。 

对于非 .NET SDK，只能在门户中设置索引策略，方法是：打开“数据资源管理器”，导航到想要更改的特定表，转到“缩放和设置”->“索引策略”，进行所需的更改，并单击“保存”。

对于 .NET SDK，可以在 app.config 文件中提交更改：
```
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        } 
      ]
    }
  ],
  "excludedPaths": 
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>平台形式的 Azure Cosmos DB 似乎拥有许多功能，例如排序、聚合、分层和其他功能。 是否会将这些功能添加到表 API 中？ 
表 API 提供与 Azure 表存储相同的查询功能。 Azure Cosmos DB 还支持排序、聚合、地理空间查询、层次结构和各种内置函数。 在将来的服务更新中，我们会在表 API 中提供更多的功能。 有关详细信息，请参阅 [Azure Cosmos DB DocumentDB API 的 SQL 查询](../documentdb/documentdb-sql-query.md)。
 
### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>何时应更改表 API 的 TableThroughput？
符合以下任一情况时，都应更改 TableThroughput：
* 要执行数据提取、转换和加载 (ETL) 操作，或者想在短时间内上传大量数据。 
* 需要后端容器提供更大吞吐量。 例如，发现已用吞吐量超过预配吞吐量，且吞吐量已达到限制。 有关详细信息，请参阅[为 Azure Cosmos DB 容器设置吞吐量](set-throughput.md)。

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>是否可以提高或降低表 API 表的吞吐量？ 
是，可以使用 Azure Cosmos DB 门户的缩放窗格来缩放吞吐量。 有关详细信息，请参阅[设置吞吐量](set-throughput.md)。

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>是否为新预配的表设置了默认 TableThroughput？
是，如果未通过 app.config 替代 TableThroughput，并且未使用 Azure Cosmos DB 中预创建的容器，服务则会创建吞吐量为 400 的表。
 
### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>对于 Azure 表存储服务的现有客户，定价是否有任何变化？
无。 对于现有的 Azure 表存储客户，价格上没有任何更改。 

### <a name="how-is-the-price-calculated-for-the-table-api"></a>表 API 的价格是如何计算的？ 
价格取决于分配的 TableThroughput。 

### <a name="how-do-i-handle-any-throttling-on-the-tables-in-table-api-offering"></a>如何在表 API 服务中处理对表设置的任何限制？ 
如果请求速率超出了为基础容器预配的吞吐量容量，则会出现错误，SDK 会使用重试策略重试调用。

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>为何需要选择吞吐量而不是 PartitionKey 和 RowKey 来利用 Azure Cosmos DB 的表 API 服务？
如果未在 app.config 文件中或通过门户提供吞吐量，Azure Cosmos DB 将为容器设置默认的吞吐量。 

Azure Cosmos DB 针对操作设置上限，在性能和延迟方面提供保障。 如果引擎可以针对租户的操作实施调控，则可以做到这一点。 设置 TableThroughput 可确保在吞吐量和延迟方面获得保障，因为平台会保留此容量，并保证操作成功。 

通过使用吞吐量规范，可以弹性更改吞吐量，以利用应用程序的季节性，满足吞吐量需求并节省成本。

### <a name="azure-table-storage-has-been-very-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-have-not-performed-a-single-transaction-or-stored-anything-can-you-please-explain"></a>Azure 表存储对我而言非常便宜，因为我只需支付数据的存储费用，并且我很少进行查询。 但是，即使我未执行任何事务或存储任何数据，Azure Cosmos DB 表 API 服务似乎也要收费。 这是怎么回事？

Azure Cosmos DB 设计为一个全球分布的、基于 SLA 的系统，在可用性、延迟和吞吐量方面提供保障。 在 Azure Cosmos DB 中保留吞吐量时，获得的保障与其他系统不同。 Azure Cosmos DB 还提供客户长期以来一直期盼的其他功能，例如辅助索引和全局分发。  

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>在向 Azure 表存储引入数据时，我从未收到过“配额已满”通知（指示分区已满）。 但使用表 API 时会收到此消息。 是此服务有限制，迫使我更改现有的应用程序吗？

Azure Cosmos DB 是基于 SLA 的系统，可提供无限缩放，并在延迟、吞吐量、可用性和一致性方面提供保障。 为了确保获得有保障的高级性能，需确保数据大小和索引可管理且可缩放。 我们为每个分区键的实体或项数实施 10 GB 限制，以确保能提供强大的查找和查询性能。 若要确保即使针对 Azure 存储，应用程序也能很好地进行缩放，建议*不要*创建热分区，即，将所有信息存储在一个分区内并查询它。 

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>表 API 是否仍然需要 PartitionKey 和 RowKey？ 
是的。 由于表 API 的外围应用类似于 Azure 表存储 SDK，因此使用分区键可以高效地分发数据。 行键在该分区中是唯一的。 需要存在行键且它不能为 null（在标准 SDK 中可为 null）。 RowKey 的长度为 255 个字节，PartitionKey 的长度为 1 KB。 

### <a name="what-are-the-error-messages-for-the-table-api"></a>表 API 的错误消息有哪些？
Azure 表存储和 Azure Cosmos DB 表 API 使用相同的 SDK，因此，大多数错误是相同的。

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>在表 API 中尝试一个接一个地创建许多表时，为何会受到限制？
Azure Cosmos DB 是基于 SLA 的系统，在可用性、延迟和吞吐量方面提供保障。 由于它是预配的系统，因此会保留资源来保证满足这些要求。 以较快的速率创建表会被系统检测到并受到限制。 建议查看表创建速率，将其降至每分钟不超过 5 个。 请记住，表 API 是预配的系统。 只要预配它，就必须付费。 

## <a name="develop-against-the-graph-api-preview"></a>针对图形 API（预览版）进行开发
### <a name="how-can-i-apply-the-functionality-of-graph-api-preview-to-azure-cosmos-db"></a>如何向 Azure Cosmos DB 应用图形 API（预览版）的功能？
可以使用扩展库来应用图形 API（预览版）的功能。 此库称为 Microsoft Azure Graphs，在 NuGet 中提供。 

### <a name="it-looks-like-you-support-the-gremlin-graph-traversal-language-do-you-plan-to-add-more-forms-of-query"></a>你们似乎支持 Gremlin 图形遍历语言。 是否计划添加更多查询形式？
是，我们计划将来添加其他查询机制。 

### <a name="how-can-i-use-the-new-graph-api-preview-offering"></a>如何使用新的图形 API（预览版）服务？ 
若要开始使用，请先阅读[图形 API](../cosmos-db/create-graph-dotnet.md) 快速入门文章。

<a id="cassandra"></a> 
## <a name="develop-with-the-apache-cassandra-api-preview"></a>使用 Apache Cassandra API（预览版）进行开发

### <a name="what-is-the-protocol-version-supported-in-the-preview-do-you-plan-to-support-other-protocols"></a>预览版支持哪个协议版本？ 你们是否打算支持其他协议？
Azure Cosmos DB 的 Apache Cassandra API 目前支持 CQL 版本 4。 若要提供有关其他模型支持的反馈，请使用 [Uservoice 反馈](https://feedback.azure.com/forums/263030-azure-cosmos-db)创建请求，或者向 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 发送电子邮件。 

### <a name="is-it-possible-to-create-multiple-tables-with-apache-cassandra-api-of-azure-cosmos-db"></a>是否可以使用 Azure Cosmos DB 的 Apache Cassandra API 创建多个表？
Azure Cosmos DB 是资源调控的系统，适用于数据和控制平面活动。 与集合和表一样，容器是针对特定的吞吐量容量预配的运行时实体。 快速连续创建这些容器属于非预期的活动，可能会受到限制。 如果测试活动会立即删除/创建表，请尝试隔离这些活动，或者在删除数据后重用现有的表。

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>最多可以创建几个表？
可创建的表数没有实际限制。 如果需要创建极大量的表（总稳定大小超过 10 TB），而不是像平时那样创建几十或几百个，请向 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 发送电子邮件。 

### <a name="what-is-the-maximum-number-of-keyspaces-that-can-be-created"></a>最多可以创建几个键空间？ 
可创建的键空间数没有实际限制，因为它们是元数据容器。 如果需要创建极大量的键空间，请向 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 发送电子邮件。 

### <a name="is-it-possible-to-bring-in-a-lot-of-data-after-starting-from-a-normal-table"></a>从普通的表启动后，是否可以引入大量数据？ 
存储容量是自动管理的，推入更多的数据时，存储容量将会增大。 这可以确保提供足够的吞吐量来支持这么多的数据，让我们自信地导入所需数量的数据。

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>是否可以提供 yaml 文件设置来配置 Azure Cosmos DB 的 Apache Casssandra API 行为？
Azure Cosmos DB 的 Apache Cassandra API 是一个平台服务。 它提供协议级的兼容性来执行操作。 它可以消除管理、监视和配置的复杂性。 开发人员和用户无需担心可用性、逻辑删除、键缓存、行缓存、布隆筛选器和其他许多设置。 Azure Cosmos DB Apache Cassandra API 注重于提供所需的读取和写入性能且不产生开销。

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Azure Cosmos DB 的 Apache Cassandra API 是否支持节点添加/群集状态/节点状态命令？
Apache Cassandra API 是一个平台服务，使用它可以轻松做出容量规划，以及应对吞吐量方面的弹性需求。 借助 Azure Cosmos DB，可以预配所需的吞吐量。 然后，可以在一整天中多次上调和下调吞吐量，而无需担心如何添加/删除或管理节点。 这也意味着，不需要使用节点或群集管理工具。 

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation"></a>有关创建键空间的各项配置设置有什么变化？
出于可用性和低延迟的原因，Azure Cosmos DB 提供现成的全局分发。 不需要设置副本。 在将数据写入或分发到的任何区域中，所有写入始终是持久提交的仲裁，同时可提供性能保障。 出于此原因，`Simple Strategy` 和 `Network Topology Strategy` 等配置设置将被忽略。 

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filters-caching-read-repair-changes-gcgrace-and-compression-memtableflushperiod"></a>布隆筛选器、缓存、读取修复更改、gc_grace 和 compression memtable_flush_period 等表元数据的各项设置有什么变化？
Azure Cosmos DB 为读/写操作提供性能和吞吐量，无需更改任何配置设置，也不会造成意外处理这些设置的风险。 这就消除了难以管理和维护的整套配置设置。

### <a name="what-is-the-default-consistency-of-an-apache-cassandra-api-account"></a>Apache Cassandra API 帐户的默认一致性是什么？
Azure Cosmos DB 原生支持 [Azure Cosmos DB 中的可优化数据一致性级别](consistency-levels.md)中所述的五个一致性级别。 Apache Cassandra API 目前仅支持会话一致性。 

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Cassandra 表是否支持生存时间 (TTL)？ 
是的，表支持 TTL。 

### <a name="what-is-the-default-throughput-of-a-table-when-created-through-cql-what-if-i-need-to-change-it"></a>通过 CQL 创建的表的默认吞吐量是多少？ 如何更改该默认值？
Azure Cosmos DB 使用每秒请求单位数 (RU/s) 作为所提供的吞吐量的单位。 通过 CQL 创建的表默认预配了 400 RU/s。 可以在 Azure 门户中更改表的吞吐量，最大可提高到 10,000 RU。 如果在预览期需要 10,000 RU/s 以上的吞吐量，请使用 Azure 门户创建表。 或者，如果需要对 CQL 使用此功能，请通过 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 联系我们。 

###  <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-what-should-i-monitor"></a>是否可以监视节点状态、副本状态、gc 和 OS 参数？ 应该监视哪些参数？
Azure Cosmos DB 是一个平台服务，可帮助你提高工作效率，而无需担心如何管理和监视基础结构。 需要监视的指标是吞吐量，可通过 Azure 门户中“Azure Cosmos DB 指标”页上的“吞吐量”选项卡查看。 这些图表指出了是否已受到限制，以及是要提高还是降低吞吐量。 有关详细信息，请参阅[监视 Azure Cosmos DB](monitor-accounts.md) 和[使用 Azure Cosmos DB 中的指标进行监视和调试](use-metrics.md)。

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>哪些客户端 SDK 适用于 Azure Cosmos DB 的 Apache Cassandra API？
我们已使用 C#、Java、Python 和 Node Apache Cassandra 驱动程序对 Apache Cassandra API 进行测试。 如果你使用了其他驱动程序并需要帮助，请向 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 发送电子邮件。

### <a name="how-do-i-get-ru-capacity-planning-for-the-apache-cassandra-api"></a>如何获取 Apache Cassandra API 的 RU 容量规划？
Azure Cosmos DB 提供[容量规划器](https://www.documentdb.com/capacityplanner)。 提供实体的 JSON 表示形式后，规划器即可获取时点读/写所需的 RU 数。 对于查询和其他操作，现有驱动程序会返回包含 RU 消耗信息的元数据。 可以利用该信息完成准确的规划。 

### <a name="is-composite-primary-key-supported"></a>是否支持复合主键？
是的，可以使用正则语法创建复合分区键。 

### <a name="can-i-use-sstable-loader-for-data-loading"></a>是否可以使用 sstable 加载程序加载数据？
不可以，目前不支持 sstable 加载程序。 

### <a name="can-an-on-premise-cassandra-cluster-be-paired-with-the-azure-cosmos-db-apache-cassandra-api"></a>本地 Cassandra 群集是否可与 Azure Cosmos DB Apache Cassandra API 配对？
请向 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 发送电子邮件并描述具体的情景。 本服务针对云环境提供优化的体验，且不产生操作开销。

### <a name="do-i-need-to-take-snapshots-and-incremental-backups"></a>是否需要创建快照和增量备份？ 
Azure Cosmos DB 的所有 API 每隔四小时生成两个免费的完整备份。 因此，可确保客户无需创建备份计划。 如果想要修改备份的保留期和频率，请向 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 发送电子邮件。 

### <a name="where-can-i-provide-feedback-if-a-feature-in-the-regular-cassandra-api-does-not-work"></a>如果常规 Cassandra API 中的某个功能失效，可在哪里提供反馈？
请向 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 发送电子邮件。

### <a name="where-can-i-suggest-new-features-for-the-apache-cassandra-api"></a>可在哪里提议 Apache Cassandra API 的新功能？
请向 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 发送电子邮件。或者，可以在 [Uservoice 反馈](https://feedback.azure.com/forums/263030-azure-cosmos-db)站点上提供反馈。 

### <a name="why-do-i-need-to-choose-a-throughput-level"></a>为何需要选择吞吐量级别？
Azure Cosmos DB 根据表的创建位置、门户或 CQL 设置容器的默认吞吐量。 

Azure Cosmos DB 针对操作设置上限，在性能和延迟方面提供保障。 之所以能够提供这种保障，是因为引擎可以针对租户的操作实施调控。 设置吞吐量可确保在吞吐量和延迟方面获得保障，因为平台会保留此容量，并保证操作成功。 有关详细信息，请参阅 [Azure Cosmos DB 中的请求单位](request-units.md)。

通过使用吞吐量规范，可以弹性更改吞吐量，以利用应用程序的季节性，满足吞吐量需求并节省成本。

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-regular-cassandra-with-the-azure-cosmos-db-cassandra-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>将数据引入到常规 Cassandra 时，我从未收到过“配额已满”通知（表示分区已满）。 但使用 Azure Cosmos DB Cassandra API 时，我却收到了此消息。 是此服务有限制，迫使我更改现有的应用程序吗？

Azure Cosmos DB 是基于 SLA 的系统，可提供无限缩放，并在延迟、吞吐量、可用性和一致性方面提供保障。 为了确保获得有保障的高级性能，需确保数据大小和索引可管理且可缩放。 根据分区键针对实体或项数实施 10-GB 限制的目的是确保 Azure Cosmos DB 提供强大的查找和查询性能。 若要确保即使针对 Azure 存储，应用程序也能很好地进行缩放，建议*不要*创建热分区，即，将所有信息存储在一个分区内并查询它。 

### <a name="how-does-the-cassandra-api-handle-failover-if-a-region-goes-down"></a>在某个区域出现故障时，Cassandra API 如何处理故障转移？ 
Azure Cosmos DB Cassandra API 借助 Azure Cosmos DB 的全球分布式平台。 为了确保应用程序能够容许数据中心停机，需在 Azure Cosmos DB 门户中至少再为帐户启用一个区域。 可以根据[使用多区域 Azure Cosmos DB 帐户进行开发](regional-failover.md)中所述，使用门户设置区域的优先级。 

可为帐户添加任意数目的区域，并通过提供故障转移优先级控制可将帐户故障转移到的区域。 当然，还需要在帐户中提供一个应用程序以使用数据库。 这样，客户就不会遇到停机情况。  

### <a name="is-the-apache-cassandra-api-enabled-for-backups"></a>是否可为 Apache Cassandra API 启用备份？
可以，Azure Cosmos DB Cassandra API 提供与 Azure Cosmos DB 相同的备份。 可自动创建备份。 有关详细信息，请参阅[使用 Azure Cosmos DB 联机备份和还原](online-backup-and-restore.md)。
 
### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Apache Cassandra API 是否默认对实体的所有属性编制索引？
是，默认情况下 Azure Cosmos DB 会对实体的所有属性进行索引。 有关详细信息，请参阅 [Azure Cosmos DB：索引策略](indexing-policies.md)。 这样可以获得有保障的性能，以及一致的索引和持久的仲裁提交写入。 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>这是否意味着无需创建多个索引即可满足查询要求？ 
是，Azure Cosmos DB 针对所有属性提供自动索引，无需任何架构定义。 此自动化功能使开发人员能够将重心放在应用程序上，而不必担心索引的创建和管理。 有关详细信息，请参阅 [Azure Cosmos DB：索引策略](indexing-policies.md)。

### <a name="can-i-change-the-indexing-policy"></a>是否可以更改索引策略？
是，可以提供索引定义来更改索引策略。 有关详细信息，请参阅 [Azure Cosmos DB 功能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)。 需要适当地将这些设置编码并转义。 有关详细信息，请向 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 发送电子邮件。 

下面是 app.config 文件中采用 JSON 字符串格式的示例：

```csharp
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        } 
      ]
    }
  ],
  "excludedPaths": 
[
 {
      "path": "/anotherpath"
 }
]
}
```
### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>是否可以在本地将新的 Cassandra API SDK 用于模拟器？
[本地模拟器](local-emulator.md)目前不支持 Cassandra API。 

<a id="moving-to-cosmos-db"></a>
## <a name="questions-from-documentdb-customers"></a>DocumentDB 客户提出的问题
### <a name="why-are-you-moving-to-azure-cosmos-db"></a>为什么要迁移到 Azure Cosmos DB？ 

Azure Cosmos DB 是大规模全球分布式云数据库的重大飞跃。 现在，DocumentDB 客户可以访问 Azure Cosmos DB 提供的突破性系统和功能。

Azure Cosmos DB 在 2010 年以“Project Florence”的名义开始解决开发人员在 Microsoft 内部构建大型应用程序时面临的难题。 构建全球分布式应用并不是只有 Microsoft 才会遇到的难题，因此我们在 2015 年以 Azure DocumentDB 的形式向 Azure 开发人员提供了与此相关的第一代技术。 

自此之后，我们添加了新的特性，并引入了重要新功能。 成果就是 Azure Cosmos DB。 随着此次发布，DocumentDB 客户连同其数据自动顺利地成为 Azure Cosmos DB 客户。 这些功能涉及的领域包括核心数据库引擎以及全局分发、弹性可伸缩性，并享有行业领先的全面 SLA 保障。 具体而言，我们已改进了 Azure Cosmos DB 数据库引擎，使其能够有效地将所有热门数据模型、类型系统和 API 映射到 Azure Cosmos DB 的基础数据模型。 

对于开发人员而言，此项工作当前的成果包括对 [Gremlin](../cosmos-db/graph-introduction.md) 和[表存储 API](../cosmos-db/table-introduction.md) 的全新支持。 而这仅仅是一个开始。 我们计划不断添加其他热门 API 和更新的数据模型，为全球规模的性能和存储带来进步。 

必须指出的是，DocumentDB 的 [SQL 方言](../documentdb/documentdb-sql-query.md)始终是基础 Azure Cosmos DB 能够支持的多种 API 之一。 对于使用 Azure Cosmos DB 等完全托管服务的开发人员，该服务的唯一接口就是该服务公开的 API。 现有 DocumentDB 客户在操作时与以往其实并无不同。 Azure Cosmos DB 提供的 SQL API 与 DocumentDB 完全相同。 现在（以及将来），你可以访问其他以前无法访问的功能。 

我们持续努力的另一个成果是扩建了吞吐量和存储的全局分发与弹性缩放的基础。 我们对全局分发子系统做了几项基础性的增强。 一致前缀一致性模型就是这众多面向开发人员的功能之中的一个（总共提供五个定义完善的一致性模型）。 我们还会推出其他许多有趣的功能，在它们成熟时即会发布。 

### <a name="what-do-i-need-to-do-to-ensure-that-my-documentdb-resources-continue-to-run-on-azure-cosmos-db"></a>如何确保 DocumentDB 资源可继续在 Azure Cosmos DB 上运行？

完全不需要进行任何更改。 DocumentDB 资源现在称为 Azure Cosmos DB 资源，迁移后，服务不会发生任何中断。

### <a name="what-changes-do-i-need-to-make-for-my-app-to-work-with-azure-cosmos-db"></a>要进行哪些更改才能让应用配合 Azure Cosmos DB 工作？

不需要进行任何更改。 类、命名空间和 NuGet 包名称都没有变化。 与往常一样，我们建议使用最新的 SDK，以利用最新的功能和改进。 

### <a name="whats-changed-in-the-azure-portal"></a>Azure 门户有哪些变化？

DocumentDB 不再以 Azure 服务的形式显示在门户中。 它原来的位置上会显示一个新的 Azure Cosmos DB 图标，如下图所示。 可以像以前一样使用所有集合，并且仍可以缩放吞吐量、更改一致性级别和监视 SLA。 数据资源管理器（预览版）的功能已得到增强。 现在，可以在一个页面中查看和编辑文档、创建和运行查询，以及使用存储过程、触发器和 UDF，如下图所示： 

![“Azure Cosmos DB 集合”页](./media/faq/cosmos-db-data-explorer.png)

### <a name="are-there-changes-to-pricing"></a>价格是否有变化？

没有，在 Azure Cosmos DB 上运行应用的费用与以前相同。

### <a name="are-there-changes-to-the-slas"></a>SLA 是否有变化？

没有，有关可用性、一致性、延迟和吞吐量的 SLA 都保持不变，并且仍会显示在门户中。 有关详细信息，请参阅 [Azure Cosmos DB 的 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)。
   
![包含示例数据的待办事项应用](./media/faq/azure-cosmosdb-portal-metrics-slas.png)


[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
