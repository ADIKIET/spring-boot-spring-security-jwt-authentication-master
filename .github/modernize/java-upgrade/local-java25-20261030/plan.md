# Upgrade Plan: spring-boot-security-jwt (local-java25-20261030)

- **Generated**: 2026-10-30 16:15:01 +05:30
- **HEAD Branch**: feature/java-25-upgrade
- **HEAD Commit ID**: f8a1787

- **Status**: Accepted by the user on 2026-10-30; implementation completed locally.

> The Copilot modernization model (`agent-host-copilotcli:auto`) was unavailable during plan generation, so no extension session ID or telemetry was created. The user has accepted this plan and subsequently requested that the branch and changes be pushed. A local commit will be created before pushing.

## Available Tools

**JDKs**
- JDK 25.0.4.1: `C:\softwares\jdk-25.0.4.1` (target JDK, already installed)
- JDK 17: not available (base-JDK baseline step skipped; a supplemental Java 25 pre-change build/test run passed)

**Build Tools**
- Maven 3.10.0: `C:\softwares\apache-maven-3.10.0` (used for validation)
- Maven Wrapper: 3.6.0 → 3.10.0 (update wrapper to the installed Maven version; Maven 3.6.0 is obsolete for this Java 25 target)

## Guidelines

- Preserve the requested scope: Java 25 and only necessary compatibility updates.
- Work on the already-created `feature/java-25-upgrade` branch.
- Commit the completed changes and push the feature branch when GitHub CLI authentication is available.

> Note: You can add any specific guidelines or constraints for the upgrade process here if needed, bullet points are preferred.

## Options

- Working branch: feature/java-25-upgrade (provided by user; already created)
- Run tests before and after the upgrade: true

## Upgrade Goals

- Java 25

## Technology Stack

| Technology/Dependency | Current | Min Compatible Version | Why Incompatible |
| --------------------- | ------- | ---------------------- | ---------------- |
| Java | 17 | 25 | User requested Java 25 runtime and compilation target |
| Spring Boot parent | 3.1.0 | 3.5.16 | 3.5.16 is the latest stable 3.5 maintenance release in Maven metadata and provides a supported Java 25-compatible Spring Framework line while retaining the Boot 3.x application model |
| Spring Framework (Boot-managed) | Boot 3.1.0 managed | 6.2.19 (via Boot 3.5.16) | Assessment identified Spring Framework-related Maven compatibility incidents; let the upgraded Boot parent manage the compatible framework version |
| Maven Wrapper | 3.6.0 | 3.10.0 | The project wrapper predates the Java 25 target; use current installed Maven 3.10.0 |
| Logback Core | 1.5.11 (explicit) | 1.5.34 | Preserve the explicit override because it is newer than the Boot 3.1.0-managed version; align it to the Boot 3.5.16-managed version |
| Logback Classic | 1.5.6 (explicit) | 1.5.34 | Preserve the explicit override because it is newer than the Boot 3.1.0-managed version; align it to the Boot 3.5.16-managed version |
| Spring Security | Boot 3.1.0 managed | 6.5.11 (via Boot 3.5.16) | Upgrade transitively with Boot; existing SecurityFilterChain API is compatible |
| JJWT | 0.11.5 | 0.11.5 | No Java 25-specific change identified |

## Derived Upgrades

- Spring Boot 3.1.0 → 3.5.16: required to put the application on the Java 25-compatible Spring Framework/Security maintenance line while avoiding an unrelated Boot 4 migration.
- Maven Wrapper 3.6.0 → 3.10.0: align the wrapper with the available Maven toolchain for the Java 25 target.
- Align explicit Logback overrides to Boot 3.5.16's managed 1.5.34: retain the existing explicit overrides (which are newer than the old BOM-managed version) while avoiding stale and mismatched versions.

## Impact Analysis

### Dependency Changes

