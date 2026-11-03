# Architecture

## Overview

Document Intelligence is a document-processing platform designed to turn uploaded files into structured, searchable and usable information.

Documents are stored and processed through a pipeline that can extract text, metadata, classifications and summaries before making the processed content available for search and further analysis.

Processing runs outside the HTTP request path so that large or expensive document operations do not block API requests. As processing requirements grow, workloads can be distributed across multiple workers and independent processing steps can run in parallel.

The system starts as a modular .NET application and is designed to evolve gradually towards distributed processing with RabbitMQ, Python-based AI workers and FlowForge-based workflow orchestration.

## Repository Structure

```
DocumentIntelligence/
├── docs/
│   ├── architecture.md
│   ├── decisions.md
│   └── development/
│       ├── 001-project-foundation.md
│       └── CHANGELOG.md
│
├── src/
│   ├── DocumentIntelligence.Api
│   ├── DocumentIntelligence.Application
│   ├── DocumentIntelligence.Data
│   ├── DocumentIntelligence.Interface
│   ├── DocumentIntelligence.Models
│   └── DocumentIntelligence.Worker
│
├── tests/
│   ├── DocumentIntelligence.UnitTests
│   └── DocumentIntelligence.IntegrationTests
│
└── DocumentIntelligence.slnx
```

## Project Responsibilities

### DocumentIntelligence.Api

Contains controllers, API configuration and HTTP-specific behaviour.

Controllers remain thin and delegate application behaviour to services.

### DocumentIntelligence.Application

Contains application services, use-case orchestration and business logic.

### DocumentIntelligence.Data

Contains EF Core, repositories, Unit of Work implementations and persistence logic.

### DocumentIntelligence.Interface

Contains shared interfaces and abstractions used across projects.

Examples may include:

- `IDocumentService`
- `IRepository<T>`
- `IUnitOfWork`
- `IFileStorage`
- `IDocumentProcessor`
- `ITextExtractor`

Interfaces are added only when a concrete implementation requires them.

### DocumentIntelligence.Models

Contains entities, requests, responses, enums and shared model types.

### DocumentIntelligence.Worker

Handles document-processing workloads outside the API request lifecycle.

The worker will later support distributed and horizontally scalable processing.

## Dependency Direction

```
Api
 ├── Application
 ├── Interface
 └── Models

Application
 ├── Interface
 └── Models

Data
 ├── Interface
 └── Models

Worker
 ├── Application
 ├── Interface
 └── Models

Interface
 └── Models

Models
 └── no project dependencies
```

The dependency direction keeps business and shared abstractions independent from infrastructure concerns.

## Processing Direction

The document-processing pipeline will evolve towards:

```
Upload
  ↓
Store Document
  ↓
Extract Text
  ↓
  ├── Extract Metadata
  ├── Classify Document
  └── Generate Summary
          ↓
       Index Document
```

Independent processing steps can later run in parallel.

## Scaling Direction

Document processing will be separated from HTTP request handling.

The architecture is expected to evolve towards:

```
ASP.NET Core API
       |
       v
    RabbitMQ
       |
       v
Processing Workers
```

Multiple worker instances can consume work concurrently, allowing processing capacity to scale independently from the API.

AI workloads can later run in separate Python workers where Python provides a clear advantage.

## FlowForge Integration

FlowForge will be introduced when document processing requires workflow dependencies, parallel execution, retries and recovery across multiple processing steps.

Document Intelligence remains responsible for document-specific data and business rules.

FlowForge remains responsible for workflow execution and coordination.

## Planned Evolution

The architecture may later include:

- RabbitMQ for durable asynchronous communication
- Multiple worker instances for horizontal scaling
- Python workers for AI and NLP workloads
- FlowForge for workflow orchestration
- Object storage for document binaries
- Dedicated search infrastructure
- Distributed tracing and metrics