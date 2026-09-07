# Agentforce System Readiness Assessment

## Overview
A 7-dimension scoring rubric and facilitation guide for assessing whether a Salesforce org is technically and organizationally ready to deploy Agentforce, before any Topics or Actions get scoped.

## The Problem It Solves
Most Agentforce rollouts get scoped and built before anyone checks whether the underlying org can actually support an agent safely - clean data, a scoped permission model, a licensed and configured Data Cloud/Einstein Trust Layer, and a business process specific enough to hand to an agent in the first place. This assessment surfaces those gaps up front, as a pre-flight check rather than a post-launch incident report.

## How to Use This
This isn't a deployable package - it's a strategist-track diagnostic tool. Open [`agentforce-system-readiness-assessment.md`](./agentforce-system-readiness-assessment.md) for:
- A facilitation guide with discovery questions across 7 readiness dimensions (data quality, Data Cloud/Einstein Trust Layer, permissions, automation, Topic/Action design maturity, governance, and change management)
- A 1-5 scoring rubric per dimension, with hard-blocker red flags called out separately from the numeric score
- Readiness bands (Not Ready / Emerging / Ready / Optimized) with recommended next steps for each
- An appendix on estimating Data Cloud and Agentforce consumption costs, so the assessment connects architecture readiness to budget conversations

Run it as a structured discovery session with the org's admin/architect, a Data Cloud contact if one exists, and the business process owner - then score and read the band before scoping any Topics or Actions.

## About the Creator
Built by [@shonnah-dev](https://github.com/shonnah-dev) as part of the Trailblazer Labs Builder in Residence Cohort - Strategist track.
