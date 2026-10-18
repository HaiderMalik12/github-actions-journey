# GitHub Actions Fundamentals Learning Plan

Your starting workflow, [hello.yml](.github/workflows/hello.yml), runs on a push, starts one job on an Ubuntu runner, and executes two shell commands.

Build on it one concept at a time. Aim for **four weeks, with four sessions each week**. Allow 30–45 minutes for early sessions, 60–90 minutes for the CI exercises, and 2–3 hours for the capstone. Treat this as a flexible schedule. Move forward when you can complete each exercise and explain the result.

## Before you start

You should be able to commit and push changes, create a branch, open a pull request, and run basic shell commands. Use a practice repository with GitHub Actions enabled. These exercises use GitHub-hosted Ubuntu runners and Bash. Use your actual default branch name wherever the plan says `main`.

Keep using the same small application throughout the CI exercises. Choose a language and package manager you already know so you can focus on GitHub Actions.

## Week 1: Understand how workflows run

| Session | Learn | Hands-on exercise |
|---|---|---|
| 1. Workflow anatomy | YAML indentation; workflows, events, jobs, runners, and steps; `name`, `on`, `jobs`, `runs-on`, and `run` | Explain every line of `hello.yml`. Add a third step that prints the runner’s working directory. |
| 2. Triggers | `push`, `pull_request`, `workflow_dispatch`, branch filters, and path filters | Add a manual trigger with one input. Then configure pushes to `main` and pull requests targeting `main`. Test an included and an excluded path change separately. |
| 3. Runner environment | What runs on the runner; working directories; files shared between steps; separate shell processes for steps | Create a file in one step and read it in the next. Try exporting a shell variable and changing directories; observe that these shell changes do not carry into the next step. |
| 4. Actions versus commands | `uses` versus `run`; checking out repository code; action inputs with `with`; version references | Add `actions/checkout`, then read your repository’s README from a step. |

**Trigger details:** Manual dispatch requires the workflow file to exist on the default branch. For `pull_request`, branch filters match the target branch. When branch and path filters are both configured, both must match.

**Checkpoint:** Explain this chain without looking it up: **event → workflow → job → runner → steps**.

