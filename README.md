# MSOffsiteTDD

## UMA Advisor Portal — AI-Assisted TDD Workshop

A local workshop application for Morgan Stanley Wealth Management engineering leaders to experience Test-Driven Development (TDD), using Claude to help generate tests and implementation.

## Repository status

This README establishes the agreed scope and SET-01 implementation requirements. The application scaffold has not yet been delivered or verified. The directory structure, run configurations and commands below describe what SET-01 must provide; they become usable after the corresponding code is committed.

Repository: [MSOffsiteTDD](https://pscode.lioncloud.net/aasadlak/msoffsitetdd).

This README replaces the GitLab-generated introductory template with the project scope, delivery backlog and local development requirements.

## Workshop objective

Three groups of 3–4 participants, with no assumed TDD experience, will spend 40–60 minutes implementing one advisor insight each:

| Group | Insight | Business behavior to implement |
| --- | --- | --- |
| A | Earnings event today | Find accounts holding securities with an earnings announcement on the evaluation date. |
| B | Retirement within 90 days | Find qualifying customers and their associated accounts using approved date-window rules. |
| C | Accounts holding a given security | Find accounts holding a selected security and summarize their exposure. |

Each group clarifies requirements, designs its test scenarios, works through Red → Green, and refactors. Humans approve business rules and review generated changes. Claude challenges assumptions and assists with tests and code.

The starter application supplies the UI, data, APIs and extension points. The three insight rules and their tests remain workshop work.

## Portal scope

The portal represents a financial advisor managing a book of Unified Managed Accounts (UMA) across multiple customers and investment strategies.

- **Book summary:** illustrative aggregate account/customer counts, assets under management, cash and accounts requiring rebalance review.
- **Account sample:** approximately 12 detailed accounts across 6–8 customers, with strategies, holdings, restrictions, cash and activity.
- **Insights:** a prominent panel that starts empty and displays results once the workshop rules are implemented.
- **Account details:** selected-account holdings, customer profile, planned retirement, strategy and recent activity.

Book-level aggregates represent a larger book and are separate from the detailed sample. They must be labeled accordingly. The sample does not need to reconcile to the full-book totals; each detailed account's holdings and cash must reconcile to its portfolio value.

### Agreed layout

| Page area | Layout |
| --- | --- |
| Upper section | Left navigation / UMA book summary / Insights |
| Lower section | Left navigation / Accounts and account details spanning the remaining width |

### Wireframe

![UMA advisor portal wireframe: left navigation, UMA book summary and insights at the top; account list and account details across the remaining width below.](docs/wireframes/uma-advisor-wireframe.png)

The wireframe is the agreed layout baseline for UI-01. It is a static preview; the displayed values and customer names are illustrative. The starter application must keep the insights panel empty until workshop insight rules are implemented.

Rebalance status provides business context. A rebalance engine, order generation, trade execution, production authentication and live market-data feeds are outside the starter scope.

## Local architecture

| Component | Baseline | Local responsibility |
| --- | --- | --- |
| Frontend | Angular 22, TypeScript, npm | Single-page UI and development proxy |
| Backend | Java 21, Spring Boot 4.0.x, Maven Wrapper | REST APIs, business logic and embedded database |
| Database | H2 in-memory, version managed by Spring Boot | Synthetic workshop data, reset on backend restart |
| AI assistance | Participant-provided Claude access | Development assistance; not an application runtime dependency |

Run Angular and Java as two processes on the same desktop. H2 runs inside the Java process. Docker, Kubernetes, a separately installed database and cloud infrastructure are not required.

Angular calls relative `/api` URLs. Its development proxy forwards them to the Java backend. Keep server URLs out of UI components.

All workshop date rules must use a shared, fixed evaluation date supplied by the backend. The wireframe uses **2026-11-10**; SET-02 will formalize this configuration and API contract.

## SET-01 — Create the repository and local development setup

**Suggested owner:** one full-stack engineer, with a second developer verifying setup independently.

**User story:** As a workshop developer, I want to clone one repository and start the Angular UI and Java backend from my IDE, so I can build the portal without provisioning external infrastructure.

**Outcome:** a minimal running foundation that proves Angular → Java → H2 connectivity. The completed portal, business data and insight logic are delivered by later stories.

### Implementation tasks

1. Create the frontend, backend and documentation folders in the existing GitLab repository.
2. Generate a minimal Angular application and a Java Spring Boot application.
3. Include Maven Wrapper launchers for Windows and macOS/Linux. Use the repository-local Angular CLI through npm scripts; a global CLI installation is not required.
4. Pin exact application dependency versions. Commit `package-lock.json`, the backend build configuration and Maven Wrapper configuration. Record the tested Node, npm and JDK versions.
5. Configure H2 in-memory mode and demonstrate database access with a minimal connectivity check. Domain tables and workshop seed data belong to DATA-01 onward.
6. Provide `GET /api/health`, returning `{"status":"UP"}` only when the backend can query H2. Return a non-success response if the check fails.
7. Display a minimal Angular screen showing whether the backend is available. This is a connectivity screen; the agreed portal layout belongs to UI-01.
8. Configure the Angular development proxy and a local backend profile. Bind both development servers to localhost.
9. Provide named IDE run tasks for starting the backend and frontend independently, plus a convenient combined start option. Supply VS Code configurations as the reference setup; document equivalent IDE-terminal commands for other IDEs.
10. Configure Java and Angular test runners. Add a meaningful backend health/database check and a frontend connectivity-screen check. Do not implement workshop insight tests.
11. Add ignore rules for dependencies, build outputs, local credentials, logs and personal IDE settings. Preserve shared run configurations.
12. Update this README with verified startup, test, stop and troubleshooting instructions. Validate from a fresh checkout on the intended workshop operating system.

### Acceptance criteria

- [ ] A second developer can follow this README from a fresh checkout without undocumented steps.
- [ ] The README identifies the operating systems and IDE setup actually verified; support is not claimed for untested combinations.
- [ ] Dependency installation and builds succeed using the committed npm lockfile and Maven Wrapper.
- [ ] The backend starts at `http://localhost:8080` and successfully queries its embedded H2 database.
- [ ] `http://localhost:8080/api/health` returns HTTP 200 and `{"status":"UP"}` when healthy.
- [ ] The frontend starts at `http://localhost:4200` and obtains backend status through its `/api` proxy.
- [ ] When the backend stops, the UI displays an unavailable state rather than retaining a misleading success indicator after its next check or page reload.
- [ ] The named IDE run tasks and documented terminal commands both work.
- [ ] Backend and frontend tests pass, and the Angular production build succeeds.
- [ ] Stopping and restarting the backend recreates the in-memory database; no persistent database service is required.
- [ ] No portal business features, customer data or insight implementations are needed to complete this story.
- [ ] No Docker, cloud service, app login or Claude API key is required to run the application.
- [ ] The implementation is committed to GitLab with verification evidence in its merge request.

### Dependencies and completion boundary

The GitLab project exists. Implementation requires repository access, permitted JDK/Node installations and access to approved npm/Maven dependency sources.

SET-01 can finish before SET-02. Its temporary health response is infrastructure plumbing, not the domain API contract. Full account APIs, seeded business data, the final UI and workshop handoff are separate stories.

## Target repository structure

```text
frontend/
  src/
  angular.json
  package.json
  package-lock.json
  proxy.conf.json
backend/
  src/main/java/
  src/main/resources/
  src/test/java/
  .mvn/wrapper/
  mvnw
  mvnw.cmd
  pom.xml
docs/
  wireframes/
    uma-advisor-wireframe.png
.vscode/
  tasks.json
  launch.json
  extensions.json
.gitignore
README.md
```

## Developer prerequisites

- Git and access to the existing internal GitLab project.
- JDK 21, with `JAVA_HOME` pointing to that JDK.
- Node.js 24, **24.15.0 or later within the 24.x line**, and its bundled npm.
- An IDE with Java and Angular/TypeScript support. VS Code is the reference run-configuration format; IntelliJ IDEA or Eclipse users may use the documented terminal commands.
- Access to approved dependency registries during installation and preflight. Configure corporate proxies/certificates through approved local settings; do not commit credentials or disable TLS verification.
- Claude access for the exercise, independent of application startup.

The implementation owner must record exact tested patch versions before SET-01 is closed. Freeze versions before workshop rehearsal; do not upgrade dependencies during the session.

## Target startup workflow

**These commands are the SET-01 delivery contract, not a claim that scaffold files already exist.** Run them after implementation is merged.

Clone the existing project, then open its root folder in your IDE:

```bash
git clone https://pscode.lioncloud.net/aasadlak/msoffsitetdd.git
cd msoffsitetdd
```

Use your approved GitLab authentication method. The clone URL is also available from the project's **Code / Clone** menu.

### Start the backend

From the IDE terminal, enter `backend/` and run:

**macOS / Linux**

```bash
./mvnw spring-boot:run -Dspring-boot.run.profiles=local
```

**Windows PowerShell**

```powershell
.\mvnw.cmd spring-boot:run -Dspring-boot.run.profiles=local
```

Verify `http://localhost:8080/api/health` before starting frontend checks.

### Start the frontend

In a second IDE terminal, enter `frontend/` and run:

```bash
npm ci
npm start
```

Open `http://localhost:4200`. `npm start` must configure the development proxy automatically.

Alternatively, use the shared IDE tasks **Start backend**, **Start frontend** or **Start local application**. These are required deliverables of SET-01.

### Run checks

From `backend/`:

```bash
./mvnw test
```

Windows PowerShell: use `.\mvnw.cmd test`.

From `frontend/`:

```bash
npm test
npm run build
```

`npm test` must run once and exit with a meaningful status. An optional `npm run test:watch` may support development.

### Stop and reset

Stop each process through the IDE or press `Ctrl+C` in its terminal. Restarting Java recreates H2. Later data stories will make this restart reload the synthetic workshop dataset automatically.

### Troubleshooting

| Symptom | Check |
| --- | --- |
| Java build uses the wrong runtime | Compare `java -version`, `JAVA_HOME` and the IDE project SDK. |
| npm install fails | Confirm the supported Node version, lockfile and approved registry/proxy access. |
| Maven download fails | Check approved artifact repository, local Maven settings and certificate configuration. |
| Port 4200 or 8080 is occupied | Stop the conflicting process; if a port is intentionally changed, update the proxy and run configuration consistently. |
| UI reports backend unavailable | Verify `/api/health` directly, then check backend logs and the Angular proxy. |
| Database changes disappear after restart | Expected: H2 is intentionally in-memory. |

## Working backlog

All stories are **planned** until implementation and acceptance evidence are available. The IDs below are the working baseline for assigning work.

| ID | Story |
| --- | --- |
| SET-01 | Create the repository and local development setup |
| SET-02 | Define shared contracts and workshop conventions |
| DATA-01 | Create the detailed account data model |
| DATA-02 | Seed the detailed sample accounts |
| DATA-03 | Seed book-level aggregates |
| DATA-04 | Seed workshop event and boundary data |
| JAVA-01 | Implement book summary services |
| JAVA-02 | Implement account search and listing |
| JAVA-03 | Implement account detail services |
| JAVA-04 | Create the insights extension framework |
| UI-01 | Build the application shell and summary layout |
| UI-02 | Build the account grid |
| UI-03 | Build the account detail section |
| UI-04 | Build the reusable insights display |
| READY-01 | Integrate and verify the local application |
| READY-02 | Prepare the participant handoff |

Deliver the foundation first, then parallelize data, Java and UI work against agreed contracts. Integrate the application and rehearse an exercise before the offsite.

## Data and workshop conventions

- Use synthetic customers, accounts and holdings only.
- Keep the evaluation date fixed and the seed data repeatable.
- Label all illustrative book aggregates separately from detailed account data.
- Resolve business ambiguity through approved acceptance criteria before implementation.
- Keep insight rules out of the starter baseline; participants implement them through TDD.
- Keep application runtime independent of Claude. Use the approved Claude access method for development assistance.

## Add files and collaborate in GitLab

Use the existing [MSOffsiteTDD repository](https://pscode.lioncloud.net/aasadlak/msoffsitetdd) for all starter application work.

- [Create or upload files](https://docs.gitlab.com/user/project/repository/web_editor/), including this root `README.md`.
- [Invite team members and collaborators](https://docs.gitlab.com/user/project/members/).
- [Create a merge request](https://docs.gitlab.com/user/project/merge_requests/creating_merge_requests/) for each completed story or independently reviewable change.
- [Link merge requests to issues](https://docs.gitlab.com/user/project/issues/managing_issues/#closing-issues-automatically), using the backlog story ID in the issue and merge request title.
- [Configure merge request approvals](https://docs.gitlab.com/user/project/merge_requests/approvals/) where supported by the internal GitLab instance.
- [Set up project integrations](https://pscode.lioncloud.net/aasadlak/msoffsitetdd/-/settings/integrations) if needed by the team.

### Suggested contribution workflow

1. Create or assign a GitLab issue using one of the backlog story IDs.
2. Create a feature branch from the repository's default branch, for example `feature/SET-01-local-setup`.
3. Make the change, run applicable checks and update documentation.
4. Open a merge request describing what changed and how it was verified.
5. Obtain review and merge through the team's agreed process.

### Test and delivery approach

Local execution is the workshop requirement. The **Run checks** section above defines the intended local commands; SET-01 must implement and verify them.

GitLab CI may later run the same builds and checks when an appropriate runner is available. A pipeline, cloud deployment and Auto DevOps are not prerequisites for SET-01 or workshop execution. The original template's cloud and Kubernetes deployment suggestions do not apply to this local application.

Use [GitLab issues](https://pscode.lioncloud.net/aasadlak/msoffsitetdd/-/issues) to record defects, setup problems and open decisions. Include the story ID, reproduction steps and relevant logs without credentials.

## Compatibility references

- [Angular version compatibility](https://angular.dev/reference/versions)
- [Spring Boot 4.0 system requirements](https://docs.spring.io/spring-boot/4.0/system-requirements.html)
