---
title: Azure CLI Samples for Azure Cosmos DB | Microsoft Docs
description: This article lists several Azure CLI code samples available for interacting with Azure Cosmos DB. View API-specific CLI samples.
author: seesharprun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 08/19/2022
ms.author: sidandrews
ms.reviewer: mjbrown 
ms.custom: devx-track-azurecli, seo-azure-cli
keywords: cosmos db, azure cli samples, azure cli code samples, azure cli script samples
---

# Azure CLI samples for Azure Cosmos DB Core (SQL) API

[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

The following tables include links to sample Azure CLI scripts for the Azure Cosmos DB SQL API and to sample Azure CLI scripts that apply to all Cosmos DB APIs. Common samples are the same across all APIs.

These samples require Azure CLI version 2.30 or later. Run `az --version` to find the version. If you need to install or upgrade, see [Install Azure CLI](/cli/azure/install-azure-cli). If using Azure Cloud Shell, the latest version is already installed.

## Core (SQL) API Samples

|Task | Description |
|---|---|
| [Create an Azure Cosmos account, database, and container](../scripts/cli/sql/create.md)| Creates an Azure Cosmos DB account, database, and container for Core (SQL) API. |
| [Create a serverless Azure Cosmos account, database, and container](../scripts/cli/sql/serverless.md)| Creates a serverless Azure Cosmos DB account, database, and container for Core (SQL) API. |
| [Create an Azure Cosmos account, database, and container with autoscale](../scripts/cli/sql/autoscale.md)| Creates an Azure Cosmos DB account, database, and container with autoscale for Core (SQL) API. |
| [Perform throughput operations](../scripts/cli/sql/throughput.md) | Read, update, and migrate between autoscale and standard throughput on a database and container.|
| [Lock resources from deletion](../scripts/cli/sql/lock.md)| Prevent resources from being deleted with  resource locks.|
|||

## Common API Samples

These samples apply to all Azure Cosmos DB APIs. These samples use a SQL (Core) API account, but these operations are identical across all database APIs in Cosmos DB.

|Task | Description |
|---|---|
| [Add or fail over regions](../scripts/cli/common/regions.md) | Add a region, change failover priority, trigger a manual failover.|
| [Perform account key operations](../scripts/cli/common/keys.md) | List account keys, read-only keys, regenerate keys and list connection strings.|
| [Secure with IP firewall](../scripts/cli/common/ipfirewall.md)| Create a Cosmos account with IP firewall configured.|
| [Secure new account with service endpoints](../scripts/cli/common/service-endpoints.md)| Create a Cosmos account and secure with service-endpoints.|
| [Secure existing account with service endpoints](../scripts/cli/common/service-endpoints-ignore-missing-vnet.md)| Update a Cosmos account to secure with service-endpoints when the subnet is eventually configured.|
| [Find existing free-tier account](../scripts/cli/common/free-tier.md)| Find whether there is an existing free-tier account in your subscription.|
|||

## Next steps

Reference pages for all Azure Cosmos DB CLI commands are available in the [Azure CLI Reference](/cli/azure/cosmosdb).

For Azure CLI samples for other APIs see:

- [CLI Samples for Cassandra](../cassandra/cli-samples.md)
- [CLI Samples for Gremlin](../graph/cli-samples.md)
- [CLI Samples for MongoDB API](../mongodb/cli-samples.md)
- [CLI Samples for Table](../table/cli-samples.md)
