---
title: How to manage legal tags in Microsoft Azure Data Manager for Energy
description: This article describes how to manage legal tags in Azure Data Manager for Energy
author: Lakshmisha-KS
ms.author: lakshmishaks
ms.service: energy-data-services
ms.topic: how-to
ms.date: 09/16/2024
ms.custom: template-how-to
---

# How to manage legal tags
In this article, you'll know how to use Search extensions service in your Azure Data Manager for Energy instance. Search-extension service is an SLB proprietary service that offers additional features to what is provided by OSDU. Specifically, it exposes elastic scripting and aggregation functionality to allow you to perform custom calculations to enable analytics workflows and provide insight into your data stored in the search service. This can be extremely helpful in scenarios where you need to run calculations or aggregations on sections ofyour data.

## API access
### Required roles
1. The search-extension service requires you to have access levels of users.datalake.admins or users.datalake.viewers orusers.datalake.editors.
1. In addition to service roles, you must be a member of data groups to access the data.

### Timeout
The connection timeout for the search extensions service APIs is set to 60 seconds. For any reason, if the request fails to get a response within 60 seconds, the service will return 408, "Request timed out". You may need to optimize your request if this happens.

## Aggregations GET API
The aggregations GET API provides various mechanisms for calculating and summarizing data as metric aggregations, such as sum or average from field values, bucket aggregations, such as grouping data into categories based on the field values or other criteria.

The request is made up of the following properties
* kind: The kind(s) to perform the aggregation against
* x-query: (optional) the filter to limit what data the aggregation is performed against
* x-aggregation: the calculation/aggregation to perform on the data
* x-spatial-filter: the spatial filter to define the area of interest where data the aggregation is performed against

This can be extremely helpful in scenarios where you need to run calculations or aggregations on sections of your data. Previously you would need to pull all the data to do this. This could take minutes or even hours and be extremely costly in terms of ingress and egress. Now you can do the same calculations in seconds directly onthe data source.

The kind and -x-query values are the same as expected in the OSDU search service (the kind and queryparameters respectively).
The x-aggregation definition exposes almost directly the Elastic aggregation syntax as described in the [elastic documentation](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations.html). This includes sub-aggregations, painless scripts, and sorting/limits capabilities, etc. Refer to the [Elastic documentation](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations.html) for what aggregations are supported and how they are used.

The x-spatial-filter definition supports geo-point geo data which supports lat/lon pairs. x-spatial-filter and x-query group in the request header have AND relationship. If both of the criteria are defined in the request header, then the aggregation service will return results which match both clauses.

The queries in x-spatial-filter group are Geo Distance, Geo Polygon and Bounding Box. Only one spatial criteriacan be used while defining filter.

> [!NOTE]
> Geo-spatial fields (which are indexed with GeoJSON FeatureCollection payload) in Search service query response have different structure compared to Storage records and optimized for search use-case. These are novalid GeoJSON. To retrieve, valid GeoJSON please use Storage service's record API.

### Geo distance
Filters documents that include only hits that exist within a specific distance from a geo point.

