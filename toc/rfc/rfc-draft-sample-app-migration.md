# Meta
[meta]: #meta
- Name: Sample Application Migration and Portfolio Consolidation
- Start Date: 2026-10-09
- Author(s): @benjaminguttmann-avtq
- Status: Draft
- RFC Pull Request: Not yet submitted
- Related RFCs:
- Approving Working Groups: Buildpacks and Stacks; Docs
- Affected Component(s): cloudfoundry-samples repositories; sample documentation and validation for classic Cloud Foundry buildpacks

## Summary

Migrate and modernize a focused, maintained portfolio of Cloud Foundry sample
applications instead of carrying forward every historical repository unchanged.
Preserve distinct deployment capabilities, consolidate redundant examples, and
fill important buildpack coverage gaps.

A source review of 73 public repositories proposes retaining and modernizing
12 examples, replacing 8 implementations while preserving their capabilities,
consolidating 34 repositories, evaluating 11 platform/service demonstrations
separately, and excluding 8 supporting or superseded projects from application
migration. The 34 consolidation candidates have concrete replacement mappings
to 10 other existing sample repositories. Those 10 are not new repositories and
are not the complete future portfolio.

Approval establishes the proposed portfolio direction and migration safeguards.
The Buildpacks and Stacks and Docs working groups will review this proposal.
Validation will use kind-deployment with the latest respective buildpacks on all
available stacks, currently cflinuxfs4 and cflinuxfs5. This is a source-based
proposal, not a report of successful deployments or approval to retire repositories.

## Problem

The cloudfoundry-samples organization contains many historical applications,
including overlapping framework examples, obsolete deployment instructions,
service demonstrations and supporting projects. Migrating all of them unchanged
would carry forward redundant maintenance work and unsupported assumptions.
Discarding applications solely because of their age or programming language
would lose useful deployment examples.

Language coverage alone does not establish feature coverage. For example,
Spring Boot JAR and Tomcat WAR deployments differ; a JRuby application packaged
as a WAR does not demonstrate JRuby installation by the Ruby buildpack; and a
prebuilt sidecar does not demonstrate the Binary buildpack. Some old PHP samples
depend on Python-based user extensions that the reviewed PHP implementation no
longer supports.

The reviewed portfolio has no explicit samples for Staticfile, Nginx, R or
Binary. Other gaps include alternative package managers, published .NET
artifacts, direct JRuby deployment and vendored dependency variants. Meanwhile,
sidecars, routing, scaling and service brokers have platform value independent
of buildpack feature coverage and should not be removed as language duplicates.

