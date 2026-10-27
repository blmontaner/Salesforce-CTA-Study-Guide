# CTA Guide Comparison and Follow-Up Evidence

Reviewed: 23 October 2026. Companions: `cta-wiki-audit-2026-10-23.md` and `cta-realistic-study-plan-2026-10-23.md`.

## Recommendation

Use **SF Toolkit selectively as a quick reference in the sampled domains**, particularly Service Cloud, sharing, and migration. Use **the Coding With The Force repository for its structured topic map, public practice material, and your editable fork**. Use **official Salesforce exam guides and implementation documentation as the authority**. Neither community guide is a complete or reliably correct standalone CTA curriculum.

This is not a blanket accuracy ranking: the earlier review read 39 wiki pages and compared material findings with newer repository text; this follow-up inspected the SF Toolkit navigation and ten substantive pages plus its introduction. More headings do not prove more complete coverage, and an absence of findings in an unreviewed chapter is not approval.

No changes were made to either community site or to the user's GitHub fork. Its previously reported seven-commit lag was not refreshed in this investigation.

## Scope and Retrieval

SF Toolkit browser-inspected pages: Introduction, Integration, CDC, OAuth Flows, Service Cloud, Sharing, Data Migration, Development Lifecycle, Exam Guidelines, Apex Governor Limits, and Archiving & Backup. Embedded diagrams were not exhaustively audited, and commercial allocations were not validated across every SKU. No upstream commit or publication date was established for this live site.

Direct HTTP tools still failed on several domains with TLS/proxy errors. The research browser successfully loaded Google, SF Toolkit, Salesforce Help, and Trailhead. Title-based searches resolved actual current destinations, including the exam guides and storage page. A top result was treated as a lead, not proof: the destination's substantive text was inspected. Google AI Overviews were not used as evidence.

Other Salesforce Developer evidence was retrieved by read-only research agents from substantive guide bodies or the public documentation content endpoint, with release-specific qualifications. Historical audit evidence is distinguished below from newly inspected evidence. No exams, org configuration, signing operations, or restore tests were executed.

## Highest-Priority SF Toolkit Findings

### 1. JWT Signing Instructions Are Internally Incompatible

