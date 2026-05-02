---
title: "Dapr Go Service SDK"
source: "https://docs.dapr.io/developing-applications/sdks/go/go-service/"
author:
published:
created: 2026-05-02
description: "Go Service (Callback) SDK for building Dapr callback services"
tags:
  - "raw"
---

# Dapr Go SDK Service Documentation

## Overview

The Dapr Go SDK provides a service package for building callback services that integrate with Dapr. These services handle incoming requests from Dapr and other services.

In addition to the Dapr API client, Dapr Go SDK also provides a service package to bootstrap Dapr callback services.

## Service Types

Two service implementation approaches:

### HTTP Service

A callback service built using HTTP protocol for request handling.

### gRPC Service

A callback service implemented using gRPC for high-performance, typed communication.

## Purpose

Both options enable developers to create services that listen for and process requests routed through the Dapr sidecar, supporting patterns like service invocation, pub/sub callbacks, and binding triggers.

## Navigation

- HTTP Service: dedicated documentation for HTTP-based implementations
- gRPC Service: separate guidance for gRPC-based implementations
