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
It does not authorize immediate archiving or deletion. The review is static:
applications have not been built, staged, or validated on a current CF platform.
The Buildpacks and Stacks and Docs working groups will review and approve this
RFC. Validation will use kind-deployment with the latest respective buildpacks
on all available stacks, currently cflinuxfs4 and cflinuxfs5.
The destination organization, ownership assignments and implementation schedule
remain explicit decisions required before repository moves begin.

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

## Proposal

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

MUST, SHOULD and MAY express requirements of this proposal, not actions already
performed. A recipe is not evidence of a working integration, and a documented
variant is not evidence of successful staging.

### Portfolio decisions

| Treatment | Repositories | Proposed action |
| --- | ---: | --- |
| Retain and modernize | 12 | Maintain the core examples listed below; update dependencies, instructions and deployment configuration. |
| Replace while preserving the capability | 8 | Deliver small current implementations before retiring historical ones. |
| Consolidate | 34 | Implement the named replacement mappings and shared variants; archive sources only after their retirement gates pass. |
| Evaluate separately | 11 | Obtain explicit decisions from platform/service owners rather than treating these as buildpack duplicates. |
| Exclude from sample-app migration | 8 | Handle supporting, documentation or superseded repositories separately; exclusion does not authorize deletion. |

These categories total 73. They are proposals based on the reviewed snapshot,
not an assertion that every retained example works today. Owners MAY revise an
individual disposition when new usage or feature evidence emerges, but MUST
record the reason and any revised replacement mapping.

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

The eight capability-preserving replacement cases are cf-ex-phalcon,
cf-ex-phpmyadmin, jruby-rails-bookshelf, pong_matcher_groovy, resque-sample,
spring-batch-tweet-workers, springmvc-hibernate-template and
zentasks-scala-cloudfoundry. Preserve their selected capabilities through
current variants; do not assume Phalcon availability, legacy PHP extension
compatibility or that historical JRuby/WAR packaging covers direct JRuby.

The eleven separate platform/service decisions concern capi-sidecar-samples,
cf-autoscaler, cf-s3-demo, fib-cpu, github-service-broker-ruby,
go_service_broker, http2_tile_demo, rabbitmq-cloudfoundry-samples,
rails-elastic-search, ratelimit-service and multi-process-sample. In particular,
the consolidation plan selects rabbitmq-cloudfoundry-samples as its messaging
destination: archiving it instead MUST block dependent retirements until a
revised messaging destination is agreed and validated.

### Consolidation destinations and missing variants

Consolidation means maintaining a smaller set of representative demonstrations,
not merging the old business applications into a single large application.
Variants SHOULD be small and independently deployable. Do not require every
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
portfolio categories; they MUST NOT be added to the category totals or presented
as the final number of maintained repositories. Several required variants do
not yet exist.

The detailed analysis provides a source-to-destination decision for each of the
34 candidates, including current overlap, required additions, excluded business
scope and retirement prerequisites. That mapping MUST accompany RFC review and
be used as the implementation baseline. Shared variants MUST be implemented
once per destination rather than copied from each retiring application.

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
More specialized agents, proxies and configuration flags SHOULD be prioritized
by user need rather than requiring exhaustive public sample coverage.

Coverage MUST distinguish app-side service configuration from buildpack-injected
integrations. Current PHP user-extension behavior MUST be verified before a
replacement relies on the documented JSON extension mechanism.

### Repository location, ownership and maintenance

This RFC requires review and approval by both the Buildpacks and Stacks working
group and the Docs working group. Relevant platform/service maintainers SHOULD
participate in decisions affecting their examples.

Before moving repositories, sponsors and maintainers MUST agree the destination
organization, responsible maintainers, repository permissions and security/
disclosure ownership. This RFC does not assume a particular new organization
or require moving repositories if their current location remains appropriate.

For retained repositories moving between organizations, prefer a GitHub transfer
where available so history and collaboration metadata can be retained. Verify
the effects on issues, pull requests, releases, redirects, CI secrets and
integrations rather than assuming everything transfers. If a transfer is not
possible, document the alternative and any metadata/history limitations.

Each maintained sample or variant MUST have a named owner, a clear feature goal,
documented build/deployment commands, required services, and the runtime,
buildpack release and stack combinations actually tested. Owners MUST maintain
repeatable automated checks and a dependency-update process as specified below.
Initial validation MUST cover
all stacks available in kind-deployment, currently cflinuxfs4 and cflinuxfs5;
successful validation does not imply compatibility with future stack or
buildpack versions.

No budget, delivery date or final repository count is established by the source
review. Capacity and sequencing MUST be agreed with the maintainers before
execution; claimed savings require a separate estimate.

### Automated dependency maintenance

Maintained sample repositories MUST configure automated dependency-update PRs,
using Dependabot, Renovate or equivalent tooling, for supported ecosystems and
all relevant sample/variant directories. Include manifests and lockfiles, and
keep CI dependencies updated. Document the update cadence and any tooling gaps;
an owner MUST provide a maintenance process for unsupported dependencies.

Update PRs MUST run the applicable automated build/unit tests and feature smoke
tests using kind-deployment, the latest respective buildpacks and all available
stacks, currently cflinuxfs4 and cflinuxfs5. Test the affected runnable variants,
not just dependency installation. Required results MUST apply to the current PR
revision against the current target branch. Failed, missing, skipped or stale
required checks MUST block merging.

