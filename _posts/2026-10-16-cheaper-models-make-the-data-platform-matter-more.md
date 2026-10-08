---
layout: post
title:  "Cheaper Models Make the Data Platform Matter More"
date:   2026-10-16 08:00:00 -0700
categories: redshift
---

*Part 3 of a 4-part series on Redshift and the modern data platform: {% include series-link.html slug="redshift-quietly-found-the-middle-ground" label="Part 1: Redshift Quietly Found the Middle Ground" date="October 12" %} · {% include series-link.html slug="agentic-ai-needs-a-work-record" label="Part 2: Agentic AI Needs a Work Record" date="October 14" %} · {% include series-link.html slug="exploration-is-not-the-same-as-production" label="Part 4: Exploration Is Not the Same as Production" date="October 19" %}*

The message hits the team channel: did you see the new LLM pricing? A capable AI model, a fraction of the cost.

Within a week, someone has rebuilt the prototype around it. Within a month, the pipeline depends on it. Within a quarter, the vendor ships another price cut and the whole cycle starts over.

I have watched teams do this three times in a year. Each rebuild felt urgent. Each one taught the same lesson, which nobody wrote down: the model is the interchangeable part. Everything around it is not.

Seeing the news about cheaper AI models like Kimi and DeepSeek brought this back. One of the more interesting shifts happening right now is not just that large language models are getting better. It is that capable models are getting cheaper to run — lower API pricing while maintaining, and sometimes exceeding, benchmark scores.

*cough, cough*

Don't worry, we're not at AGI.

Open-weight models are becoming good enough for more enterprise tasks. That changes behavior. Just watch.

When intelligence gets cheaper, more people in your organization will use it. And those who already use it will use more of it.

More summaries.
More analysis.
More exception review.
More classification.
More code generation.
More data exploration.
More background jobs.
More "just ask the system" moments.

That sounds like an AI story.

But I think it is actually a data platform story.

## Every interaction needs context

Every one of those interactions needs good context. Context that gives the model a better first shot.

Customer data.
Operational data.
Product data.
Financial data.
Policy data.
Historical decisions.
Work records.
Cost signals.
All together.

The model may become more interchangeable. The data foundation does not.

It is easy to chase the model of the week. It is harder to build an architecture that can absorb model churn without rebuilding the data estate every quarter.

The better questions are:

Can you use different models for different workloads?
Can you route low-risk work to cheaper models?
Can you reserve premium models for higher-value tasks?
Can you measure quality, cost, latency, and business outcome?
Can you govern what each model or agent can access?
Can you see what is actually happening?

## Cost-only thinking is dangerous

There is a trap here, and it shows up in two places.

The first is model routing. Cost-only routing is a dangerous path. As I talked about at AWS Summit DC, cost does not always mean alignment with the business or the customer. The taxonomy has to come before the router: risk, data sensitivity, reversibility, required accuracy, latency, auditability, business impact. No vibing.

The second is cost optimization itself. In my Summit DC talk I made the point directly: a cost agent that only cuts is dangerous. Sometimes the right answer is to spend less. Sometimes the responsible answer is to spend more. Downsize the instance when CPU and memory stay low, sure. But also buy reliability when the workload earns it: add a proxy for connection storms, increase replicas or headroom for critical windows.

The question is alignment, not simply a smaller bill.

## The foundation absorbs the mess

Here is a small example of what a stable foundation looks like in practice. Agents produce messy output: semi-structured JSON, nested events, fields that change shape between runs. Traditionally you would shred that into rigid columns or leave it as strings that every query has to parse.

Iceberg V3, now fully supported in S3 Tables as of last week, has a native variant type for exactly this. You land semi-structured data without defining the schema up front, and the format shreds it into hidden columns with statistics that query engines use for file pruning. The mess gets absorbed by the foundation instead of leaking into every pipeline.

That is the pattern. The model changes, the agent output changes shape, the workload grows, and the foundation takes it without a rebuild.

That is where Redshift keeps showing up for me. Not as the model layer. As the governed analytical layer that helps enterprises understand the activity around humans, applications, agents, and models.

If open-weight models make experimentation cheaper, Redshift helps make the resulting activity measurable. If teams start using multiple models, Redshift helps compare outcomes. If AI workloads create more queries and events, Redshift helps analyze the pressure. If the business wants cost visibility, Redshift gives teams a familiar SQL foundation for asking where the spend and value are coming from.

## Many models, one foundation

The future is probably not one model. It is many models.

Some proprietary.
Some open-weight.
Some specialized.
Some cheap.
Some expensive.
Some embedded in applications.
Some running behind agents.

That means model optionality becomes important. But model optionality only works if the data platform underneath is stable, governed, observable, and flexible.

Cheaper models may change the cost of intelligence. But they do not remove the need for trusted data, workload isolation, governance, cost discipline, and analytical visibility.

If anything, they make those things more important.

*Next: {% include series-link.html slug="exploration-is-not-the-same-as-production" label="Part 4 — Exploration Is Not the Same as Production" date="October 19" %}, on the morning the dashboards stopped loading and what it taught us about workload placement.*

## References

- [Amazon S3 Tables now support all Apache Iceberg V3 data types](https://aws.amazon.com/blogs/aws/amazon-s3-tables-now-support-all-apache-iceberg-v3-data-types/)
- [Amazon Redshift RG features](https://aws.amazon.com/redshift/features/rg/)
- [Redshift Serverless](https://aws.amazon.com/redshift/redshift-serverless/)
- [Redshift data sharing](https://docs.aws.amazon.com/redshift/latest/dg/datashare-overview.html)