Reference: [Understanding GitHub Actions](https://docs.github.com/en/actions/get-started/understand-github-actions).

## Week 2: Control execution and pass information

| Session | Learn | Hands-on exercise |
|---|---|---|
| 5. Variables and contexts | Workflow, job, and step `env`; `github`, `runner`, and `vars` contexts; `${{ }}` expressions; `$GITHUB_ENV` | Print the event name and branch. Override a variable at different scopes. Write a computed value to `$GITHUB_ENV` and read it in a subsequent step in the same job. |
| 6. Conditions and failures | `if`, exit codes, skipped steps, `success()`, `failure()`, and `always()` | Deliberately fail a step. Add a diagnostic step that runs after failure and a short diagnostic step using `always()`. Observe the difference between a failed step and a skipped step. |
| 7. Multiple jobs | Parallel execution, job isolation, and `needs` | Create `lint`, `test`, and `build` jobs. Use placeholder commands initially. Make `build` depend on both checks. Confirm that a file from `test` is absent in `build` on a separate GitHub-hosted runner. |
| 8. Outputs | Step IDs, `$GITHUB_OUTPUT`, step outputs, and job outputs | Generate a version string and pass it to a dependent job. |

**Checkpoint:** Predict which jobs will run, fail, or skip before triggering the workflow.

Reference: [Workflow syntax](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax). Use this as a lookup tool.

## Week 3: Build useful continuous integration

| Session | Learn | Hands-on exercise |
|---|---|---|
| 9. Real CI | Checkout, runtime setup, dependency installation, linting, testing, and building | Add a tiny application in a language you know. Add a lockfile where your package manager supports one, and use its reproducible installation command. Make lint, test, and build work locally, then run them on pull requests. |
| 10. Matrices | Testing multiple runtime versions; matrix variables; `fail-fast` | Run the tests against two supported runtime versions. |
| 11. Artifacts and caching | Saving build results versus reusing dependencies | Upload a build result as an artifact. Download the artifact in a downstream job. Enable dependency caching; inspect cache hit/miss logs on repeated runs and after a lockfile change. Confirm CI still passes without a cache. |
| 12. Debugging | Logs, failed commands, reruns, and job summaries | Introduce a failing test, diagnose it from the Actions logs, and fix it. Write a short result to `$GITHUB_STEP_SUMMARY`. |

**Checkpoint:** Open a pull request that fails CI, fix it, and download the successful build artifact.

Reference: [GitHub Actions tutorials](https://docs.github.com/en/actions/tutorials), including language-specific CI guides.

## Week 4: Make workflows reliable and secure

| Session | Learn | Hands-on exercise |
|---|---|---|
| 13. Permissions and secrets | `GITHUB_TOKEN`, least-privilege `permissions`, secrets versus variables, fork pull requests, untrusted input, and action SHA pinning | Set `permissions: contents: read`. Reference a dummy secret through `env` and check only whether it is set. Pass a manual text input through `env` into a quoted shell variable. Pin an action to a verified full commit SHA. |
| 14. Run controls | `timeout-minutes`, concurrency groups, and cancellation | Group runs by workflow and pull request or branch. Push twice to verify cancellation within that group, and confirm another branch is unaffected. Add a job timeout. |
| 15. Review and optional reuse | Consolidate core concepts; introduction to reusable workflows (`workflow_call`) versus composite actions | First repeat any exercise you cannot explain. If ready, extract one repeated sequence and explain your choice of reuse mechanism. |
| 16. Capstone | Combine the fundamentals | Build the pipeline below from a blank workflow file. |

**Security details:** Fork pull requests normally do not receive repository secrets, and their `GITHUB_TOKEN` is read-only. Use `pull_request` for the practice CI pipeline. For untrusted text, avoid inserting expressions directly into a `run` script; pass values through `env` and quote shell expansions.

Reference: [Secure use reference](https://docs.github.com/en/actions/reference/security/secure-use).

## Capstone project

Build a pull request pipeline that:

- [ ] Runs lint and tests.
- [ ] Tests two supported runtime versions.
- [ ] Builds only after checks pass.
- [ ] Uploads a build artifact.
- [ ] Uses explicit permissions.
- [ ] Cancels outdated runs.
- [ ] Produces a readable job summary.
- [ ] Uses a reproducible dependency installation and succeeds without a cache.
- [ ] Sets a job timeout.

**Acceptance checks:** Open a pull request with a failing test and confirm the build is skipped. Fix the test and confirm the build and artifact upload succeed. Push two quick updates to test cancellation, then download and inspect the artifact. Explain how information travels through environment variables, outputs, and artifacts.

**Optional repository integration:** Configure the CI checks as required checks in a branch ruleset, if your repository supports it. Confirm a failing check blocks merging.

References: [Workflow commands](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-commands), [dependency caching](https://docs.github.com/en/actions/concepts/workflows-and-actions/dependency-caching), and [sharing artifacts between jobs](https://docs.github.com/en/actions/tutorials/store-and-share-data).

## How to study

For every session, follow this loop: **predict → edit → run → inspect → explain**.

Keep a short note of what surprised you. Record the change you made, the result you expected, what actually happened, and what you learned.

You have mastered the fundamentals when you can write the capstone with documentation as a reference, explain every line, and diagnose an intentionally broken run.

## After the fundamentals

Continue with deployment environments and approvals, scheduled workflows, cloud authentication with OpenID Connect, service containers, and custom actions as your projects require them. Reusable workflows can also be studied in depth here.

## First exercise

1. Add a third step to `hello.yml` that prints the working directory using `pwd`.
2. Push the change and inspect the logs for all three steps.
3. Add a step that runs `exit 1` before the final step.
4. Predict whether the final step will run, then push and check.
5. Record what happened and explain why.
6. Remove the intentional failure so your practice workflow finishes successfully again.
