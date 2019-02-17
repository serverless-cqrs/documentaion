# Adapter Interface

## Description

Adapters allow us to persist **events** \(for the write model\) and/or **projections** \(for the read model\).

If you can't find an adapter for your favorite database, you can build one by implementing the **Adapter Interface**.

The **Adapter Interface** has two sets of methods, depending if you are persisting **events**, **projections**, or both. Not every database is suitable for storing both. DynamoDB, for example, is very well suited for storing **events**, but lacks the search capabilities which are useful when storing **projections**. ElasticSearch, on the other hand, is great for storing, and searching, arbitrary JSON objects \(**projections**\) but is not very well suited for a stack of **events**.

When implementing the **Adapter Interface**, you can choose which set of methods to implement.

## Methods

{% page-ref page="write-model.md" %}

{% page-ref page="read-model.md" %}

## Examples

{% page-ref page="../../components/dynamodb-adapter.md" %}

{% page-ref page="../../components/elasticsearch-adapter.md" %}

{% page-ref page="../../components/memory-adapter.md" %}

