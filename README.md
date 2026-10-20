> ### Confidentiality Notice
>
> This repository contains an anonymized portfolio case study based on a real-world project.
>
> Company names, client information, URLs, credentials, proprietary business logic, and sensitive implementation details have been removed or modified to protect confidentiality.
>
> Any code snippets included are examples only and do not represent any protected production codebase.

---

# Pipeline CI/CD — Environment-Aware Build & E2E Quality Gate Evolution

**Alset | Azure DevOps · YAML · Cypress · JavaScript · Vite · Azure Static Web Apps**

## Project Overview

This project documents the evolution of an existing CI/CD pipeline into a more controlled and maintainable delivery workflow, with Cypress E2E testing integrated as a quality gate.

The work progressed through three main stages:

1. **Initial integration:** established automated Cypress validation within the existing Build → Test → Deploy process.
2. **Environment-aware validation:** aligned application builds, Cypress configuration, and deployment targets with the target branch.
3. **Reusable test execution:** separated Cypress execution logic into a reusable YAML template, enabling both integrated pipeline validation and independent regression runs.

As a QA Engineer, I took the initiative to extend my work beyond test automation by learning how the pipeline stages interact and applying that knowledge to improve the team's testing and delivery process.

## My Contribution

- Implemented the initial Cypress E2E integration into Azure DevOps.
- Developed and refined YAML pipeline stages, dependencies, and execution conditions.
- Integrated artifact-based testing to validate the application built by the pipeline.
- Aligned DEV and PROD build configurations, test settings, and deployment targets.
- Refactored Cypress execution into a reusable YAML template.
- Enabled integrated validation and independent regression execution.
- Configured environment variables and deployment settings.
- Integrated Mochawesome and JUnit reporting, including test-result publication in Azure DevOps.
- Preserved reports and failure screenshots as pipeline artifacts.
- Established test outcomes as a deployment control, preventing failed validation from progressing through the normal delivery flow.

My contribution focused on the QA automation and pipeline integration layer, rather than replacing dedicated DevOps or infrastructure architecture responsibilities.

## Technology Stack

- **CI/CD:** Azure DevOps Pipelines, YAML
- **Test automation:** Cypress, JavaScript
- **Application build:** Node.js, Vite
- **Deployment:** Azure Static Web Apps
- **Reporting:** Mochawesome, JUnit, Azure DevOps Test Results
- **Version control:** Git, GitHub

## Pipeline Evolution

### Stage 1 — Initial Cypress Integration

The existing pipeline did not include automated Cypress validation. The initial implementation introduced a testing stage between the application build and deployment.

```text
Build DEV
    ↓
Publish application artifact
    ↓
Serve artifact locally
    ↓
Run Cypress E2E tests
    ↓
Tests pass?
    ├── No  → Stop delivery and preserve evidence
    └── Yes → Continue deployment flow
```

The pipeline generated a Development build for initial validation. For changes targeting `main`, the Production build was generated after successful Cypress validation.

This established the initial **Build → Test → Deploy** workflow and introduced automated E2E testing as a pre-deployment quality gate.

### Stage 2 — Environment-Aware Build & Validation

The initial design had a limitation: Production deployments were validated against the Development build and its configuration before the Production artifact was generated.

The pipeline was refined to align the build, Cypress configuration, and deployment target with the target branch.

| Target branch | Application build | Cypress configuration | Deployment target |
|---|---|---|---|
| `dev` | Development | DEV | DEV |
| `main` | Production | PROD | PROD |

The resulting delivery flow became:

```text
Target branch
    ↓
Build for the target environment
    ↓
Publish application artifact
    ↓
Run Cypress against that build
    ↓
Publish test results
    ↓
Validation passed?
    ├── No  → Block deployment
    └── Yes → Deploy to the matching environment
```

The build artifact is served locally during Cypress execution. This allows the tests to validate the application produced by the current pipeline instead of an independently generated local build.

Environment-specific credentials and API settings are supplied through pipeline variables rather than hardcoded into the shared test logic.

### Stage 3 — Reusable Cypress Execution

As the pipeline evolved, Cypress execution logic was separated from the main delivery pipeline into a reusable YAML template.

This reduced duplication and made the testing process easier to maintain and reuse across different execution scenarios.