Source: [OAuth Flows](https://sf-toolkit.com/cta#OAuth%20Flows/OAuthFlows.md), JWT example and steps.

The example declares `"alg": "RS256"` but computes the signature using `HMACSHA256(..., Private Key)` and explicitly tells the reader to sign with HMACSHA256. RS256 is RSA signing with SHA-256, not a symmetric HMAC. Following those instructions does not produce an RS256 assertion. Do not fix this Salesforce flow by merely changing the header to HS256.

Verified Salesforce evidence: [JWT claims](https://developer.salesforce.com/docs/platform/named-credentials/references/named-credentials-reference/jwt-claims.html) describes RS256 as an asymmetric private/public-key algorithm. [Salesforce's incoming JWT Bearer example](https://developer.salesforce.com/blogs/2022/05/integrate-einstein-bots-into-any-channel-using-the-new-sdk-and-framework) uses `Algorithm.RSA256` with an RSA private key.

The same Toolkit page says to use jwt.io and "Input your header, payload, and private key to validate the token." Do not enter production private keys or live bearer assertions into online debugging tools. Local signature verification needs the matching public key, not disclosure of the private key. [Salesforce Crypto documentation](https://developer.salesforce.com/docs/get_document_content/apexref/apex_classes_restful_crypto.htm/en-us/264.0) explicitly distinguishes private-key signing from public-key verification. No actual leakage was demonstrated; the finding concerns unsafe instructions.

Positive finding: this page correctly explains the PKCE challenge/verifier and recommends code flow with PKCE instead of User Agent flow. Those improvements do not cancel out the JWT defect.

### 2. CDC Configuration Incorrectly Excludes Tooling API

Source: [CDC](https://sf-toolkit.com/cta#Integration/ChangeDataCapture.md), Custom Channels and Enriched Fields sections.

Both sections say configuration is possible only through Metadata API. Current official [custom-channel documentation](https://developer.salesforce.com/docs/platform/change-data-capture/guide/cdc-custom-channel.html) states "You can create a custom channel with Metadata API or Tooling API." [Enrichment documentation](https://developer.salesforce.com/docs/platform/change-data-capture/guide/cdc-enrich-intro.html) likewise names both APIs.

The page explains 72-hour retention and encrypted event storage, but does not give a complete replication recovery or subscriber-authorization design. Add checkpoints, duplicate handling, gap/overflow reconciliation, and recovery outside retention. Mention Pub/Sub rather than presenting Streaming API as the entire current landscape.

Security qualification from newly inspected official documentation: [CDC ignores record sharing](https://developer.salesforce.com/docs/platform/change-data-capture/guide/cdc-security-perms.html), but [delivered CDC payloads respect subscriber FLS](https://developer.salesforce.com/docs/platform/change-data-capture/guide/cdc-security-field.html). Custom platform events have different FLS behavior. Do not generalize one event type's security to another. Schema visibility also differs from payload visibility.

### 3. Sharing Limits Contradict Each Other

Source: [Sharing](https://sf-toolkit.com/cta#Core/Sharing.md), Sharing Limits section.

The prose says the 300-rule limit can be raised to 500; the table says 1,000. This is a directly observed contradiction. Neither increased cap was independently verified here. Support exceptions must not be treated as universally available entitlements.

Positive finding: this page distinguishes Product2 guest sharing, Knowledge access models, several object-specific OWD choices, sharing sets, teams, and account-relationship sharing. It is more useful than the wiki's incomplete record-security chapter for these sampled topics. It still needs complete CRUD/FLS, license, guest-access, and negative-access test coverage.

### 4. Service Limits Lose Essential Scope

Source: [Service Cloud](https://sf-toolkit.com/cta#Product%20&%20Clouds/ServiceCloud.md), limits table.

The numeric values `2,000 / 50 / 500 / 3,000 / 300 / 25 / 200` match the inspected official [rule-limits table](https://help.salesforce.com/s/articleView?id=service.rules_limits.htm&type=5). They are **not** established numerical errors or an incorrectly copied Workflow table.

The issue is omitted qualifiers. The official 50-active-rules ceiling is an aggregate applying to a combination of workflow, assignment, auto-response, escalation rules, and record-change processes. It does not mean 50 simultaneously active Case assignment rules. Official [assignment guidelines](https://help.salesforce.com/s/articleView?id=service.customize_leadrules.htm&type=5) explicitly state: "Your organization can have one case assignment rule active at a time." They also say only one rule per assignment-rule type can be in effect.

The page treats Live Agent as a current add-on. The inspected [legacy Chat guide](https://developer.salesforce.com/docs/get_document_content/live_agent_dev/live_agent_dev_guide_intro.htm/en-us/262.0) identifies February 14, 2026 as its retirement date and recommends Messaging for In-App and Web. Its future-tense wording is itself dated, but its published retirement date is clear. Do not design a new implementation around legacy Chat without current product confirmation.

Its Social Customer Service advice includes an explicit end-of-2024 warning. Related official documentation verifies Social Studio retirement on November 18, 2024, not an independently retrieved identical retirement statement for every Social Customer Service component. Preserve that product distinction.

### 5. Backup and Archive Guidance Is Historical

Source: [Archiving & Backup](https://sf-toolkit.com/cta#Storage/Archiving.md).

The page says there is no specific Salesforce archiving product. Salesforce now publishes a [Salesforce Archive product page](https://www.salesforce.com/platform/data-archiving-solutions/) covering policy-based offloading of records, files, and attachments, permission-based access, and unarchiving.

The backup section is explicitly dated November 2023 and says Salesforce Backup does not support files and only supports daily backup. This is not current portfolio guidance. [Backup & Recover, formerly Own Recover](https://www.salesforce.com/platform/data-backup-recovery/), lists files and attachments, automated daily backups, and backup on demand; continuous protection is a separate add-on. [Backup & Recover Next](https://www.salesforce.com/blog/introducing-salesforce-backup-recover-next/) is a distinct native offering, with documented files/attachments support and region-specific availability.

Do not transfer features between the older Salesforce Backup SKU, Salesforce Backup & Recover (formerly Own Recover), and Salesforce Backup & Recover Next. The historical 2023 statement was not disproved for its original SKU and date.

The advice to avoid Big Objects because they are difficult to justify to judges is coaching opinion, not an official prohibition. Choose an archive based on access/query needs, retention, residency, authorization, cost, and recovery rather than a memorized vendor preference.

### 6. Migration Recommendations Need Safety Boundaries

Source: [Data Migration](https://sf-toolkit.com/cta#Exam/DataMigration.md).

Useful content includes cleaning/deduplication, external IDs, parent grouping, and alternatives to big-bang migration. However, "Temporarily disable sharing settings" and broadly disabling Flow/triggers/automation are unsafe as unconditional steps. Specify approved migration-only bypasses, who retains access, maintenance-window restrictions, validation, sharing recalculation, and re-enablement checks. Deferring calculation must not be casually equated with disabling authorization.

Bulk API 2.0 parallel loading is a legitimate option, not a universal best choice. Account for locking/skew, retryable failures, partial success, reconciliation, delta capture, cutover, and rollback. The inspected text does not supply that operational design.

## Comparative Coverage

| Need | SF Toolkit observed value | GitHub guide observed value | Use |
| --- | --- | --- | --- |
| Quick feature/decision lookup | Concise product, sharing, migration, and limit summaries; broader navigation | Structured numbered chapters, with several substantial notes but many placeholders | Prefer Toolkit selectively in reviewed areas; verify high-impact claims |
| Service Cloud | Case routing, assignment, auto-response, escalation, Omni-Channel; materially more text | Service chapter is almost empty | Toolkit as a starting checklist, not a complete solution design |
| Identity/security | Useful PKCE explanation, but unsafe JWT instructions | Verified MFA/SAML/OIDC errors remain in newer text | Use official identity sources; neither is safe as sole authority |
| Integration operations | Pattern list and CDC detail; weak complete failure/recovery treatment | Broader pattern notes but blanket middleware advice and recovery gaps | Official integration guides plus scenario-specific failure design |
| Development lifecycle | Generic lifecycle stages; an environment/branching section and diagrams, not a detailed tested delivery plan | Governance notes, but delivery coverage remains incomplete | Build an explicit CI/CD/environment/rollback decision card |
| Currentness | Some explicit 2023/2024 historical content and current-flow improvements | Newer August 2026 repository still retains older technical errors | Evaluate each claim's source/date, not the site's appearance |
| Scenario practice | Guidelines and diagram-led material; no comparable full-scenario corpus verified in sampled pages | Previously inventoried 6 mini, 3 medium, and 7 full PDFs | Use GitHub practice collection with reviewed feedback; contents/rights not exhaustively validated |
| Personal maintained notes | Live community reference; no verified source history captured | Your fork is an editable home for corrected decision cards and study notes | Preserve fork-only work; sync/corrections require a separate authorized action |

## Official Exam Evidence Now Confirmed

Title searches led to substantive official Help pages, resolving major gaps in the original audit.

| Stage | Verified prerequisite | Published timings | Other verified requirements |
| --- | --- | --- | --- |
| [Evaluation](https://help.salesforce.com/s/articleView?id=005299002&type=1) | Salesforce Certified Application Architect and Salesforce Certified System Architect | 60-minute preparation, 30-minute presentation, 30-minute Q&A, 15-minute feedback/discussion | One CTA judge; external camera; Google Doc scenario, Slides, Sheet, blank Lucidchart, timer; AI and templates prohibited |
| [Review Board](https://help.salesforce.com/s/articleView?id=005298996&type=1&language=en_US) | Architect Review Board Evaluation completed with a passing grade | 3-hour preparation, 45-minute presentation, 40-minute Q&A | Panel of CTA judges; Google Doc scenario, Slides, Sheet, blank Lucidchart with Salesforce Shape Library, timer; no other materials; Lucidchart templates prohibited |

The named Review Board segments total 265 minutes (4 hours 25 minutes). That is not a verified total booking duration including breaks, administration, and orientation. Evaluation segments including feedback total 135 minutes (2 hours 15 minutes).

Both guides describe substantial architect/implementation experience as their intended audience, separate from formal credential prerequisites. They permit other product recommendations when defensible; a list of industry clouds in a community sidebar does not make each one mandatory exam mastery.

Fees, result timelines, retake/section-retake rules, evaluation-pass validity, and full day-of logistics remain unverified in this follow-up. Candidate instructions must be checked before booking. The Evaluation guide contains an apparently self-referential scheduling sentence; do not derive an extra exam stage from it.

## Other Earlier Gaps Resolved or Narrowed

| Topic | Newly inspected evidence | Disposition |
| --- | --- | --- |
| Data storage | [Data and Storage Resources](https://help.salesforce.com/s/articleView?id=xcloud.admin_monitorresources.htm&type=5): Professional/Enterprise 10 GB per org plus 20 MB per eligible user license; Performance/Unlimited 10 GB plus 120 MB, with 20 MB for Lightning Platform Starter | Universal 120 MB/internal user is incorrect. Mixed/custom licenses and specific contracted bundles need actual Setup/contract validation. Do not memorize the old 102 GB example |
| File storage | Same Help table lists 10 GB/org for Professional/Enterprise/Performance/Unlimited and 1 GB/org for Essentials/Starter | No universal per-user increment was established by this current table. Do not infer every possible license, purchase, or bundle from it |
| External account roles | [Record-access group membership](https://developer.salesforce.com/docs/get_document_content/draes/draes_group_membership.htm/en-us/262.0) moves customer/partner account roles with the account owner's role | Correct the wiki's enabling-administrator claim. [High-volume users](https://developer.salesforce.com/docs/get_document_content/draes/draes_object_relationships_implicit_sharing.htm/en-us/262.0) use a sharing model without roles/groups. Role-count taxonomy still unresolved |
| Field Service | [ServiceResource](https://developer.salesforce.com/docs/get_document_content/object_reference/sforce_api_objects_serviceresource.htm/en-us/264.0): dispatchers require Field Service Dispatcher PSL. [Mobile setup](https://developer.salesforce.com/docs/get_document_content/field_service_dev/sfs_gs_lwc_action_org_setup.htm/en-us/262.0) requires specific mobile permissions | Service Cloud User/Knowledge checkboxes alone are not sufficient. Full persona/contract entitlement matrix remains unverified |
| Currency | [DatedConversionRate](https://developer.salesforce.com/docs/get_document_content/object_reference/sforce_api_objects_datedconversionrate.htm/en-us/264.0) supports updating rates. [Trailhead ACM exercise](https://trailhead.salesforce.com/content/learn/projects/prepare-your-salesforce-org-for-users/update-the-exchange-rate) changes Close Date and demonstrates converted amount changes | Effective dating is not an immutable accounting snapshot. Detailed historical-rate-edit/reporting effects still not independently confirmed |
| Flow callouts | [Flow metadata](https://developer.salesforce.com/docs/get_document_content/api_meta/meta_visual_workflow.htm/en-us/264.0) distinguishes transaction models and async-after-commit paths | Does not establish a complete callout-context matrix. Scheduled path, async path, schedule-triggered Flow, and Scheduled Apex must remain distinct |

The current classic-edition Help storage table and new marketing pricing tiers use different product labels. Do not invent a mapping between them or override actual org entitlements with a universal formula.

## Starter Decision Cards

These are short architecture prompts, not wiki rewrites or a complete set of chapter cards. Limits and options apply only to the identified context. Practical controls below are design recommendations, not claims that a product implements them automatically.

### Identity and JWT

- Use: Code+PKCE for user-delegated native/mobile access; JWT Bearer where an approved certificate-based integration acts as an authorized user.
- Alternatives: Client credentials for appropriate service integrations; SAML/OIDC for authentication rather than interchangeable API token types.
- Limits: Client/app registration, flow support, scope, assertion validity, and user authorization must match the exact deployment.
- Security: RS256 is asymmetric; keep signing keys protected, verify with public keys, and never publish secrets to debug tools.
- Failure: Monitor token errors; rotate/revoke compromised credentials; retry authentication deliberately, not indefinitely.
- Sources: [Mobile web-server flow](https://developer.salesforce.com/docs/platform/mobile-sdk/guide/oauth-web-server-flow.html), [JWT claims](https://developer.salesforce.com/docs/platform/named-credentials/references/named-credentials-reference/jwt-claims.html), [Crypto](https://developer.salesforce.com/docs/get_document_content/apexref/apex_classes_restful_crypto.htm/en-us/264.0).

### CDC and Business Events

- Use: CDC for record-change replication; custom platform events for a defined business-event contract.
- Alternatives: Bulk/polling for tolerable latency, or synchronous request/reply where the caller requires a result.
- Limits: Current CDC/high-volume event retention is 72 hours; Pub/Sub does not support every legacy Streaming event type. Legacy standard-volume events have a separately documented 24-hour window.
- Security: CDC external subscriptions ignore sharing but respect payload FLS; encrypted storage does not replace subscriber authorization.
- Failure: Persist processing-aware checkpoints, deduplicate business effects, reconcile gaps/overflow, and backfill outside retention. Enriched replay fields can have latest rather than original values.
- Sources: [Durability](https://developer.salesforce.com/docs/platform/pub-sub-api/guide/event-message-durability.html), [CDC security](https://developer.salesforce.com/docs/platform/change-data-capture/guide/cdc-security-perms.html), [FLS](https://developer.salesforce.com/docs/platform/change-data-capture/guide/cdc-security-field.html), [enrichment considerations](https://developer.salesforce.com/docs/platform/change-data-capture/guide/cdc-enrich-considerations.html).

### Direct Integration or Middleware

- Use: Direct calls for bounded, manageable contracts; middleware when transformation, multi-system orchestration, buffering, and centralized operations justify it.
- Alternatives: Events, bulk synchronization, external objects, or an existing enterprise integration platform.
- Limits: Volume, latency, API and transaction budgets, atomicity, and vendor capabilities drive selection; neither "always middleware" nor "always direct" is sound.
- Security: Separate user identity from service identity; control credentials, scopes, data exposure, and source authorization.
- Failure: Define timeouts, safe retries, idempotency, reconciliation, alerts, and accountable owners.
- Source: [Official request/reply pattern](https://developer.salesforce.com/docs/atlas.en-us.integration_patterns_and_practices.meta/integration_patterns_and_practices/integ_pat_remote_process_invocation_state.htm), previously inspected in the original audit.

### Sharing and External Personas

- Use: Select object, field, and record controls for a persona/license matrix; apply role-based and high-volume external models deliberately.
- Alternatives: Sharing rules/sets, teams, account relationships, territories, or supported programmatic sharing as requirements justify.
- Limits: Object/license eligibility differs. Do not rely on inconsistent community caps or assume every external license uses roles.
- Security: Test unauthorized access and guest exposure as well as the successful user journey; integration permissions may be broader than interactive sharing.
- Failure: Plan ownership changes, recalculation, deprovisioning, and access review without casually disabling authorization.
- Sources: [External-role movement](https://developer.salesforce.com/docs/get_document_content/draes/draes_group_membership.htm/en-us/262.0), [high-volume model](https://developer.salesforce.com/docs/get_document_content/draes/draes_object_relationships_implicit_sharing.htm/en-us/262.0).

### Service Routing

- Use: Assignment for owner/queue selection; Omni-Channel for work distribution and agent capacity; escalation and entitlements for appropriate time-based service requirements.
- Alternatives: Flow-based or external routing for requirements not met by standard features, with explicit operational ownership.
- Limits: Aggregate rule ceilings do not override one-active-Case-assignment-rule behavior. Messaging and legacy Chat are not interchangeable current product choices.
- Security: Ensure queue/agent licenses and record access match routing; do not infer authorization merely because work was assigned.
- Failure: Define default/catch-all ownership, unavailable-agent paths, routing monitoring, and missed-SLA escalation.
- Sources: [Rule limits](https://help.salesforce.com/s/articleView?id=service.rules_limits.htm&type=5), [assignment guidelines](https://help.salesforce.com/s/articleView?id=service.customize_leadrules.htm&type=5), [Chat retirement](https://developer.salesforce.com/docs/get_document_content/live_agent_dev/live_agent_dev_guide_intro.htm/en-us/262.0).

### Migration and Delivery

- Use: A rehearsed load/cutover plan plus controlled release pipeline for changing systems without losing business consistency.
- Alternatives: Big-bang, phased, or coexistence migration; parallel or serial/load partitioning where supported and justified.
- Limits: Locking/skew, referential order, partial success, source deltas, metadata dependencies, and environment constraints.
- Security: Scope any automation bypass, protect test/migration data, restrict access during changes, and audit privileged actions.
- Failure: Reconcile counts and business totals, track failed records, restore controls, define rollback/forward-fix criteria, and rehearse recovery.
- Sources: [SF Toolkit migration checklist](https://sf-toolkit.com/cta#Exam/DataMigration.md), [official Review Board objectives](https://help.salesforce.com/s/articleView?id=005298996&type=1&language=en_US); safeguards are architectural recommendations, not a tested runbook.

### Storage, Archive, and Backup

- Use: Size active storage, archive inactive data according to policy, and back up for loss/corruption recovery; these solve different needs.
- Alternatives: Salesforce Archive, supported external archive/data services, and contracted native or third-party backup offerings.
- Limits: Edition/license allocation, retrieval/index support, supported records/files/metadata, residency, and add-on availability.
- Security: Match archived and restored data authorization, encryption/key ownership, legal holds, retention, and deletion obligations.
- Failure: Set RPO/RTO, reconcile record/file relationships, test restore/unarchive, and monitor backup/archive jobs.
- Sources: [Storage](https://help.salesforce.com/s/articleView?id=xcloud.admin_monitorresources.htm&type=5), [Archive](https://www.salesforce.com/platform/data-archiving-solutions/), [Backup & Recover](https://www.salesforce.com/platform/data-backup-recovery/).

### CTA Preparation

- Use: Official objectives and stage rules to organize decisions, labs, unfamiliar scenarios, presentations, and live Q&A.
- Alternatives: Learning-only 12-week topic track versus a longer board-oriented program; peer review versus optional paid coaching.
- Limits: Eligibility and consistent performance matter more than reading completion; our 40-week/12-hour baseline is a planning estimate.
- Security: Respect exam confidentiality and the prohibition on AI/templates; use legitimate public practice material only.
- Failure: Log weak domains, verify disputed facts, vary cases, repeat timed assessments, and use buffer time rather than booking by calendar alone.
- Sources: [Evaluation](https://help.salesforce.com/s/articleView?id=005299002&type=1), [Review Board](https://help.salesforce.com/s/articleView?id=005298996&type=1&language=en_US), companion study plan for explicitly unofficial scheduling guidance.

## Next Personalization Inputs

The guide comparison does not establish the user's readiness. Ask for Salesforce implementation/architecture experience, current certifications, sustainable weekly hours, weakest domains, reviewer access, and whether the goal is the Evaluation, Review Board, or general architecture learning. Do not sync or restructure the fork merely because it was mentioned.
