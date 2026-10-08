---
layout: post
title:  "Agentic AI Needs a Work Record"
date:   2026-10-14 08:00:00 -0700
categories: redshift
---

*Part 2 of a 4-part series on Redshift and the modern data platform: {% include series-link.html slug="redshift-quietly-found-the-middle-ground" label="Part 1: Redshift Quietly Found the Middle Ground" date="October 12" %} · {% include series-link.html slug="cheaper-models-make-the-data-platform-matter-more" label="Part 3: Cheaper Models Make the Data Platform Matter More" date="October 16" %} · {% include series-link.html slug="exploration-is-not-the-same-as-production" label="Part 4: Exploration Is Not the Same as Production" date="October 19" %}*

It is 2 AM. An agent ran. A number on a report changed.

By morning, someone asks the obvious question: why? What did it look at? What did it decide? Who approved it?

Nobody can answer. Not the analyst. Not the dashboard vendor. Not the agent framework. The work happened, the output exists, and the trail between them is gone.

In {% include series-link.html slug="redshift-quietly-found-the-middle-ground" label="Part 1" date="October 12" %} I argued that Redshift found a pragmatic middle ground: performance, governance, interoperability, operational familiarity, and lower cost. The more I think about agentic AI, the more important that middle ground feels.

Because agents do not just ask questions.

They create work.

They investigate.
They compare.
They retry.
They summarize.
They trigger follow-up actions.
They may run continuously in the background.

That changes the enterprise data problem.

A dashboard refresh is easy to reason about. A human analyst query is usually attributable. But an agentic workflow can involve multiple steps, tools, data sources, permissions, and decisions before anyone sees the final output.

## The work record

That is why I like [Gary Samuelson's](https://garysamuelson.github.io/) framing around the "work record." Gary builds on [Nate B. Jones'](https://www.youtube.com/@natebjones) point that the agentic workflow opportunity depends on more than better models. It depends on infrastructure that can capture the work itself:

What was the goal?
What data was accessed?
What actions were taken?
What policies applied?
Who owned the outcome?
What changed?
What did it cost?

That is the part many AI demos skip. They show the answer. They do not show the work as an enterprise object.

And enterprises are going to need that object. Not just for audit. For optimization, cost control, governance, and trust.

Agentic AI will produce a new class of operational and analytical signals: agent runs, tool calls, query patterns, policy checks, human corrections, accepted outputs, rejected outputs, cost by workflow, outcomes by process.

Those signals need somewhere durable, governed, and queryable to live.

## The storage layer is learning to keep records too

Here is a development that landed last week and fits this story better than most product announcements do. Amazon S3 Tables now support all Apache Iceberg V3 data types, and two of the V3 features are work-record machinery at the storage layer.

The first is row lineage. Every record automatically carries a row ID and a last-updated sequence number. Downstream jobs can ask "what changed?" without scanning the whole table. The table itself remembers its own history.

The second is deletion vectors. A compliance request to remove thousands of records from a billion-row table used to scatter small delete files everywhere, slowing queries until compaction caught up. Now it writes a single compact binary file. The delete is recorded cleanly, and maintenance stays cheap.

Neither of these is a full work record. But the direction is unmistakable: the industry is pushing provenance and change-tracking down into the table format itself. The question "what happened to this data?" is becoming answerable at every layer of the stack.

## What a work record looks like in practice

This is where my AWS Summit DC talk connects directly. The talk was about AI agents that observe database telemetry and recommend usage, capacity, and cost alignment. But the deeper point was about what the agent must produce: reviewable operational evidence, not vague advice.

Every agent decision in that loop carries four parts: the spec (allowed scope and constraints), the evidence (metrics, logs, queries, cost), the decision (finding, tradeoff, approval path), and the artifact (runbook, CLI command, infrastructure diff, or ticket).

A concrete example from the talk, a storage recommendation:

```json
{
  "finding": "IOPS underused",
  "option": "migrate to gp3",
  "guardrail": "check latency"
}
```

And a Redshift contention decision:

```json
{
  "finding": "9-11 AM contention",
  "recommendation": "Tune WLM first; isolate if queues remain"
}
```

That is a work record in miniature. Goal, evidence, options considered, guardrails, and a recommendation with an approval path. Now imagine that pattern applied to every agent step, tool call, policy check, human approval, correction, escalation, cost signal, and final outcome, each tied to a shared work ID or case ID the way business process management has always tracked units of work.

Agents also need a lifecycle, not just a prompt. In the talk I framed it as intent, spec, evidence, suggest, approval, validation: the agent proposes, a human approves, and the outcome is validated against agreed metrics like cost delta, p95 latency, and queue wait. Separate the stages. Observe-only agents summarize and recommend. Review-required agents draft the ticket or runbook. Nothing touches production without passing the gate.

BPM has always been about more than boxes and arrows. At its best, it defines the unit of work, tracks state, assigns ownership, enforces policy, and measures outcomes. Those ideas map directly to agentic AI.

## Where the work record lives

The work record would come from whatever owns the unit of work: Step Functions, EventBridge, Bedrock Agents or a custom agent runtime, approval systems, CloudTrail and CloudWatch, cost allocation data, or whatever already owns the workflow. Everyone's needs and systems are different.

The key is that each event is published against a shared `work_id` or `case_id`: agent step, tool call, policy check, human approval, correction, escalation, cost signal, final outcome, and so on.

Those events can land in S3 and Iceberg or other operational stores. I think of it as a simple division of labor:

- orchestration creates the work record
- events carry it
- the lake stores it
- Redshift makes it analyzable and explainable

That does not mean every agent workload belongs in a warehouse. It does not. But it does mean the analytical foundation matters.

If agents are going to operate across business processes, enterprises will need to analyze agent behavior the same way they analyze customer behavior, supply chain behavior, financial performance, and operational risk.

That requires performance, governance, interoperability, cost discipline, and a platform that fits how enterprises already operate.

This is where Redshift's role gets more interesting. Not as the agent runtime. Not as the orchestration layer. But as part of the governed analytical foundation for understanding what agents are doing, what value they are creating, and what risk they are introducing.

The next wave of enterprise AI will not just be about whether agents can complete tasks. It will be about whether organizations can understand, govern, optimize, and trust the work those agents perform.

That starts with the work record. And the next time a number changes at 2 AM, someone should be able to answer why.

*Next: {% include series-link.html slug="cheaper-models-make-the-data-platform-matter-more" label="Part 3 — Cheaper Models Make the Data Platform Matter More" date="October 16" %}, on the team that rebuilt everything around the model of the month.*

## References

- Gary Samuelson, [AI Agents Need a Work Record. BPM Has Had One for 25 Years.](https://garysamuelson.github.io/agentic/work-record-is-bpm-task/)
- [Amazon S3 Tables now support all Apache Iceberg V3 data types](https://aws.amazon.com/blogs/aws/amazon-s3-tables-now-support-all-apache-iceberg-v3-data-types/)
- [Redshift data sharing](https://docs.aws.amazon.com/redshift/latest/dg/datashare-overview.html)
- [Amazon Redshift RG features](https://aws.amazon.com/redshift/features/rg/)
