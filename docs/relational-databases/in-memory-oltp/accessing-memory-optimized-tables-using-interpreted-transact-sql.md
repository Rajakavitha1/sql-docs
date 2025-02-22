---
title: "Memory-optimized tables using interpreted T-SQL"
description: Learn about accessing memory-optimized tables using interpreted Transact-SQL (Transact-SQL batches or stored procedures in SQL Server).
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.date: "05/31/2016"
ms.prod: sql
ms.technology: in-memory-oltp
ms.topic: conceptual
ms.custom: seo-dt-2019
ms.assetid: 92a44d4d-0e53-4fb0-b890-de264c65c95a
monikerRange: "=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current"
---
# Accessing Memory-Optimized Tables Using Interpreted Transact-SQL
[!INCLUDE[tsql-appliesto-ss2014-asdb-xxxx-xxx_md](../../includes/appliesto-ss-asdb-xxxx-xxx-md.md)]

 With only a few exceptions, you can access memory-optimized tables using any [!INCLUDE[tsql](../../includes/tsql-md.md)] query or DML operation (select, insert, update, or delete), ad hoc batches, and SQL modules such as stored procedures, table-value functions, triggers, and views.  
  
Interpreted [!INCLUDE[tsql](../../includes/tsql-md.md)] refers to [!INCLUDE[tsql](../../includes/tsql-md.md)] batches or stored procedures other than a natively compiled stored procedure. Interpreted [!INCLUDE[tsql](../../includes/tsql-md.md)] access to memory-optimized tables is referred to as interop access.  

Starting with [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)], queries in interpreted [!INCLUDE[tsql](../../includes/tsql-md.md)] can scan memory-optimized tables in parallel, instead of just in serial mode.

Memory-optimized tables can also be accessed using a natively compiled stored procedure. Natively compiled stored procedures are recommended for performance-critical OLTP operations.  
  
Interpreted [!INCLUDE[tsql](../../includes/tsql-md.md)] access is recommended for these scenarios:  
  
- Ad hoc queries and administrative tasks.  
  
- Reporting queries, which typically use constructs not available in natively compiled stored procedures (such as *window* functions, sometimes referred to as [OVER](../../t-sql/queries/select-over-clause-transact-sql.md) functions).  
  
- To migrate performance-critical parts of your application to memory-optimized tables, with minimal (or no) application code changes. You can potentially see performance improvements from migrating tables. If you then migrate stored procedures to natively compiled stored procedures, you may see further performance improvement.  
  
- When a [!INCLUDE[tsql](../../includes/tsql-md.md)] statement is not available for natively compiled stored procedures.  
  
However, the following [!INCLUDE[tsql](../../includes/tsql-md.md)] constructs are not supported in interpreted [!INCLUDE[tsql](../../includes/tsql-md.md)] stored procedures that access data in a memory-optimized table.  
  
|Area|Unsupported|  
|----------|-----------------|  
|Access to tables|TRUNCATE TABLE<br /><br /> MERGE (memory-optimized table as target)<br /><br /> Dynamic and keyset cursors (these automatically degrade to static).<br /><br /> Access from CLR modules, using the context connection.<br /><br /> Referencing a memory-optimized table from an indexed view.|  
|Cross-database|Cross-database queries<br /><br /> Cross-database transactions<br /><br /> Linked servers|  
  
## Table Hints

For more information about table hints, see. [Table Hints &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-table.md). The SNAPSHOT was added to support [!INCLUDE[inmemory](../../includes/inmemory-md.md)].  
  
The following table hints are not supported when accessing a memory-optimized table using interpreted [!INCLUDE[tsql](../../includes/tsql-md.md)].  

:::row:::
    :::column:::
        HOLDLOCK

        PAGLOCK

        READUNCOMMITTED

        TABLOCKXX
    :::column-end:::
    :::column:::
        IGNORE_CONSTRAINTS

        READCOMMITTED

        ROWLOCK

        UPDLOCK
    :::column-end:::
    :::column:::
        IGNORE_TRIGGERS

        READCOMMITTEDLOCK

        SPATIAL_WINDOW_MAX_CELLS = *integer*

        XLOCK
    :::column-end:::
    :::column:::
        NOWAIT

        READPAST

        TABLOCK
    :::column-end:::
:::row-end:::

When accessing a memory-optimized table from an explicit or implicit transaction using interpreted [!INCLUDE[tsql](../../includes/tsql-md.md)], you must do at least one of the following:  
  
- Specify  an [isolation level table hint](../../relational-databases/in-memory-oltp/transactions-with-memory-optimized-tables.md) such as SNAPSHOT, REPEATABLEREAD, or SERIALIZABLE.  
  
- Set the database option [MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT](../../t-sql/statements/alter-database-transact-sql-set-options.md) to ON.  
  
An isolation level table hint is not required for memory-optimized tables accessed by queries running in [auto-commit mode](../../odbc/reference/develop-app/auto-commit-mode.md).  
  
## See Also

[Transact-SQL Support for In-Memory OLTP](../../relational-databases/in-memory-oltp/transact-sql-support-for-in-memory-oltp.md)   

[Migrating to In-Memory OLTP](./plan-your-adoption-of-in-memory-oltp-features-in-sql-server.md)