Repositories MAY enable auto-merge without individual maintainer review for
dependency-only PRs from trusted update automation that meet an owner-approved
eligibility policy. The initial policy SHOULD allow only explicitly eligible
patch/minor updates whose behavior is covered by the required test suite.
Patch/minor labels alone are not evidence of compatibility. Auto-merge MUST
respect repository rules and required status checks; it MUST NOT bypass them.

Major-version updates, runtime-target changes, CI workflow/test changes and
updates outside the eligibility policy MUST receive maintainer review. A named
owner remains accountable for the policy, failed/blocked updates and regressions,
even when routine updates merge automatically. Use least-privilege automation
credentials and protect validation access from untrusted PR code.

This RFC proposes the automation requirements; it does not claim that the bots,
test suites or auto-merge rules are already configured.

### Execution and validation

1. **Confirm the baseline:** Publish the reviewed inventory and mappings,
   resolve owners and destination location, check existing users/references,
   and record any changes to the proposed dispositions.
2. **Deliver representatives:** Modernize retained examples and implement the
   capability-preserving replacements and shared consolidation variants. Do
   not archive a source merely because its replacement has a README.
3. **Validate:** Build and deploy each required variant using kind-deployment
  with the latest buildpack for its respective language or serving capability,
  on all available stacks, currently cflinuxfs4 and cflinuxfs5. Inspect detected
  container, staging logs and startup commands; perform HTTP, worker, task and
  binding smoke tests.
4. **Close coverage gaps:** Add and validate the explicit missing buildpack
   examples and prioritized dependency/configuration variants. This work can
   run alongside representative modernization.
5. **Retire in batches:** Archive only sources whose individual retirement
   gates pass; keep unready sources available with accurate status notices.

At the start of each validation run, resolve the latest respective buildpack
and record its exact version and artifact or commit identifier. Explicitly
select each stack rather than relying on a default. For multi-buildpack examples,
use the latest respective constituent buildpacks, including Apt and PHP where
applicable. Record the kind-deployment revision and available stack inventory
so the tested matrix is reproducible. Any unavailable or failing required
combination MUST be recorded and resolved before the dependent retirement gate
can pass; do not silently omit a stack.

Validation MUST demonstrate the intended feature: actual Apt package use,
appropriate worker health checks, sidecar memory/process behavior, and explicit
buildpack/container selection for packaging variants. Offline/vendored claims
require a test without dependency-network access. Do not publish secrets or
unredacted environment/service-credential dumps. Service instances and validation
access for kind-deployment MUST be provided; credentials MUST be handled outside
public repositories.

Record validation evidence in a migration checklist: source, destination and
variant, owner, kind-deployment revision, exact buildpack/runtime versions and
artifacts, stack, result, documentation references and retirement approval.
Revalidate any replacement affected by later material runtime/buildpack changes.

### Retirement gates and compatibility

A source repository MUST NOT be archived until:

- Its selected replacement capabilities are implemented and validated, or its
  independent owner explicitly agrees that no replacement is needed.
- Known users and public documentation/tutorial references have been checked;
  excluded business functionality and any unresolved usage are addressed.
- Its README identifies its disposition, supported successor and limitations;
  references under community control are updated.
- Useful test assets/recipes are preserved and the owner records approval.

Prefer archiving over deletion so historical code and discussions remain
accessible. This proposal authorizes no repository deletion. Archiving does
not provide ongoing security maintenance; historical examples MUST NOT be
advertised as current production-ready applications.

If replacement validation fails, postpone the affected retirement. If a
post-migration issue invalidates a replacement, update notices and references,
repair or revise the destination, and reconsider the affected disposition with
the owner. Do not describe old runtimes as a supported fallback.

### Alternatives considered

| Alternative | Assessment |
| --- | --- |
| Migrate all 73 repositories unchanged | Preserves historical breadth but carries obsolete instructions, duplicates and compatibility assumptions forward. |
| Keep only one application per language | Smaller, but loses packaging, worker and composition capabilities and ignores platform/service value. |
| Archive all outdated repositories immediately | Removes historical references without validated successors and may disrupt tutorials/users. |
| Create one large multi-feature application | Couples unrelated services and makes individual deployment paths harder to understand and validate. |
| Proposed representative portfolio with runnable variants | Preserves selected capabilities with shared maintenance; requires explicit ownership, implementation and validation before retirement. |

### Risks and expected outcomes

Risks include losing undocumented usage, underestimating modernization effort,
introducing unverified service/extension assumptions, and concentrating work on
destinations without committed owners. The gates above limit feature loss;
named ownership, independent variants and recorded validation reduce coupling
and ambiguity. No delivery effort or maintenance reduction has been measured.

Completion means every reviewed repository has a recorded disposition, every
retirement has passed its gates, retained/replacement examples have documented
owners, tested deployment paths and operational dependency-update automation
with required checks, and the four initially unrepresented
buildpacks have explicit validated samples. It does not mean all historical
business functionality is recreated or that exactly ten repositories remain.

### Open decisions before implementation

- What is the destination organization, if a repository move is desired, and
  who has authority to approve transfers and archiving?
- Which maintainers own the retained examples, replacement cases and missing
  buildpack samples, with what implementation/review capacity?
- Which service instances and access will be provided in kind-deployment,
  and how will repeatable validation checks be maintained?
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
