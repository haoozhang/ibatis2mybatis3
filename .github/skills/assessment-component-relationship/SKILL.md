---
name: assessment-component-relationship
description: Generate component relationship diagram from project analysis
---

# Assessment Component Relationship

This skill generates a component relationship diagram by analyzing the internal structure and interactions within the application.

## When to Use This Skill

Use this skill when you need to:

- Understand the internal module and component structure of the application
- Visualize how services, controllers, and repositories interact
- Map data flow between internal components
- Identify tightly coupled or loosely coupled areas of the codebase
- Share component architecture understanding with stakeholders before modernization

## What This Skill Does

This skill creates a visual component relationship diagram by analyzing the source code:

1. **Analyze Project Modules**:
   - Scan source code directory structure for modules and packages
   - Identify key component types by framework conventions:
     - Java (Spring): Controllers, Services, Repositories, Configurations, Entities, DTOs, Listeners, Filters
     - Java (Jakarta EE): Servlets, EJBs, CDI Beans, JPA Entities, JAX-RS Resources
     - .NET (ASP.NET Core): Controllers, Services, Middleware, DbContext, Entities, Hubs, Filters
     - .NET (Blazor/MVC): Pages, Components, ViewModels
   - Identify data models and entity classes
   - Detect configuration and startup classes

2. **Map Component Interactions**:
   - Trace dependency injection patterns:
     - Constructor injection references
     - Field/property injection annotations
   - Identify communication patterns:
     - REST endpoint calls between components
     - gRPC service definitions and clients
     - Message queue producers and consumers
     - Event publishers and subscribers
   - Map data access patterns:
     - Which services access which repositories
     - Database context usage and entity relationships
   - Detect cross-cutting concerns:
     - Middleware/filter chains
     - Interceptors and aspects
     - Error handling pipelines

3. **Generate Diagram**:
   - Create a Mermaid flowchart or C4 component diagram
   - Organize components by architectural layer (Presentation, Business Logic, Data Access, Infrastructure)
   - Show interaction arrows with brief labels describing the relationship
   - Group related components within subgraphs
   - **Important**: Follow GitHub-compatible Mermaid syntax:
     - Avoid special characters (@, #, $, etc.) in node labels and arrow labels
     - Use plain text descriptions instead
     - Test rendering compatibility with GitHub
   - Save to `.github/modernize/assessment-component-relationship.md`

## Input Parameters

- `workspace-path` (optional): Path to the project to analyze (defaults to current directory)

## How to Use

### Prerequisites

- No special prerequisites required
- Works with Java and .NET projects only
- Best used after running `assessment` skill to understand the context

### Triggering Component Relationship Generation

Simply express the intent to visualize component relationships. Example prompts:

- "Generate component relationship diagram"
- "Show me how the internal components interact"
- "Create a visual map of service interactions"
- "Visualize the component architecture"

The component relationship generation process automatically:
- Scans source code for component classes and interfaces
- Identifies framework conventions (controllers, services, repositories, etc.)
- Traces dependency injection and communication patterns
- Creates a Mermaid diagram showing:
  - Components grouped by architectural layer
  - Interaction arrows between components
  - Data flow direction
  - Key interfaces and contracts
- Saves diagram to `.github/modernize/assessment-component-relationship.md`

## Diagram Output

The generated diagram will be saved to:

- `.github/modernize/assessment-component-relationship.md`

The diagram includes:
- **Architectural Layers**: Presentation, Business Logic, Data Access, Infrastructure
- **Components**: Controllers, services, repositories, and other key classes
- **Interactions**: Arrows showing which components call or depend on which
- **Data Flow**: Direction of data movement through the system
- **Grouping**: Related components organized within subgraphs

## Diagram Style

The diagram is intentionally focused on component-level relationships:

Include:
- Key components grouped by architectural layer
- Service-to-service interactions
- Controller-to-service-to-repository chains
- Cross-cutting concerns (middleware, filters, interceptors)
- Data flow direction between components

Exclude:
- Individual method signatures or method-level calls
- Low-level implementation details within a component
- External dependencies (covered by assessment-dependency-map skill)
- Migration directions (covered in separate planning)
- Private helper classes or utilities

## Success Criteria

Component relationship generation is complete when:
- Source code structure analyzed successfully
- Key components and their types identified
- Interactions between components mapped
- Mermaid diagram generated with clear, readable structure
- Diagram saved to `.github/modernize/assessment-component-relationship.md`
- Diagram is viewable in markdown preview

## Error Handling

**Unsupported Project Type**:
- Only supports Java and .NET projects
- Provide clear message for unsupported project types

**No Source Code Found**:
- If no recognized source files exist, report what was searched for
- Suggest checking the workspace path

**Minimal Component Structure**:
- For simple projects with few components, generate a basic diagram
- Indicate that the project has a simple structure
- Still show what components exist and their relationships

For any failure, provide clear error messages and next steps.
