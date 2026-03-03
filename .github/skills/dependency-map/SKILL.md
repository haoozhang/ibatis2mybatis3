---
name: dependency-map
description: Generate dependency map from project analysis
---

# Assessment Dependency Map

This skill generates a dependency map diagram based on the project's declared and transitive dependencies.

## When to Use This Skill

Use this skill when you need to:

- Visualize the project's dependency structure
- Understand which libraries and frameworks the application relies on
- Identify transitive dependency chains and potential version conflicts
- Get a clear overview of external dependencies grouped by functional category
- Share dependency understanding with stakeholders before modernization

## What This Skill Does

This skill creates a visual dependency map by analyzing the project's build and configuration files:

1. **Analyze Dependency Declarations**:
   - Examine build and package files:
     - Java: pom.xml, build.gradle, settings.gradle, gradle.properties
     - .NET: *.csproj, Directory.Build.props, packages.config, Directory.Packages.props
   - Extract for each dependency:
     - Group/package name and artifact name
     - Declared version (or version range)
     - Scope (compile, runtime, test, provided)
   - Identify transitive dependencies where possible (e.g., from dependency tree output, BOM imports, or lock files)

2. **Map Dependency Relationships**:
   - Group dependencies by functional category:
     - Web frameworks (e.g., Spring Web, ASP.NET Core MVC)
     - Database drivers and ORMs (e.g., Hibernate, Entity Framework)
     - Messaging libraries (e.g., Kafka client, RabbitMQ)
     - Caching (e.g., Redis, EhCache)
     - Logging (e.g., SLF4J, Serilog)
     - Security and authentication (e.g., Spring Security, Microsoft.Identity)
     - Testing (e.g., JUnit, xUnit)
     - Utilities and others
   - Identify version conflicts or outdated versions where obvious
   - Detect parent POM / BOM imports (Java) or central package management (.NET)

3. **Generate Diagram**:
   - Create a Mermaid flowchart diagram
   - Show the application at the center with dependency groups radiating outward
   - Include version information on key dependencies
   - Show transitive relationships where they are significant
   - **Important**: Follow GitHub-compatible Mermaid syntax:
     - Avoid special characters (@, #, $, etc.) in node labels and arrow labels
     - Use plain text descriptions instead
     - Test rendering compatibility with GitHub
   - Save to `.github/modernize/dependency-map.md`

## Input Parameters

- `workspace-path` (optional): Path to the project to analyze (defaults to current directory)

## How to Use

### Prerequisites

- No special prerequisites required
- Works with Java and .NET projects only
- Best used after running `assessment` skill to understand the context

### Triggering Dependency Map Generation

Simply express the intent to visualize dependencies. Example prompts:

- "Generate dependency map from the project"
- "Show me the project dependencies"
- "Create a visual map of all dependencies"
- "Visualize the dependency structure"

The dependency map generation process automatically:
- Analyzes build files (pom.xml, build.gradle, *.csproj, etc.)
- Extracts direct and transitive dependencies with versions
- Groups dependencies by functional category
- Creates a Mermaid diagram showing:
  - Application as central node
  - Dependency groups with key libraries and versions
  - Transitive dependency chains where significant
- Saves diagram to `.github/modernize/dependency-map.md`

## Diagram Output

The generated diagram will be saved to:

- `.github/modernize/dependency-map.md`

The diagram includes:
- **Application Node**: The project being analyzed
- **Dependency Groups**: Functional categories (web, database, messaging, etc.)
- **Key Libraries**: Major libraries with version numbers
- **Transitive Chains**: Important transitive dependency relationships
- **Version Info**: Version numbers for key dependencies

## Diagram Style

The diagram is intentionally focused on dependency structure:

Include:
- Direct dependencies grouped by category
- Version numbers for key dependencies
- Transitive dependencies where significant
- Parent POM / BOM / central package management relationships

Exclude:
- Individual class-level usage of dependencies
- Runtime behavior or call patterns
- Migration directions (covered in separate planning)
- Test-only dependencies (unless specifically relevant)

## Success Criteria

Dependency map generation is complete when:
- Build and package files analyzed successfully
- Dependencies extracted with versions and categories
- Mermaid diagram generated with clear, readable structure
- Diagram saved to `.github/modernize/dependency-map.md`
- Diagram is viewable in markdown preview

## Error Handling

**Unsupported Project Type**:
- Only supports Java and .NET projects
- Provide clear message for unsupported project types

**No Build Files Found**:
- If no recognized build files exist, report what was searched for
- Suggest checking the workspace path

**Incomplete Dependency Information**:
- Generate a diagram from available data
- Indicate which information is missing or could not be resolved

For any failure, provide clear error messages and next steps.