```text
Main CI/CD pipeline
    ↓
Build application
    ↓
Invoke reusable Cypress template
    ↓
Validate local build artifact
    ↓
Publish results
    ↓
Deploy if validation succeeds
```

The template supports two execution modes:

| Mode | Purpose |
|---|---|
| Integrated validation | Tests the application artifact generated during the current pipeline run. |
| Independent regression | Runs Cypress against an already deployed environment without rebuilding the application. |

A separate pipeline can invoke the same testing logic for an independent regression run. This provides a way to validate a deployed environment without executing the entire build-and-deploy workflow.

The key architectural improvement was separating **how Cypress tests are executed** from **when and why the pipeline invokes them**.

## Artifact-Based Validation

The pipeline transfers the application between stages through published artifacts.

```text
Vite build
    ↓
vite-dist artifact
    ↓
Cypress stage downloads artifact
    ↓
Application served locally
    ↓
Cypress validates the built application
```

This creates a clear relationship between the build and its test results. The test stage validates the artifact produced by the pipeline, while the deployment stage uses the appropriate validated build for the target environment.

## Reporting & Quality Gate

The pipeline integrates multiple reporting mechanisms to make automated test executions easier to investigate.

- **Mochawesome HTML and JSON reports:** detailed execution results.
- **JUnit results:** structured test outcomes published to Azure DevOps.
- **Azure DevOps Tests tab:** test execution metrics, failures, and individual results.
- **Pipeline artifacts:** retained reports and failure screenshots for investigation.

The test stage is configured to fail when Cypress validation fails. Deployment stages depend on successful completion of the required validation stages and their applicable branch conditions.

The intended failure-handling flow is:

```text
Cypress execution
    ↓
Test failure
    ↓
Publish available results and evidence
    ↓
Pipeline remains failed
    ↓
Subsequent deployment is blocked
```

Publishing evidence independently of the test outcome allows failures to be investigated without losing the available execution reports.

## Key Technical Decisions

| Decision | Reason |
|---|---|
| Integrate Cypress before deployment | Make automated E2E validation part of software delivery. |
| Test a built application artifact | Validate the application produced by the pipeline. |
| Align builds and Cypress settings with the target environment | Avoid validating PROD delivery using only DEV configuration. |
| Use branch-based pipeline conditions | Control environment-specific build and deployment behavior. |
| Extract Cypress execution into a reusable template | Reduce duplication and improve maintainability. |
| Support independent regression execution | Validate deployed environments without requiring a new application build. |
| Publish JUnit results and execution evidence | Improve failure visibility and investigation. |
| Make successful validation a deployment prerequisite | Prevent failed E2E validation from progressing through the normal delivery workflow. |

## Results

The evolution established a more structured relationship between QA automation and application delivery.

- Introduced Cypress E2E validation into an existing CI/CD process.
- Established a Build → Test → Deploy workflow with automated quality gates.
- Improved alignment between application builds, Cypress configuration, and DEV/PROD deployment targets.
- Added artifact-based validation and environment-specific pipeline configuration.
- Integrated Mochawesome reports, JUnit publication, and failure evidence.
- Refactored Cypress execution into a reusable YAML template.
- Enabled integrated testing and independent regression execution through shared test logic.
- Improved visibility into automated test results within Azure DevOps.

## Skills Demonstrated

- Cypress E2E automation and CI/CD integration.
- Azure DevOps YAML pipelines and stage dependencies.
- Application artifact management and validation.
- Environment-specific test configuration.
- Reusable pipeline templates and execution modes.
- JUnit and Mochawesome reporting.
- Quality gate implementation and deployment control.
- Root-cause investigation using automated test evidence.
- Cross-functional problem-solving beyond traditional manual QA activities.

## Conclusion

This project demonstrates how I expanded my QA automation work into CI/CD integration, starting with an initial Cypress testing stage and progressively improving the pipeline's environment awareness, reusability, and reporting.

Rather than treating automated tests as a separate activity, I worked to make their results an integrated part of the delivery process—helping the team validate application builds, investigate failures, and control when changes could proceed toward deployment.

---

<div style="text-align:center; color:#888; font-size:10px;">


<div style="text-align:center; color:#888; font-size:10px;">