| File | Dependency | Current | Action | Target | Reason |
|------|------------|---------|--------|--------|--------|
| pom.xml | spring-boot-starter-parent | 3.1.0 | upgrade | 3.5.16 | Compatible Java 25-capable Spring line; maintain Boot 3.x scope |
| pom.xml | java.version | 17 | upgrade | 25 | User-requested compilation/runtime target |
| pom.xml | logback-core | 1.5.11 | upgrade | 1.5.34 | Keep the explicit version override, update to Boot 3.5.16's managed secure line |
| pom.xml | logback-classic | 1.5.6 | upgrade | 1.5.34 | Keep the explicit version override, align to Boot 3.5.16's managed version |
| .mvn/wrapper/maven-wrapper.properties | Apache Maven distribution | 3.6.0 | upgrade | 3.10.0 | Project wrapper version alignment for Java 25 |

### Source Code Changes

No source changes identified. Existing configuration uses `SecurityFilterChain` and `authorizeHttpRequests`; targeted searches found no internal JDK APIs, reflective access to `java.base`, or `javax.*` imports.

### Configuration Changes

No application configuration changes identified.

### CI/CD Changes

No CI/CD files were found in the repository. The README now documents the Java 25 minimum requirement.

### Risks & Warnings

- **Spring Boot dependency-line upgrade**: Boot 3.1 to 3.5 changes managed Spring Framework, Spring Security, Hibernate and related libraries. The application uses the modern security filter-chain API and targeted source/config searches found no known migration blocker. **Mitigation**: compile all sources and run the complete test suite after the aligned parent/dependency update.
- **Unavailable extension CVE scan**: the modernization tools could not run because their configured model is unavailable. **Mitigation**: use the target BOM's managed dependency versions, preserve and align the explicit Logback overrides, run Maven dependency resolution, and report that an extension CVE scan was unavailable.
- **Baseline JDK mismatch**: JDK 17 is not installed; the available JDK is 25. **Mitigation**: skip the exact base-JDK baseline per workflow; a supplemental clean build and test run on Java 25 before edits succeeded.

## Upgrade Steps

- Step 1: Setup Environment
  - **Rationale**: Confirm the target JDK and Maven toolchain before changing project files.
  - **Changes to Make**: No installation is needed; JDK 25.0.4.1 and Maven 3.10.0 are already available.
  - **Verification**: `java -version` and `mvn -version`, using `C:\softwares\jdk-25.0.4.1` and `C:\softwares\apache-maven-3.10.0`; expect JDK 25 and Maven 3.10.0.
- Step 2: Setup Baseline
  - **Rationale**: Establish a comparable pre-upgrade result; the exact Java 17 baseline is unavailable.
  - **Changes to Make**: None; skip the Java 17 baseline. A supplemental pre-change Java 25 clean compile and test already passed.
  - **Verification**: Base-JDK baseline skipped; supplemental command `mvn clean test-compile -q` followed by `mvn clean test -q` on Java 25, both successful.
- Step 3: Upgrade Java 25 and Compatible Spring/Maven Versions
  - **Rationale**: Update the compiler/runtime target and compatible framework/build versions together so the project remains buildable after this step.
  - **Changes to Make**: Apply all Dependency Changes above in `pom.xml` and `.mvn/wrapper/maven-wrapper.properties`.
  - **Verification**: `mvn clean test-compile -q` with JDK 25 and Maven 3.10.0; expect production and test compilation to succeed.
- Step 4: Validate Dependencies and CVEs
  - **Rationale**: Check the target dependency graph and resolve any reported upgrade-relevant vulnerability by updating the managed or direct version.
  - **Changes to Make**: Review the resolved dependency list; apply minimal version corrections only if a finding requires them.
  - **Verification**: `mvn dependency:list -DexcludeTransitive=true` and, if available, the Java dependency CVE validator; compile after any correction.
- Step 5: Final Validation
  - **Rationale**: Verify the requested Java 25 target and ensure the complete test suite passes.
  - **Changes to Make**: Address any compilation or test failures found; do not leave TODOs or temporary workarounds.
  - **Verification**: `mvn clean test-compile -q` and `mvn clean test -q`, using JDK 25 and Maven 3.10.0; expect success and all tests passing.
