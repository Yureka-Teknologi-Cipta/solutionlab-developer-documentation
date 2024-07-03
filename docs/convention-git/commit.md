---
sidebar_position: 3
---

# Commit

Our commit convention follows the Conventional Commits specification for clear and standardized commit messages. This convention ensures consistency and enables automated release notes generation and versioning.

## Types

-   `fix`: Indicates a commit that addresses a bug or fixes an issue in the codebase.

-   `feat`: Denotes a commit that introduces a new feature or functionality to the project.

-   `build`: Refers to commits related to build processes or build system changes.

-   `chore`: Represents commits related to tasks or maintenance that are not directly related to the production code, such as updating dependencies or refactoring configurations.

-   `ci`: Signifies commits related to continuous integration and deployment configurations or scripts.

-   `docs`: Describes commits that involve documentation changes, including updates to README files, inline comments, or documentation generation.

-   `style`: Reflects commits that exclusively address code styling or formatting changes, without altering the code's functionality.

-   `refactor`: Indicates commits that restructure existing code without changing its external behavior, often improving its readability, maintainability, or performance.

-   `perf`: Denotes commits that optimize the codebase's performance, typically by improving algorithms, data structures, or resource utilization.

-   `test`: Represents commits related to testing, including adding, modifying, or deleting test cases or test infrastructure.

## Sample

Commit message with description

```
feat: add invoice config to extend base configs
```

Commit message and ! to draw attention to breaking change

```
feat!: send an email to the customer when a product is shipped
```

Commit message with scope

```
feat(lang): add Indonesian language
```

Commit message with multi-paragraph body and multiple footers

```fix: prevent racing of requests

Introduce a request id and a reference to latest request. Dismiss
incoming responses other than from latest request.

Remove timeouts which were used to mitigate the racing issue but are
obsolete now.
```
