# Document Intelligence

Document Intelligence is a scalable backend platform for processing unstructured documents into structured, searchable and usable information.

The platform supports document upload, storage, text extraction, metadata extraction, classification, summarisation, search, processing history and failure recovery.

Document processing runs outside the HTTP request lifecycle and is designed to scale through asynchronous messaging, distributed workers and parallel processing. FlowForge will later provide workflow orchestration for dependent processing steps, retries and recovery.

## Architecture

The platform starts as a modular .NET application with clear boundaries between API, application logic, persistence, shared abstractions, models and background processing.

```
DocumentIntelligence/
├── docs/
├── src/
│   ├── DocumentIntelligence.Api
│   ├── DocumentIntelligence.Application
│   ├── DocumentIntelligence.Data
│   ├── DocumentIntelligence.Interface
│   ├── DocumentIntelligence.Models
│   └── DocumentIntelligence.Worker
├── tests/
│   ├── DocumentIntelligence.UnitTests
│   └── DocumentIntelligence.IntegrationTests
└── DocumentIntelligence.slnx
```

## Processing Pipeline

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

Independent processing stages can later run in parallel and scale separately.

## Technology

- C#
- .NET 10
- ASP.NET Core
- Entity Framework Core
- SQL Server
- xUnit
- Testcontainers
- Docker
- RabbitMQ later
- Python / FastAPI for AI processing later
- FlowForge for workflow orchestration later
- OpenTelemetry
- GitHub Actions

## Documentation

Architecture decisions and development milestones are maintained in the [`docs`](./docs) directory.
