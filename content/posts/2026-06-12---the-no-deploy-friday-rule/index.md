---
title: "The No-Deploy-Friday Rule"
date: "2026-06-12 10:00"
template: post
draft: false
slug: "/posts/the-no-deploy-friday-rule"
category: Engineering Culture
tags:
  - engineering-culture
  - devops
  - reliability
description: |
  Most teams I have worked with have some version of a no-deploy-Friday rule. I want to write about what I think it is actually covering up.
---

Some teams I have worked with have some version of a no-deploy-Friday rule. Sometimes it is written down. Usually it is just understood -- nobody announces it, everyone knows.

I get where it comes from. A deployment goes wrong late on a Friday, someone spends the weekend dealing with it, a rule gets made. That is a reasonable short-term response.

But in my experience, most teams keep the rule long after they should have fixed the underlying problem.

Full disclosure: Regardless which role I play, I'm just someone who has worked across a few different stacks and pays attention to patterns. Take what resonates.

## The Elasticsearch sharding optimisation

One team I worked with had a React frontend on Elastic Beanstalk backed by an Elasticsearch cluster. The deployment process felt, in everyone's gut feeling, unreliable. Nobody could articulate why exactly. It just felt risky.

When things degraded, latency creeping up, search getting slow, the response was always the same: restart some nodes, bump the task count, give it more RAM. Vertical scaling as a substitute for investigation.

I am not an Elasticsearch expert. But I read the documentation, formed a hypothesis about shard allocation, tested it, watched the telemetry. The re-sharding held. It was not complicated in hindsight -- it just required treating the infrastructure as something you could reason about rather than something you managed by feel.

The team clearly had the ability to do that kind of work. They just were not applying it to the deployment process, because deploying had become an emotional thing rather than a technical one. The Friday rule was one symptom of that.

## The DLQ thing

Something I keep noticing in teams running event-driven systems. The DLQ gets set up as a safety net, which is correct. Then it starts filling up regularly and clearing it becomes just another Monday task. The question of why the main execution path keeps failing stops getting asked.

What I find odd is that these same teams usually do not have error rate alerts on the actual processing function. They are monitoring the dead-letter queue but not the function where things fail. The DLQ is showing you something is wrong. The function tells you what.

There is a discipline for thinking about this more rigorously: SRE, SLOs, error budgets. A lot of teams are put off by the terminology. But the core idea is not complicated. An error budget is just a way of deciding when to slow down based on actual data from your system, rather than the day of the week. Worth understanding even if you decide not to adopt the full framework.

## An incident

An automated update came through, touched something interacting with an old workaround in the dependency config -- put in place months ago to mitigate a vulnerability and never removed because nobody filed a follow-up ticket. CI was green. Runtime dependency resolution broke. Someone got paged.

The fix was not complicated: remove the workaround, update the dependency to a version that included the fix natively, verify the resolution tree was clean. An afternoon.

The conversation afterwards was mostly about whether the deployment should have happened on a Friday. I found that frustrating. The workaround is still in your config on Monday. If another automated update comes through next week, the same thing happens. Rescheduling does not fix the root cause.

## What I actually think

The teams I have seen deploy confidently on any day are not doing anything heroic. They have automated rollbacks. They deploy small changes often. They have monitoring on the actual execution path, not just the recovery mechanism.

I am not saying every team can get there quickly. It takes investment and a team environment where that work gets prioritised, which is its own challenge. But I think it is worth being honest about what the Friday rule is. It reduces the chance of a bad weekend. It does not reduce the chance of a bad deployment.

The goal is a pipeline reliable enough that the calendar stops being relevant. The rule is a workaround for not having that yet.
