---
title: "SQL 数据库：什么是 DTU？ | Microsoft Docs"
description: "了解什么是 Azure SQL 数据库事务单元。"
keywords: "数据库选项,数据库性能"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: CarlRabeler
ms.assetid: 89e3e9ce-2eeb-4949-b40f-6fc3bf520538
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 04/14/2017
ms.author: carlrab
ms.openlocfilehash: 4ab447cd2ad71a787e4d6bb6052299cec52d73d0
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2017
---
# <a name="database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>数据库事务单位 (DTU) 和弹性数据库事务单位 (eDTU)
本文介绍数据库事务单位 (DTU) 和弹性数据库事务单位 (eDTU)，以及达到最大 DTU 或 eDTU 时发生的情况。  

## <a name="what-are-database-transaction-units-dtus"></a>数据库事务单位 (DTU) 的定义是？
对于[服务层](sql-database-single-database-resources.md)内特定性能级别的单个 Azure SQL 数据库，Microsoft 保证该数据库（独立于 Azure 云中的任何其他数据库）可获得一定级别的资源，并提供可预测的性能级别。 此资源量是以若干数据库事务单位或 DTU 计算的，是 CPU、内存、I/O（数据和事务日志 I/O）的混合度量值。 这些资源之间的比率最初由 [OLTP 基准工作负荷](sql-database-benchmark-overview.md)决定，旨在作为典型的真实 OLTP 工作负荷。 工作负荷超过任何以上资源量时，吞吐量将受到限制，从而导致性能下降和超时。 工作负荷使用的资源不会影响 Azure 云中其他 SQL 数据库可用的资源，而其他工作负荷使用的资源也不会影响用户自己的 SQL 数据库可用的资源。

![边界框](./media/sql-database-what-is-a-dtu/bounding-box.png)

要了解 Azure SQL 数据库在不同性能级别和服务层之间的资源相对数量，DTU 将最为有用。 例如，提高数据库的性能级别可使 DTU 加倍，这等同于使该数据库可用的资源集增加一倍。 例如，具有 1750 个 DTU 的高级 P11 数据库提供的 DTU 计算能力是具有 5 个 DTU 的基本数据库的 350 倍。  

若要更深入了解工作负荷的资源 (DTU) 消耗，请使用 [Azure SQL 数据库 Query Performance Insight](sql-database-query-performance.md)：

- 按 CPU/持续时间/执行计数确定热门查询，可以对这些查询进行调整来提高性能。 例如，I/O 密集型查询可能会受益于使用[内存中优化技术](sql-database-in-memory.md)，以便在某个特定服务层和性能级别更好地利用可用内存。
- 深入了解查询详情，查看其文本和资源使用历史记录。
- 访问性能优化建议可显示 [SQL 数据库顾问](sql-database-advisor.md)执行的操作。

可以随时 [更改服务层](sql-database-service-tiers.md)，将应用程序故障时间降至最低（通常在平均 4 秒以下）。 许多业务和应用只要能够创建数据库并按需调高或调低性能即可，尤其是当使用模式相对容易预测时。 但如果有无法预测的使用模式，则管理成本和业务模式就会变得相当困难。 对于这种情况，可以使用一个具有一定数量 eDTU 的弹性池，这些 eDTU 在池中的多个数据库之间共享。

![SQL 数据库简介：按层和级别统计的单一数据库 DTU](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

## <a name="what-are-elastic-database-transaction-units-edtus"></a>弹性数据库事务单位 (eDTU) 的定义是？
不需要为 SQL 数据库提供一组始终可用的专用资源 (DTU)（无论是否需要），可将数据库放在 SQL 数据库服务器上的[弹性池](sql-database-elastic-pool.md)中，该服务器在这些数据库之间共享资源池。 弹性池中的共享资源用弹性数据库事务单位或 eDTU 度量。 弹性池是一种简单的低成本高效益的解决方案，用于管理使用模式变化很大且不可预测的多个数据库的性能目标。 在弹性池中，可以确保不会出现一个数据库使用池中所有资源的情况，并且确保弹性池中的数据库始终可以使用最少的资源量。 

![SQL 数据库简介：按层和级别统计的 eDTU](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

对池提供了固定数量的 eDTU，以获得固定价格。 在弹性池中，单个数据库能够在配置的边界范围内灵活自动缩放。 负载较重的数据库可以消耗更多的 eDTU 来满足需求，负载较轻的数据库消耗更少的 eDTU，完全无负载的数据库则不消耗任何 eDTU。 为整个池而不是每个数据库预配资源可以简化管理任务，使池的预算可预测。

可以对现有池增加额外的 eDTU，这不会造成数据库关闭，也不会影响池中的数据库。 同样，随时可以从现有池中删除不再需要的额外 eDTU。 可以在池中添加或删除数据库，或者限制重负载数据库的 eDTU 用量，以便为其他数据库预留 eDTU。 如果可以预见到某个数据库的资源利用率不高，可将其移出池，并使用所需的可预测资源量将它配置为单一数据库。

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>如何确定工作负荷所需的 DTU 数？
如果打算将现有的本地或 SQL Server 虚拟机工作负荷迁移到 Azure SQL 数据库，可以使用 [DTU 计算器](http://dtucalculator.azurewebsites.net/) 来估计所需的 DTU 数。 对于现有的 Azure SQL 数据库工作负荷，可以使用 [SQL 数据库 Query Performance Insight](sql-database-query-performance.md) 来了解数据库资源使用量 (DTU)，更深入地了解如何优化工作负荷。 也可以使用 [sys.dm_db_ resource_stats DMV](https://msdn.microsoft.com/library/dn800981.aspx) 获取最近一小时的资源消耗信息。 或者，可以查询目录视图 [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx)，获取最近 14 天的同类数据，不过，五分钟平均值的准确性较低。

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>如何知道是否可以从资源的弹性池受益？
池适合具有特定使用模式的大量数据库。 对于给定的数据库，此模式的特征是低平均使用量与相对不频繁的使用高峰。 SQL数据库自动评估现有 SQL 数据库服务器中数据库的历史资源使用率，并在 Azure 门户中推荐适当的池配置。 有关详细信息，请参阅[何时使用弹性池？](sql-database-elastic-pool.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>达到最大 DTU 时会发生什么情况？
会校准并控制性能级别，以便在选定服务层/性能级别所允许的最大限制范围内提供所需的资源来运行数据库工作负荷。 如果工作负荷达到了 CPU/数据 IO/日志 IO 限制中的其中一个限制，会继续接收资源直到达到最大允许级别，但是，可能会发现查询延迟不断增加。 这些限制不会导致任何错误，而只会减慢工作负荷，直到严重变慢，以致于查询开始超时。如果达到了并发用户会话/请求（工作线程）的最大允许数目限制，会看到明确的错误。 有关 CPU、内存、数据 I/O 和事务日志 I/O 以外的资源的限制信息，请参阅 [Azure SQL 数据库资源限制]( sql-database-resource-limits.md#what-happens-when-database-and-elastic-pool-resource-limits-are-reached) 。

## <a name="next-steps"></a>后续步骤
* 有关适用于单一数据库和弹性池上的 DTU 和 eDTU，以及除 CPU、内存、数据 I/O 和事务日志 I/O 之外的其他资源限制的信息，请参阅[服务层](sql-database-service-tiers.md)。
* 请参阅 [SQL 数据库 Query Performance Insight](sql-database-query-performance.md) 以了解 (DTU) 使用情况。
* 要了解用于确定 DTU 混合比例的 OLTP 基准工作负荷所依据的方法，请参阅 [SQL 数据库基准概述](sql-database-benchmark-overview.md) 。
