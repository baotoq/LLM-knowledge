---
title: "Workflow Execution Concurrency"
source: "https://docs.dapr.io/developing-applications/building-blocks/workflow/workflow-concurrency/"
author:
published:
created: 2026-05-03
description: "Configure concurrency for Dapr Workflows to rate limit workflow and activity executions."
tags:
  - "raw"
---
You can configure the maximum concurrent workflows and activities that can be executed at any one time with the following configuration. These limits are imposed on a *per* sidecar basis, meaning that if you have 10 replicas of your workflow app, the effective limit is 10 times the configured value.

Setting these limits can help prevent resource exhaustion on your Dapr sidecar and application, or to drain down a backlog of workflows if there had been a spike in activity causing resource contention. These limits do not distinguish between different workflow or activity definitions, so they apply to all workflows and activities running in the sidecar.

See the [Dapr Configuration documentation](https://docs.dapr.io/operations/configuration/configuration-overview/) for more information on how to apply configuration to your Dapr applications.

```yaml
apiVersion: dapr.io/v1alpha1
kind: Configuration
metadata:
  name: appconfig
spec:
  workflow:
    maxConcurrentWorkflowInvocations: 100 # Default is infinite
    maxConcurrentActivityInvocations: 1000 # Default is infinite
```

## Related links

- [Try out Dapr Workflows using the quickstart](https://docs.dapr.io/getting-started/quickstarts/workflow-quickstart/)
- [Workflow overview](https://docs.dapr.io/developing-applications/building-blocks/workflow/workflow-overview/)
- [Workflow API reference](https://docs.dapr.io/reference/api/workflow_api/)
- Try out the following examples:

Last modified April 24, 2026: [Add Reo.dev and cookie banner (#5147) (568d9aa)](https://github.com/dapr/docs/commit/568d9aad36dd12adf1a25e56f6ddddb2ad7c70a2)