The reviewed kind-deployment tooling also needs explicit preparation for this
test scope. Its [README](https://github.com/cloudfoundry/kind-deployment/blob/ad1ec94eacfdd41c09babb07d5290d12bdeef120/README.md)
documents Java, Node.js, Go and Binary as the default bootstrap set. The
[upload script](https://github.com/cloudfoundry/kind-deployment/blob/ad1ec94eacfdd41c09babb07d5290d12bdeef120/scripts/upload_buildpacks.sh)
takes buildpack versions from `versions.yaml`, lists cflinuxfs4 and cflinuxfs5,
and adds further language/serving buildpacks for the complete bootstrap. Apt is
absent from both sets. A default or complete bootstrap therefore does not
establish that every required buildpack is installed at its latest version, or
that both stacks have been tested.

## Proposal

### Goals

- Maintain a clear, owned set of examples for distinct CF deployment capabilities.
- Reduce duplicate examples without losing packaging, worker and composition paths.
- Keep examples current through automated dependency updates and deployment tests.

### Scope and non-goals

This proposal covers the public cloudfoundry-samples inventory reviewed on
2026-10-07 and its relationship to the Java, Python, Node.js, .NET Core, Go,
Ruby, Staticfile, Nginx, Apt, PHP, R and Binary classic CF buildpacks.

Paketo/CNB migration, buildpack implementation changes and CF runtime changes
are outside scope. Preserving every framework comparison, CMS, social integration
or application business workflow is also outside scope. Replacements preserve
the selected deployment/configuration demonstrations, not full application
behavior. Known users requiring excluded functionality MUST receive a separate
owner decision before their source repository is retired.

Recipes and documented variants are proposed examples, not evidence of successful
staging. No application builds or CF deployments were performed in the analysis.

### Portfolio decisions

| Treatment | Repositories | Proposed action |
| --- | ---: | --- |
| Retain and modernize | 12 | Maintain the core examples listed below; update dependencies, instructions and deployment configuration. |
| Replace while preserving the capability | 8 | Deliver small current implementations before retiring historical ones. |
| Consolidate | 34 | Implement the named replacement mappings and shared variants; archive sources only after their retirement gates pass. |
| Evaluate separately | 11 | Obtain explicit decisions from platform/service owners rather than treating these as buildpack duplicates. |
| Exclude from sample-app migration | 8 | Handle supporting, documentation or superseded repositories separately; exclusion does not authorize deletion. |

These categories total 73. They are proposals based on the reviewed snapshot,
not an assertion that every retained example works today. If new usage or feature
evidence changes a recommendation, record the reason and revised replacement.

The proposed retain-and-modernize set is:

| Repository | Demonstration to retain |
| --- | --- |
| cf-sample-app-nodejs | Basic Node.js deployment. |
| cf-sample-app-rails | Rails deployment, with database/migration variants to add. |
| cf-sample-app-spring | Small Spring deployment example. |
| spring-music | Spring Boot and database-service integration. |
| ruby-sample-app | Minimal Sinatra deployment, distinct from Rails. |
| pong_matcher_django | Python/Django and database integration. |
| dotnet-core-hello-world | ASP.NET Core deployment with a current runtime target. |
| test-app | Go deployment and CF diagnostic endpoints. |
| cf-ex-php-info | PHP runtime/extension diagnostics without exposing secrets. |
| cf-ex-composer | Composer dependencies and PHP extension requirements. |
| cf-ex-stand-alone | PHP CLI/non-web processes and CF tasks. |
| cf-ex-pgbouncer | Apt plus PHP, package installation and an additional process. |

The eight capability-preserving replacement cases are:

- cf-ex-phalcon
- cf-ex-phpmyadmin
- jruby-rails-bookshelf
- pong_matcher_groovy
- resque-sample
- spring-batch-tweet-workers
- springmvc-hibernate-template
- zentasks-scala-cloudfoundry

Preserve their selected capabilities through current variants; do not assume
Phalcon availability, legacy PHP extension
compatibility or that historical JRuby/WAR packaging covers direct JRuby.

The eleven separate platform/service decisions concern:

- capi-sidecar-samples
- cf-autoscaler
- cf-s3-demo
- fib-cpu
- github-service-broker-ruby
- go_service_broker
- http2_tile_demo
- rabbitmq-cloudfoundry-samples
- rails-elastic-search
- ratelimit-service
- multi-process-sample

The consolidation plan selects rabbitmq-cloudfoundry-samples as its messaging
destination: archiving it instead MUST block dependent retirements until a
revised messaging destination is agreed and validated.

### Consolidation destinations and missing variants

Consolidation means maintaining a smaller set of representative demonstrations,
not merging the old business applications into a single large application.
Keep variants small and independently deployable. Do not require every
database or external service merely to run the baseline example.

| Destination | Selected repository | Required work from the consolidation plan |
| --- | --- | --- |
| T-GO | test-app | Bound MySQL variant with a minimal persistent API and schema/migration step. |
| T-NODE | cf-sample-app-nodejs | MySQL API, WebSocket echo, native-module staging check and UTF-8 response test. |
| T-RAILS | cf-sample-app-rails | MySQL/PostgreSQL bindings, controlled migration tasks, Unicorn start profile and mail recipe. Current baseline uses SQLite. |
| T-RUBY | ruby-sample-app | Bound Redis read/write example; a genuine worker remains a separate capability-preserving replacement. |
| T-SPRING | spring-music | Validate existing SQL/MongoDB/Redis profiles; add redacted environment visibility and a mail recipe. |
| T-WAR | springmvc-hibernate-template | Reduce to a current WAR/servlet example with relational binding and cache configuration. |
| T-JAVA | spring-batch-tweet-workers | Separate executable Java Main web JAR and bin/lib worker-distribution variants. |
| T-DIST | pong_matcher_groovy | Modernize DistZip/Ratpack; add generic launcher/PORT and a protocol echo variant. |
| T-PHP | cf-ex-composer | MySQL/PostgreSQL bindings, routed API and supported artifact-preparation recipe; no legacy Python download hooks. |
| T-AMQP | rabbitmq-cloudfoundry-samples | Modern Java producer/worker and Node consumer, deterministic input, streaming variant and shared smoke tests. |

These ten destinations are six retain-and-modernize examples, three replacement
cases and one separately evaluated messaging repository. They overlap the
portfolio categories; they are not additional repositories and do not define
the final number of maintained repositories. Several required variants do
not yet exist.

The detailed analysis provides a source-to-destination decision for each of the
34 candidates, including current overlap, required additions, excluded business
scope and retirement prerequisites. Publish that mapping with the RFC and use
it as the implementation baseline. Implement shared variants once per destination
rather than copying them from each retiring application.

For example, enki, rails_sample_app and pong_matcher_rails map to
cf-sample-app-rails. MySQL/PostgreSQL and controlled migrations must be added
before dependent sources are retired; their blog, tutorial and Pong workflows
are not part of the replacement.

### Coverage additions

Provide explicit small samples for Staticfile, Nginx, R and Binary, using
suitable existing buildpack fixtures as starting points. These additions do
not require one repository per feature; separate runnable variants MAY share
a repository. Their names and repository locations remain to be agreed.

Preserve Java WAR, executable JAR, Groovy source, Play and distribution paths;
direct JRuby; PHP CLI/tasks; and Apt composition. Add common variants to existing
examples, including Yarn/pnpm, published .NET artifacts and vendored dependencies.
More specialized agents, proxies and configuration flags should be prioritized
by user need rather than requiring exhaustive public sample coverage.

Distinguish app-side service configuration from buildpack-injected integrations.
Verify current PHP user-extension behavior before a
replacement relies on the documented JSON extension mechanism.

### Repository location, ownership and maintenance

This RFC requires review and approval by both the Buildpacks and Stacks working
group and the Docs working group. Relevant platform/service maintainers should
participate in decisions affecting their examples. Actual repository ownership
changes, creation, renaming or archiving follow the existing
[repository ownership process](rfc-0007-repository-ownership.md), including TOC
and affected working-group approvals where required; this RFC does not replace it.

Before a move, agree the destination organization, owners, permissions and
security/disclosure responsibility. A new organization is not assumed, and a
repository need not move if its current location remains appropriate.

For retained repositories moving between organizations, prefer a GitHub transfer
where available. Check history, issues, pull requests, releases, redirects and
CI integrations before and after transfer; document limitations of any alternative.

Each maintained example needs a named owner, feature goal, runnable instructions,
required services and recorded runtime/buildpack/stack support. Owners maintain
the automated checks and update process below. Successful tests establish only
the recorded combinations, not compatibility with future versions.

Owners and implementation capacity remain to be agreed. The source review does
not establish delivery dates, costs, savings or a final repository count.

### Automated dependency maintenance

Maintained sample repositories MUST configure automated dependency-update PRs,
using Dependabot, Renovate or equivalent tooling, for supported ecosystems and
all relevant sample/variant directories. Include manifests and lockfiles, and
keep CI dependencies updated. Document the update cadence and any tooling gaps;
the owner provides a maintenance process for unsupported dependencies.

Update PRs run the applicable automated build/unit tests and feature smoke
tests using kind-deployment, the latest respective buildpacks and all available
stacks, currently cflinuxfs4 and cflinuxfs5. Test the affected runnable variants,
not just dependency installation. Required results apply to the current PR
revision against the current target branch. Failed, missing, skipped or stale
required checks MUST block merging.

Repositories can enable auto-merge without individual maintainer review for
dependency-only PRs from trusted update automation that meet an owner-approved
eligibility policy. Start with explicitly eligible patch/minor updates covered
by the test suite; a version label alone is not evidence of compatibility.
Auto-merge must respect repository rules and cannot bypass required status checks.

Major-version updates, runtime-target changes, CI workflow/test changes and
updates outside the eligibility policy MUST receive maintainer review. A named
owner handles blocked updates and regressions even when routine updates merge
automatically. Use least-privilege credentials and protect validation access
from untrusted PR code.

If an automatically merged update breaks an example, pause auto-merge for that
update class, revert or fix the dependency change, and rerun the affected stack
matrix before re-enabling it. Automation and tests are proposed, not configured.

### Execution and validation

1. **Confirm the baseline:** Publish the reviewed inventory and mappings,
   resolve owners and destination location, check existing users/references,
   and record any changes to the proposed dispositions.
2. **Deliver representatives:** Modernize retained examples and implement the
   capability-preserving replacements and shared consolidation variants. Do
   not archive a source merely because its replacement has a README.
3. **Validate:** Prepare kind-deployment with the latest buildpack for each
  required language/serving capability, plus Apt for the composition example.
  Build and deploy each required variant on all available stacks, currently
  cflinuxfs4 and cflinuxfs5. Check detected container, staging logs and startup
  commands; perform HTTP, worker, task and binding smoke tests.
4. **Close coverage gaps:** Add and validate the explicit missing buildpack
   examples and prioritized dependency/configuration variants. This work can
   run alongside representative modernization.
5. **Retire in batches:** Archive only sources whose individual retirement
   gates pass; keep unready sources available with accurate status notices.

At the start of each validation run, resolve the latest respective buildpack
and record its exact version/artifact; do not assume the bootstrap pins are
latest. Install or update the required artifacts through the CF buildpack API/CLI,
including any buildpack omitted by the bootstrap. Use the latest respective
constituents for multi-buildpack examples. Verify installed buildpacks and stacks,
explicitly select each stack, and record the kind-deployment revision. Do not
modify deployment tooling as part of this RFC merely to hide a preparation gap.

Required combinations that are unavailable or fail remain blocked, not skipped.
Tests demonstrate the selected feature: actual Apt package use, appropriate
worker health checks, sidecar memory/process behavior and the intended packaging
container. Offline/vendored claims require testing without dependency-network
access. Provide required service instances and isolate test data and credentials;
never publish secrets or full environment/credential dumps.

Keep a migration checklist with source, destination/variant, owner, deployment
revision, exact tested runtime/buildpack artifacts, stack, result and retirement
approval. Refresh affected tests after material runtime/buildpack changes.

### Retirement gates and compatibility

A source repository MUST NOT be archived until:

- Its selected replacement capabilities are implemented and validated, or its
  independent owner explicitly agrees that no replacement is needed.
- Known users and public documentation/tutorial references have been checked;
  excluded business functionality and any unresolved usage are addressed.
- Its README identifies its disposition, supported successor and limitations;
  references under community control are updated.
- Useful test assets/recipes are preserved and required owner/governance
  approvals are recorded.

Prefer archiving over deletion so historical code and discussions remain
accessible. This proposal authorizes no repository deletion. Archiving does
not provide ongoing security maintenance; historical examples MUST NOT be
advertised as current production-ready applications.

If replacement validation fails, postpone retirement. If a later failure
invalidates it, update notices, repair the destination and revisit the source
disposition with its owner. Old runtimes are not a supported fallback.

### Alternatives considered

| Alternative | Assessment |
| --- | --- |
| Migrate all 73 repositories unchanged | Preserves historical breadth but carries obsolete instructions, duplicates and compatibility assumptions forward. |
| Keep only one application per language | Smaller, but loses packaging, worker and composition capabilities and ignores platform/service value. |
| Archive all outdated repositories immediately | Removes historical references without validated successors and may disrupt tutorials/users. |
| Create one large multi-feature application | Couples unrelated services and makes individual deployment paths harder to understand and validate. |
| Proposed representative portfolio with runnable variants | Preserves selected capabilities with shared maintenance; requires explicit ownership, implementation and validation before retirement. |

### Risks and expected outcomes

| Failure scenario | Response |
| --- | --- |
| A target repository lacks a required DB, worker or packaging variant. | Implement and validate it before retiring dependent sources; a shared language is not enough. |
| Bootstrap installs older versions or omits Apt/a required stack artifact. | Prepare and verify the explicit test matrix; leave affected validation blocked rather than silently reducing coverage. |
| A service-dependent test cannot run or a required CI check is skipped. | Block merge/retirement and provide the missing service or test; an HTTP-only test cannot establish a binding or worker claim. |
| An auto-merged dependency update passes incomplete tests but breaks the example. | Pause the affected auto-merge policy, revert/fix and add a regression check before revalidation. |
| A tutorial or user still depends on excluded business functionality. | Resolve usage with the owner and update references before archiving; the small sample is not a drop-in application replacement. |

The trade-off is less duplicate maintenance in exchange for investment in
shared variants, a two-stack test environment and dependency automation.
Those shared destinations need committed owners; neither effort nor savings
has been measured. A moving latest-buildpack matrix can expose infrastructure
or upstream regressions, so failed runs need diagnosis rather than automatic
relaxation of the acceptance criteria.

Completion means every reviewed repository has a recorded disposition, every
retirement has passed its gates, retained/replacement examples have documented
owners, tested deployment paths and operational dependency-update automation
with required checks, and the four initially unrepresented
buildpacks have explicit validated samples. It does not mean all historical
business functionality is recreated or that exactly ten repositories remain.

### Open decisions before implementation

- What is the destination organization, if a repository move is desired, and
  which current ownership/charter entries and RFC 0007 approvals need updating?
- Which maintainers own the retained examples, replacement cases and missing
  buildpack samples, with what implementation/review capacity?
- Which service instances and access will be provided in kind-deployment,
  and how will repeatable validation checks be maintained?
- Should the latest-buildpack test source use published releases or default-branch
  builds? Agree the source before configuring CI; record exact artifacts in either case.
- What are the owner decisions for the eleven platform/service demonstrations
  and the eight supporting/superseded projects?

### Supporting analysis

- [sample-app-buildpack-executive-summary.en.md](../../../../../sample-app-buildpack-executive-summary.en.md): decision brief and retain-and-modernize list.
- [sample-app-buildpack-coverage.en.md](../../../../../sample-app-buildpack-coverage.en.md): pinned source evidence, all 73 dispositions, feature gaps and the complete 34-source consolidation mapping.

These are local draft references. The inventory and complete mapping MUST be
published alongside the RFC or at stable publicly accessible URLs before
submission; update these links accordingly. The October 7 source snapshot is
evidence for the proposal, not a substitute for checking current installed
buildpack releases during implementation. This local draft has not been
submitted and has no assigned RFC number.
