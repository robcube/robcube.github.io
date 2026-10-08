---
layout: post
title:  "Exploration Is Not the Same as Production"
date:   2026-10-19 08:00:00 -0700
categories: redshift
---

*Part 4 of a 4-part series on Redshift and the modern data platform: {% include series-link.html slug="redshift-quietly-found-the-middle-ground" label="Part 1: Redshift Quietly Found the Middle Ground" date="October 12" %} · {% include series-link.html slug="agentic-ai-needs-a-work-record" label="Part 2: Agentic AI Needs a Work Record" date="October 14" %} · {% include series-link.html slug="cheaper-models-make-the-data-platform-matter-more" label="Part 3: Cheaper Models Make the Data Platform Matter More" date="October 16" %}*

Nine in the morning. The dashboards stop loading. Analysts stare at spinning queries. Somewhere in the cluster, a scheduled refresh is fighting an ingestion job for the same capacity, and both are losing.

Nobody misconfigured anything. The workload just grew up. What used to be exploration became production while nobody was watching, and the infrastructure never got the memo.

The recent AWS analytics updates tell a pretty clear story about how to answer this. Glue 6.0 got cheaper and stronger for open table formats. Athena keeps improving broad serverless access. Redshift RG is getting faster and more cost-predictable for warehouse and lakehouse analytics.

That is not three random product updates. It is an operating model coming together: S3 as durable storage, Iceberg as the open table format, Glue as the transformation and catalog layer, Athena as the exploratory query layer, and Redshift RG as the governed performance layer.

That distinction matters because not every query belongs in the same place.

## Capacity alignment, not cost cutting

This came up a lot in my AWS Summit DC talk: cost optimization is not indiscriminate cost cutting. It is capacity alignment. Sometimes the right answer is to spend less. Sometimes the responsible answer is to spend more. The point is to understand what the workload is actually doing.

The talk's framing was simple: databases expose demand, waste, and risk. The discipline is turning that telemetry into explainable decisions about usage, capacity, and cost.

Start with the signals. For any database workload, you want three views: usage (what the workload is actually doing: CPU, memory, connections, latency, IOPS, peak demand), capacity (what you have provisioned: instance class, storage type, throughput, replicas), and cost (what you are paying: hourly spend, provisioned IOPS, storage, and the headroom you are deliberately buying).

Right-sizing starts with evidence, not instinct.

A concrete example from the talk: RDS storage. Provisioned IOPS should be justified by observed usage. If the telemetry shows IOPS underused, migrating from gp2 to gp3 is straightforward savings. But if the workload shows sustained I/O pressure, io1/io2 is the right call. The agent question is always the same: are we paying for IOPS we actually use?

And sometimes alignment means spending more. A cost agent that only cuts is dangerous. Downsize when CPU and memory stay low, but buy reliability when the workload earns it: a proxy for connection storms, more replicas or headroom for critical windows. The question is alignment, not simply a smaller bill.

## The storage chapter: tables that manage themselves

There is a storage side to this story, and it got interesting last week. Amazon S3 Tables now support all Apache Iceberg V3 data types, which completes a picture worth understanding even if you never touch Redshift.

S3 Tables gives you Iceberg tables on S3 with automatic compaction and snapshot management handled for you. The small-files problem, the maintenance windows, the "who is compacting the tables this weekend" rota: gone, handled by the service.

And the V3 support means those tables now handle the messy reality of modern data natively. Semi-structured JSON lands in variant columns instead of strings every query has to parse. Geospatial coordinates and nanosecond timestamps get native types instead of string encodings. Row lineage tracks what changed. Deletion vectors make compliance deletes cheap instead of scattering tiny files across the table.

Just as important: both S3 Tables and the Glue Data Catalog now speak the Iceberg REST Catalog API. That means Athena, Glue, Redshift, and anything else that speaks the API can work the same tables. The storage is open, the catalog is shared, and each engine shows up for the workload it fits.

This is the foundation the workload-placement story stands on. Keep storage open and managed. Choose the compute per workload.

## The Redshift version: contention windows

The same thinking applies to Redshift, and this is where workload placement gets real.

Look at query history on a busy cluster. Ingestion jobs, dashboard refreshes, analyst exploration, and batch jobs all compete for the same capacity. You will often find a contention window, say 9 to 11 AM, where queues show pressure and skew shows wasted compute.

The decision is whether to stay combined or split. If the workload is predictable and shared capacity meets your SLOs, tune what you have: WLM queues, schedules, views, workload controls. If a repeatable, business-critical workload keeps contending, that workload has earned isolation.

Tune first. Isolate when the queues say so. Either way, the decision comes from evidence, not from a migration plan written in a slide deck.

This is also where modernization matters. Moving to the cloud is not just relocating data. It is making the data estate ready for AI-level querying, governed tool access, and MCP-style patterns where systems discover, query, and act against trusted data safely.

## Place the workload, not the data

Athena is excellent for fast, serverless exploration in S3. You do not want to stand up infrastructure. You do not know if the question will matter yet. You are validating shape, quality, and usefulness.

But when the query pattern becomes repeatable, high-volume, business-critical, or concurrency-heavy, the economics change. "Serverless" does not automatically mean "cheap." It means you need to understand the workload.

How much data are you scanning? How often does it run? How many users depend on it? Does it need predictable performance, workload isolation, chargeback, governance, or auditability?

This is where Redshift RG changes the conversation. RG gives Redshift a built-in data lake query engine for Iceberg and Parquet, with no separate per-TB Spectrum scan charges for RG lake queries. It brings better performance, JIT Analyze, intelligent caching, and a more predictable cost model for repeatable analytics.

Use Athena when you are exploring. Use Glue when you are preparing and maintaining open table data. Use Redshift RG when the workload becomes important enough to deserve governed performance, workload isolation, and cost predictability.

Exploration should be easy. Production should be predictable. Governance should not be optional. And cost should not be discovered accidentally at the end of the month.

The lakehouse is not just a storage pattern. It is a workload placement problem.

## One agent, one database

If you want to put this into practice, the talk's closing advice still holds: start narrow. One agent, one database, one measurable optimization. Pick a single database with optimization pain: a steady non-prod RDS instance, over-provisioned IOPS, a recurring Redshift contention window. Measure before and after, on cost and performance. Expand only after the loop earns trust.

Trust grows when the loop proves itself.

*This is the kind of modernization work I enjoy most at Slalom: helping teams move beyond "which service?" into workload placement, governance, cost discipline, and business-aligned architecture.*

## References

- [Amazon S3 Tables now support all Apache Iceberg V3 data types](https://aws.amazon.com/blogs/aws/amazon-s3-tables-now-support-all-apache-iceberg-v3-data-types/)
- [AWS Glue 6.0: lower pricing and Iceberg v3](https://aws.amazon.com/about-aws/whats-new/2026/08/aws-glue-6-0-price-reduction-iceberg-v3/)
- [Amazon Redshift RG features](https://aws.amazon.com/redshift/features/rg/)
- [AWS Big Data Blog: Amazon Redshift RG](https://aws.amazon.com/blogs/big-data/amazon-redshift-rg-faster-and-lower-cost-graviton-powered/)
- [Redshift adds rg.large and rg.12xlarge](https://aws.amazon.com/about-aws/whats-new/2026/07/amazon-redshift-adds-rg-large-12xlarge-instance-sizes/)
- [Redshift support for Apache Iceberg v3](https://aws.amazon.com/about-aws/whats-new/2026/08/amazon-redshift-supports-apache-iceberg-v3/)
- [Amazon Athena pricing](https://aws.amazon.com/athena/pricing/)
