---
title: "Core application - Temporal feature | Temporal Platform Documentation"
source: "https://docs.temporal.io/evaluate/development-production-features/core-application"
author:
published:
created: 2026-05-03
description: "Discover Temporal's Workflow, Activity, and Worker framework; orchestrate steps, encapsulate business logic, and execute code efficiently using the Temporal SDK in your favorite language."
tags:
  - "raw"
---
**Workflows**, **Activities**, and **Workers** form the core parts of a Temporal Application.

**Workflows**: A Workflow defines the overall flow of the application. You write it in your programming language of choice using the Temporal SDK. Conceptually, a Workflow specifies a sequence of steps and orchestrates the execution of Activities.

**Activities**: An Activity is a method or function that encapsulates business logic prone to failure (e.g., calling a service that may go down). The system can automatically retry these Activities upon some failures. Activities perform a single, well-defined action, such as calling another service, transcoding a media file, or sending an email message.

**Workers**: A Worker executes your Workflow and Activity code.

**Follow one of our tutorials to [Get started](https://learn.temporal.io/getting_started/) learning how to develop Workflows and Activities and run them in Worker Processes.**

Or jump straight to a Temporal SDK feature guide:

For a deep dive into Temporal Workflows, Activities, and Workers, visit the following Temporal Encyclopedia pages or enroll in one of [our courses](https://learn.temporal.io/courses/).