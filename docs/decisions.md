# Architecture Decisions

## .NET 10 and ASP.NET Core

.NET 10 and ASP.NET Core are used for the main backend application.

The platform requires APIs, background processing, dependency injection, persistence, testing and containerisation, all of which are supported well by the .NET ecosystem.

## Layered Project Structure

The solution is split into:

- Api
- Application
- Data
- Interface
- Models
- Worker

The structure keeps responsibilities clear while avoiding unnecessary microservices.

## Shared Interfaces

Shared abstractions are placed in `DocumentIntelligence.Interface`.

This allows application, data and worker projects to depend on common contracts without depending directly on concrete implementations.

Interfaces are introduced only when required by an actual implementation.

## Thin Controllers

Controllers handle HTTP-specific concerns only.

Business logic belongs in application services.

## Data Access

EF Core, repository implementations, Unit of Work implementations and persistence logic belong in `DocumentIntelligence.Data`.

## Shared Models

Entities, requests, responses, enums and shared model types belong in `DocumentIntelligence.Models`.

## File Storage

Document binaries are not stored directly in SQL Server.

Local file storage will be used initially. Object storage can replace it later without changing document business logic.

## Background Processing

Document processing runs outside the API request lifecycle.

This prevents expensive processing from blocking HTTP requests and provides a clear path towards independent worker scaling.

## Distributed Processing

RabbitMQ will be introduced when document processing requires durable messaging and multiple worker instances.

## AI Processing

Python will be introduced for AI and NLP workloads where it provides a clear technical advantage.

Initial AI functionality will focus on document summarisation and classification without adding unnecessary AI orchestration frameworks.

## FlowForge

FlowForge will be introduced when document processing becomes a multi-step workflow with dependencies, parallel execution, retries and partial failure recovery.

Document Intelligence will continue to own document data and business rules.

FlowForge will own workflow execution and coordination.

## Search

SQL Server will be used for initial document search.

A dedicated search engine will only be introduced when search requirements exceed what SQL Server can reasonably support.