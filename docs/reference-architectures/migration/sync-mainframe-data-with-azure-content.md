This example architecture outlines an implementation plan to replicate and sync data during modernization to Azure. It discusses technical aspects like data stores, tools, and services.

## Architecture

:::image type="complex" source="./images/sync-mainframe-data-with-azure.svg" alt-text="An architecture diagram that shows how to sync on-premises data and Azure databases data during mainframe modernization." border="false" lightbox="./images/sync-mainframe-data-with-azure.svg":::
   The diagram has two areas, one for on-premises components and one for Azure components. The on-premises area has two rectangles. One rectangle pictures databases and the other lists integration tools. The on-premises area also includes a server icon that represents the self-hosted integration runtime. The Azure area of the diagram also has rectangles. One is for pipelines. Others are for services that the solution uses to stage and prepare data. Another lists Azure databases. Arrows point from on-premises components to Azure components. These arrows represent the flow of data in the replication and sync processes. One of the arrows goes through the on-premises data gateway.
:::image-end:::

*Download a [Visio file](https://arch-center.azureedge.net/sync-mainframe-data-with-azure.vsdx) of this architecture.*

### Workflow

Mainframe and midrange systems update on-premises application databases at regular intervals. To maintain consistency, the solution syncs the latest data with Azure databases. The sync process involves the following steps:

1. Azure Data Factory dynamic pipelines orchestrate activities that range from data extraction to data loading. You can schedule pipeline activities, start them manually, or trigger them automatically.

   Pipelines group the activities that perform tasks. To extract data, Data Factory dynamically creates one pipeline for each on-premises table. You can then use a massively parallel implementation when you replicate data in Azure. You can also configure the solution to meet your requirements: 

   - **Full replication**: You replicate the entire database and make the necessary modifications to data types and fields in the target Azure database. 
   - **Partial, delta, or incremental replication**: You use watermark columns in source tables to sync the updated rows with Azure databases. These columns contain either a continuously incrementing key or a time stamp that indicates the table's last update. 

    Data Factory also uses pipelines for the following transformation tasks:

    - Data-type conversion
    - Data manipulation
    - Data formatting
    - Column derivation
    - Data flattening
    - Data sorting
    - Data filtering 

1. On-premises databases like Db2 zOS, Db2 for i, and Db2 LUW store the application data.
1. A self-hosted integration runtime (SHIR) provides the environment that Data Factory uses to run and dispatch activities.
1. Azure Data Lake Storage Gen2 and Azure Blob Storage provide a place for data staging. This step is sometimes required to transform and merge data from multiple sources.
1. For data preparation, Data Factory uses Azure Databricks, custom activities, and pipeline data flows to transform data quickly and effectively.
1. Data Factory loads data into the following relational and nonrelational Azure databases:

   - Azure SQL
   - Azure Database for PostgreSQL
   - Azure Cosmos DB
   - Azure Data Lake Storage
   - Azure Database for MySQL 

1. SQL Server Integration Services (SSIS): This platform can extract, transform, and load data.
1. The on-premises data gateway is a locally installed Windows client application that acts as a bridge between your local on-premises data sources and services in Azure.
1. A data pipeline in Microsoft Fabric is a logical grouping of activities that perform data ingestion from Db2 to Azure storage and databases.
1. Non-Microsoft tools: When the solution requires near real-time replication, you can use non-Microsoft tools.

### Components

This section describes other tools that you can use during data modernization, synchronization, and integration.

#### Tools

- [Microsoft Service for Distributed Relational Database Architecture (DRDA)](/host-integration-server/what-is-his#Data) is a component of [Host Integration Server (HIS)](/host-integration-server/what-is-his). Microsoft Service for DRDA is an application server that DRDA Application Requester (AR) clients use. Examples of DRDA AR clients include IBM Db2 for z/OS and Db2 for i5/OS. These clients use the application server to convert Db2 SQL statements and run them on SQL Server.

- [SQL Server Migration Assistant (SSMA) for Db2](/sql/ssma/sql-server-migration-assistant) automates migration from Db2 to Microsoft database services. While it runs on a virtual machine (VM), this tool converts Db2 database objects into SQL Server database objects and creates those objects in SQL.
  
#### Data integrators

- [Data Factory](/azure/data-factory/introduction) is a hybrid data integration service. You can use this fully managed, serverless solution to create, schedule, and orchestrate extract, transform, and load (ETL) workflows and extract, load, and transform ETL workflows.

- [Azure Synapse Analytics](/azure/synapse-analytics/overview-what-is) is an enterprise analytics service that accelerates time to insight across data warehouses and big data systems. Azure Synapse Analytics brings together the best of the following technologies and services:
   - SQL technologies, which you use in enterprise data warehousing.
   - Spark technologies, which you use for big data.
   - Azure Data Explorer, which you use for log and time series analytics.
   - Azure Pipelines, which you use for data integration and ETL and ELT workflows.
   - Deep integration with other Azure services, such as Power BI, Azure Cosmos DB, and Machine Learning.

- [SSIS](/sql/integration-services/sql-server-integration-services) is a platform for building enterprise-level data integration and transformation solutions. You can use SSIS to manage, replicate, cleanse, and mine data.

- [Azure Databricks](/azure/databricks/introduction/) is a data analytics platform. It's based on the Apache Spark open-source distributed processing system and is optimized for the Azure cloud platform. In an analytics workflow, Azure Databricks reads data from multiple sources and uses Spark to provide insights.

#### Data storage

- [SQL Database](/azure/well-architected/service-guides/azure-sql-database-well-architected-framework) is part of the [Azure SQL](/azure/azure-sql/azure-sql-iaas-vs-paas-what-is-overview?view=azuresql) family and is built for the cloud. This service offers the benefits of a fully managed and evergreen platform as a service (PaaS). SQL Database also provides AI-powered, automated features that optimize performance and durability. Serverless compute and [Hyperscale storage options](/azure/azure-sql/database/service-tier-hyperscale?view=azuresql) automatically scale resources on demand.

- [Azure SQL Managed Instance](/azure/well-architected/service-guides/azure-sql-managed-instance/reliability) is part of the Azure SQL service portfolio. This intelligent and scalable cloud database service combines the broadest SQL Server engine compatibility with all the benefits of a fully managed and evergreen PaaS. With SQL Managed Instance, you can modernize existing apps at scale.

- [SQL Server on Azure Virtual Machines](/azure/azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview?view=azuresql) provides a way to lift and shift SQL Server workloads to the cloud with 100% code compatibility. As part of the Azure SQL family, SQL Server on Azure Virtual Machines offers the combined performance, security, and analytics of SQL Server with the flexibility and hybrid connectivity of Azure. Use SQL Server on Azure Virtual Machines to migrate existing apps or build new apps. You can also access the latest SQL Server updates and releases, including SQL Server 2019.

- [Azure Database for PostgreSQL](/azure/well-architected/service-guides/postgresql) is a fully managed relational database service that's based on the community edition of the open-source [PostgreSQL](https://www.postgresql.org/) database engine. Use this service to focus on application innovation instead of database management. You can also scale your workload quickly and easily.

- [Azure Cosmos DB](/azure/well-architected/service-guides/cosmos-db) is a globally distributed, [multimodel](/azure/cosmos-db/nosql/modeling-data) database. Use Azure Cosmos DB to ensure that your solutions can elastically and independently scale throughput and storage across any number of geographic regions. This fully managed NoSQL database service guarantees single-digit, millisecond latencies at the ninety-ninth percentile anywhere in the world.

- [Data Lake Storage](/azure/storage/blobs/data-lake-storage-introduction) is a storage repository that holds a large amount of data in its native, raw format. Data lake stores are optimized for scaling to terabytes and petabytes of data. The data typically comes from multiple, heterogeneous sources and can be structured, semi-structured, or unstructured. [Data Lake Storage Gen2](/azure/databricks/connect/storage/azure-storage) combines Data Lake Storage Gen1 capabilities with Blob Storage. This next-generation data lake solution provides file system semantics, file-level security, and scale. It also offers the tiered storage, high availability, and disaster recovery capabilities of Blob Storage.

- Microsoft Fabric is an enterprise-ready, end-to-end analytics platform. It unifies data movement, data processing, ingestion, transformation, real-time event routing, and report building. It supports these capabilities with integrated services like Data Engineering, Data Factory, Data Science, Real-Time Analytics, Data Warehouse, and Databases.

- [Azure Database for MySQL](/azure/well-architected/service-guides/azure-db-mysql-cost-optimization) is a fully managed relational database service based on the [community edition of the open-source MySQL database engine](https://www.mysql.com/products/community/).

## Scenario details

Data availability and integrity are essential in mainframe and midrange modernization. [Data-first strategies](/azure/architecture/example-scenario/mainframe/modernize-mainframe-data-to-azure) help keep data intact and available during the migration to Azure. To prevent disruptions during modernization, sometimes you need to replicate data quickly or keep on-premises data in sync with Azure databases.

Specifically, this solution covers:

- Extraction: Connecting to and extracting from a source database.
- Transformation:
  - Staging: Temporarily storing data in its original format and preparing it for transformation.
  - Preparation: Transforming and manipulating data by using mapping rules that meet target database requirements.
- Loading: Inserting data into a target database.

### Potential use cases

Data replication and sync scenarios that can benefit from this solution include:

- Command Query Responsibility Segregation (CQRS) architectures that use Azure to service all inquire channels.
- Environments that test on-premises applications and rehosted or re-engineered applications in parallel.
- On-premises systems with tightly coupled applications that require phased remediation or modernization.

## Recommendations

When you use Data Factory to extract data, take steps to [tune the performance of the copy activity](/azure/data-factory/copy-activity-performance#performance-tuning-steps).

## Considerations

These considerations implement the pillars of the Azure Well-Architected Framework, which is a set of guiding tenets that can be used to improve the quality of a workload. For more information, see [Microsoft Azure Well-Architected Framework](/azure/well-architected/).

Keep these points in mind when you consider this architecture.

### Reliability

Reliability ensures your application can meet the commitments you make to your customers. For more information, see [Design review checklist for Reliability](/azure/well-architected/reliability/checklist).

- Infrastructure management, including [availability](/azure/azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview), is automated in Azure databases.

- See [Pooling and failover](/host-integration-server/core/planning-and-architecting-solutions-using-microsoft-service-for-drda#pooling-and-failover) for information about Microsoft Service for DRDA failover protection.

- You can cluster the on-premises data gateway and integration runtime (IR) to provide higher availability guarantees.

### Security

Security provides assurances against deliberate attacks and the abuse of your valuable data and systems. For more information, see [Design review checklist for Security](/azure/well-architected/security/checklist).

- Use [network security groups](/azure/virtual-network/manage-network-security-group) to limit access to only what each service needs to function.

- Use [private endpoints](/azure/private-link/private-endpoint-overview) for your PaaS services. Use service firewalls that are both reachable and unreachable through the internet to supplement security for your services.

- Use managed identities for component-to-component data flows.

- See [Planning and architecting solutions by using Microsoft Service for DRDA](/host-integration-server/core/planning-and-architecting-solutions-using-microsoft-service-for-drda) to learn about the types of client connections that Microsoft Service for DRDA supports. Client connections affect the nature of transactions, pooling, failover, authentication, and encryption on your network.

### Cost Optimization

Cost Optimization is about looking at ways to reduce unnecessary expenses and improve operational efficiencies. For more information, see [Design review checklist for Cost Optimization](/azure/well-architected/cost-optimization/checklist).

- Pricing models vary between component services. Review the pricing models of the available component services to ensure that they fit your budget.

- Use the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) to estimate the cost of implementing this solution.

### Operational Excellence

Operational Excellence covers the operations processes that deploy an application and keep it running in production. For more information, see [Design review checklist for Operational Excellence](/azure/well-architected/operational-excellence/checklist).

- Infrastructure management, including [scalability](/azure/architecture/guide/design-principles/scale-out), is automated in Azure databases.

- You can [scale out the self-hosted IR](/azure/data-factory/concepts-integration-runtime#self-hosted-ir-compute-resource-and-scaling) by associating the logical instance with multiple on-premises machines in active-active mode.

### Performance Efficiency

Performance Efficiency is the ability of your workload to meet the demands placed on it by users in an efficient manner. For more information, see [Design review checklist for Performance Efficiency](/azure/well-architected/performance-efficiency/checklist).

- Consider [Azure ExpressRoute](/azure/well-architected/service-guides/azure-expressroute) as a high-scale option if your implementation uses significant bandwidth for initial replication or ongoing changed data replication.

- Choose the right [IR configuration](/azure/data-factory/choose-the-right-integration-runtime-configuration) for your scenario.

## Next steps

- Contact [Azure Data Engineering - On-premises Modernization][Email address for information on Azure Data Engineering On-premises Modernization] for more information.
- Read the [Migration guide](/data-migration/).

## Related resources

- [Azure data architecture guide](/azure/architecture/databases/)
- [Azure data platform end-to-end](/azure/architecture/example-scenario/dataplate2e/data-platform-end-to-end)

[Email address for information on Azure Data Engineering On-premises Modernization]: mailto:datasqlninja@microsoft.com
