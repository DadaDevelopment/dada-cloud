---
name: dada-cloud
description: Deploy and operate apps on Dada Cloud (Kubernetes PaaS with GitOps) through its MCP tools or the ddc CLI - connect a GitHub repo, build, deploy, set env vars, add Postgres/Redis, read logs, spin up a Dada Box sandbox. Use when the user wants to ship, configure or debug something on Dada Cloud / console.dada-tuda.ru.
---

# Dada Cloud

Dada Cloud deploys a repository into the user's own project without a terminal,
Dockerfile or fork. Every change is a GitOps operation: the console writes git,
Argo CD syncs the cluster. You drive it either through MCP tools (preferred in
an agent) or through the `ddc` CLI.

## Connect

- Claude Code: `/plugin marketplace add DadaDevelopment/dada-cloud`, then
  `/plugin install dada-cloud@dada-cloud`. First call opens a browser login at
  Dada ID.
- Any other MCP client: remote server `https://console.dada-tuda.ru/mcp`.
  Server-side agents send `Authorization: Bearer <dada-id-token>`.
- Shell: install `ddc` from https://github.com/DadaDevelopment/ddc, run
  `ddc login`, then `ddc deploy` in the app directory.

You only see projects the signed-in account has a role on. Never ask the user to
paste passwords or tokens into the chat.

## Core loop

1. `listProjects` -> pick the project; `getProject` gives its environments.
2. Code from GitHub: `listGitInstallations` / `listInstallationRepos`. No
   installation yet -> `getGitInstallUrl` and hand the link to the user.
3. `createApp` (or `connectGitRepo` for an existing app), then `triggerBuild`.
4. Writes are asynchronous. Every write returns an operation: poll
   `getOperation` until `Ready` (done) or `Failed` / `Cancelled`. `Committed`
   means git is written, not that pods run. `WaitingForApproval` needs a human.
5. `getBuild` for build status and log tail; `searchLogs` for runtime logs.

## Configure

- Env vars: `listEnvVars`, `setEnvVar` (mark secrets as secret), `deleteEnvVar`.
  A change rolls the app.
- Port / storage / image: `updateAppPort`, `updateAppStorage`, `updateAppImage`.
- Postgres: `createDatabase`, then `getDatabaseCredentials` and wire them via
  `setEnvVar`; read-only SQL through `queryDatabase` / `explainDatabaseQuery`.
- Redis: `createServiceCache`, `getServiceCacheCredentials`.
- Custom domain: `addDomainAuthorization` -> user adds the DNS record ->
  `verifyDomainAuthorization`.

## Dada Box

An ephemeral root sandbox in one call: `createBox`, `getBoxConnection`,
`exposeBox` to publish a port, `extendBox` / `suspendBox` / `deleteBox` to
manage its life. `crystallizeBox` turns a working box into a deployable app.

## Diagnose

App not reachable: check the last operation (`getOperation`), then `getBuild`,
then `searchLogs`. A build that passed with a crash-looping pod is almost always
a wrong port (`updateAppPort`) or a missing env var. Report what you saw in the
logs, not a guess.
