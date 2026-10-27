# Salesforce CTA Study Guide: Accuracy and Completeness Audit

Reviewed: 23 October 2026.

Follow-up on the same date resolved major exam-policy and storage gaps and narrowed other technical uncertainties. See `cta-guide-comparison-2026-10-23.md` for the SF Toolkit comparison and new primary-source evidence. The scope below describes the original wiki review.

## Verdict

The linked wiki is neither complete nor error-free. It is useful as an informal topic index and collection of personal experience, not as an authoritative syllabus or sole CTA preparation resource. The author explicitly identifies it as outdated and unfinished. The newer guide improves exam commentary and diagrams, but retains numerous technical errors and placeholder chapters.

This is a substantive text review, not a certification that every statement, link, diagram, entitlement, or scenario is correct. Finding no issue in a chapter is not an endorsement of every claim in it.

## Scope and Evidence

- Original: <https://github.com/Coding-With-The-Force/Salesforce-CTA-Study-Guide/wiki>.
- Wiki snapshot: commit `fb12839b1cc493aa6b0331c1baef43fd3c92a98b`, latest commit dated 1 May 2026.
- Updated repository: <https://github.com/Coding-With-The-Force/Salesforce-CTA-Study-Guide>, commit `87586bb9953192fd553a4e22cfe18c7689edd10a`, latest commit dated 19 August 2026.
- Newer-site source: `website/docs/`; also compared the repository's `docs/` tree for the technical chapters.
- Read all 39 wiki Markdown pages: chapters 00-36, Home, and Notes for Improvement. Compared corresponding newer text for material findings. Reviewed source, not a rendered end-to-end website.
- The embedded governor-limits chart was inspected. Other embedded ERDs, screenshots, videos, PDFs, and scenario solutions were not exhaustively audited. Practice scenario files were inventoried, not validated as exam-equivalent or independently rights-cleared.
- Important technical claims were cross-checked using public Salesforce documentation and public security standards. Retrieved documentation includes Summer '26 and Winter '27 releases; confirm deployment-specific differences.
- Many Salesforce Help, Trailhead, and external pages were inaccessible from this environment. Such claims are explicitly unresolved below. Source links for unresolved claims are verification targets, not evidence successfully inspected.
- The report distinguishes verified errors, verified omissions, unsupported absolutes, and unverified commercial/exam claims. It does not invent a numerical completeness score.
- No changes were made to the upstream wiki or repository. The audit and study plan are local artifacts.

Line references below refer to Markdown in the original wiki snapshot. The chapter number plus quoted text provides a stable way to locate a finding through the wiki UI.

## Highest-Priority Corrections

### 1. IAM definitions contain fundamental errors

**Chapter 06, lines 36, 55, 74, 143, 185. Verified.**

- MFA is described as "three or more ways." It requires at least two distinct factor types. Two-factor authentication is a form of MFA; fingerprints are something you are, not something you possess.
- SAML is said to use "XML Signature Wrapping" to sign assertions. XML Signature is the signing mechanism; signature wrapping is an attack.
- The ID token is said to be sent to the resource server to obtain access. An OIDC ID token communicates authentication to the client/relying party. Use access tokens for protected resource APIs, including UserInfo.
- Authorization Code is incorrectly restricted to clients able to store a secret, and Implicit is recommended for SPAs/mobile. Public clients can use Authorization Code with PKCE. Modern guidance discourages Implicit.

The newer IAM page retains the first three errors and the secret requirement. It adds useful PKCE guidance and discourages Implicit, but leaves contradictory preceding text.

Sources: [NIST MFA](https://pages.nist.gov/800-63-3/sp800-63b.html#aal2), [OWASP SAML](https://cheatsheetseries.owasp.org/cheatsheets/SAML_Security_Cheat_Sheet.html), [OIDC Core](https://openid.net/specs/openid-connect-core-1_0.html#IDToken), [RFC 8252](https://www.rfc-editor.org/rfc/rfc8252.html#section-6), [RFC 9700](https://www.rfc-editor.org/rfc/rfc9700.html#section-2.1.1).

### 2. The limits image is not safe to memorize

**Chapter 20, lines 5-6, embedded image. Verified.**

The chart labels the DML statement limit as 10,000. Standard per-transaction limits are **150 DML statements** and **10,000 DML rows**. It also gives stale or wrong context-specific values: future invocations from Queueable, long-running synchronous Apex concurrency, and skinny-table columns. Current skinny-table documentation allows 200 columns, rather than the chart's 100.

Use release-current official tables, with execution-context caveats. Scheduled Apex uses synchronous limits. The chart remains in the newer guide.

Sources: [Apex limits, Summer '26](https://developer.salesforce.com/docs/atlas.en-us.262.0.apexcode.meta/apexcode/apex_gov_limits.htm), [Skinny tables](https://developer.salesforce.com/docs/atlas.en-us.salesforce_large_data_volumes_bp.meta/salesforce_large_data_volumes_bp/ldv_deployments_infrastructure_skinny_tables.htm).

### 3. Middleware is incorrectly mandatory

**Chapter 08, lines 32, 127, 135-139; Notes for Improvement, line 13. Verified.**

Examples include "utilize middleware like Mulesoft always" and "No point to point integrations." Direct Apex callouts and External Services are legitimate choices. Middleware is appropriate when orchestration, transformation, buffering, centralized governance, and operational requirements justify it. It is not an unconditional requirement or automatic reliability improvement.

Still present in the newer technical chapter.

Source: [Salesforce request/reply integration pattern](https://developer.salesforce.com/docs/atlas.en-us.integration_patterns_and_practices.meta/integration_patterns_and_practices/integ_pat_remote_process_invocation_state.htm).

### 4. Sharing is incorrectly generalized across standard objects

**Chapter 11, lines 5-7. Verified.**

"All objects" do not universally support sharing rules, sharing sets, sharing groups, and configurable OWD. Supported controls differ by object and license. Product2, for example, supports only guest-user sharing rules. Build an object/persona/license access matrix rather than assuming every mechanism works everywhere.

Still present in the newer guide.

Source: [Sharing rule considerations](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_sharing_rule_considerations.htm).

### 5. External-object access is not necessarily all-or-nothing

**Chapter 08, line 116. Verified.**

"Access to ALL OF ITS RECORDS" conflates Salesforce-native sharing limitations with source authorization. Per-user authentication can preserve source-system authorization; a shared principal has different implications. Specify adapter support, identity, permissions, and source-side access explicitly. This is not a claim that every adapter supports Salesforce sharing rules.

Still present in the newer guide.

Source: [ExternalDataSource metadata](https://developer.salesforce.com/docs/atlas.en-us.api_meta.meta/api_meta/meta_externaldatasource.htm).

### 6. Event replay is not a complete delivery/recovery design

**Chapter 08, lines 46-51. Verified omission and outdated interface claim.**

The 72-hour retention statement is supported, but replay is not application acknowledgment, unlimited recovery, or exactly-once business processing. Add durable checkpoints, idempotency, retry/dead-letter handling, monitoring, and reconciliation after retention expires. Replay IDs are opaque and non-contiguous, not business IDs.

"External services must integrate via the Streaming API" is outdated. Pub/Sub API is another supported interface. Also qualify the empApi recommendation at lines 99-101: it is desktop Lightning Experience functionality, not Salesforce mobile functionality.

Still present in the newer guide.

Sources: [Event durability](https://developer.salesforce.com/docs/platform/pub-sub-api/guide/event-message-durability.html), [Pub/Sub API](https://developer.salesforce.com/docs/platform/pub-sub-api/guide/intro.html), [empApi](https://developer.salesforce.com/docs/platform/lightning-component-reference/guide/lightning-emp-api.html).

### 7. Mobile advice does not distinguish current products/runtimes

**Chapter 07, lines 12-16, 34-56. Verified.**

The blanket prohibition on offline creation and actions omits Mobile App Plus with LWC Offline. Evaluate the exact runtime, license, object/component support, priming, and synchronization behavior. MDM does not require choosing Hybrid instead of Native.

Refresh tokens do not guarantee login only once; expiry/revocation can force reauthentication and purge Mobile SDK SmartStore. New integration registrations should evaluate External Client Apps: new connected-app creation is restricted as of Spring '26, while existing apps continue to work.

Still present in the newer guide.

Sources: [Offline apps](https://developer.salesforce.com/docs/atlas.en-us.mobile_offline.meta/mobile_offline/intro_enabled_apps.htm), [Offline actions](https://developer.salesforce.com/docs/atlas.en-us.mobile_offline.meta/mobile_offline/dx_onboard_wizard_lwc_qa.htm), [MDM](https://developer.salesforce.com/docs/platform/mobile-sdk/guide/oauth-mdm.html), [Refresh tokens](https://developer.salesforce.com/docs/platform/mobile-sdk/guide/oauth-refresh-token-flow.html), [Client registration](https://developer.salesforce.com/docs/platform/mobile-sdk/guide/connected-apps.html).

### 8. Knowledge security omits one channel and other access requirements

**Chapter 30, lines 39-45. Verified.**

The page lists three visibility checkboxes but omits the public knowledge base. The four channels are internal, customer, partner, and public. Publication state, object/article permissions, data-category visibility, and the configured sharing model also matter. Channel selection alone is not sufficient authorization.

Still present in the newer guide.

Sources: [KnowledgeArticleVersion](https://developer.salesforce.com/docs/atlas.en-us.object_reference.meta/object_reference/sforce_api_objects_knowledgearticleversion.htm), [Knowledge__kav](https://developer.salesforce.com/docs/atlas.en-us.object_reference.meta/object_reference/sforce_api_objects_knowledge__kav.htm).

### 9. Standard-object relationship/automation rules are inaccurate

**Chapter 21, lines 30-40. Verified.**

The "TRUE OOTB details" list incorrectly treats inherited access, configurable CRUD, relationship metadata, and parent automation as equivalent. PricebookEntry references, CampaignMember.CampaignId, and WorkOrderLineItem.WorkOrderId are documented as lookups, not the claimed universal master-detail model.

The claim that any child update fires parent triggers/flows is also overbroad. Parent processing depends on actual parent changes and applicable rollup/cross-object behavior.

Still present in the newer guide.

Sources: [PricebookEntry](https://developer.salesforce.com/docs/atlas.en-us.object_reference.meta/object_reference/sforce_api_objects_pricebookentry.htm), [CampaignMember](https://developer.salesforce.com/docs/atlas.en-us.object_reference.meta/object_reference/sforce_api_objects_campaignmember.htm), [WorkOrderLineItem](https://developer.salesforce.com/docs/atlas.en-us.object_reference.meta/object_reference/sforce_api_objects_workorderlineitem.htm), [Order of execution](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_triggers_order_of_execution.htm).

### 10. Privacy consent is confused with retention and universal enforcement

**Chapter 36, lines 10, 34-40, 52-58. Verified.**

Consent effective dates indicate consent validity, not how long data is retained. Individual does not contain the same email-opt-out field as Contact/Lead. Recording preferences does not automatically suppress every connected sender.

The linked Compile Consent Settings API evaluates an explicit supported set of objects/actions, not every consent record type. It accesses org-wide consent data and requires elevated read permissions; Consent Write is a separate operation. These are important trust-boundary considerations.

Still present in the newer guide.

Sources: [ContactPointConsent](https://developer.salesforce.com/docs/atlas.en-us.object_reference.meta/object_reference/sforce_api_objects_contactpointconsent.htm), [Individual](https://developer.salesforce.com/docs/atlas.en-us.object_reference.meta/object_reference/sforce_api_objects_individual.htm), [Compile consent](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/resources_consent_action.htm), [Consent write](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/resources_consent_write.htm).

## Additional Confirmed Findings

| Chapter / wiki lines | Correction | Public evidence |
| --- | --- | --- |
| 03:28,40 | Multi-org can distribute org-wide consumption; it does not raise an individual transaction's governor budgets. | [Apex limits](https://developer.salesforce.com/docs/atlas.en-us.262.0.apexcode.meta/apexcode/apex_gov_limits.htm) |
| 05:21 | Masking is not universally display-only. Sandbox anonymization can replace/delete stored values irreversibly. Newer page adds terminology but retains other old text. | [Data Mask](https://help.salesforce.com/s/articleView?id=sf.data_mask_overview.htm&type=5) |
| 08:82-84 | Distinguish sObject Collections' 200 records from Composite subrequests. A 2,000-record query response is a page, not the entire possible result. Bulk API selection depends on volume and asynchronous suitability. | [Composite](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/resources_composite_composite_post.htm), [Query](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/resources_query.htm) |
| 09:11 | Salt prevents cross-account hash correlation and reuse of generic precomputed tables; it is not primarily a password-length concealment mechanism. Hashing is not encryption. | [NIST password verification](https://pages.nist.gov/800-63-4/sp800-63b.html#passwordver) |
| 11:39-43 | Campaign is not the Account lookup described here; CampaignMember.AccountId supports Accounts as Campaign Members when enabled. | [CampaignMember](https://developer.salesforce.com/docs/atlas.en-us.object_reference.meta/object_reference/sforce_api_objects_campaignmember.htm) |
| 11:253-259 | Replace the Fall 2023 prediction with the Create Quotes Without a Related Opportunity setting and standard-Quote/managed-CPQ distinction. | [Quote](https://developer.salesforce.com/docs/atlas.en-us.object_reference.meta/object_reference/sforce_api_objects_quote.htm) |
| 13:1-16 | Label this Salesforce CPQ managed-package material. Salesforce documents no new feature development and points to Revenue Cloud. Existing customers are not thereby declared unsupported. | [CPQ guide](https://developer.salesforce.com/docs/atlas.en-us.cpq_dev_api.meta/cpq_dev_api/cpq_developer_guide_intro.htm) |
| 14:23-36 | Use ServiceTerritoryMember, not "Service Territory Resource"; include AssignedResource. ProductRequired can relate to WorkType, WorkOrder, or WorkOrderLineItem. | [Territory membership](https://developer.salesforce.com/docs/atlas.en-us.object_reference.meta/object_reference/sforce_api_objects_serviceterritorymember.htm), [AssignedResource](https://developer.salesforce.com/docs/atlas.en-us.object_reference.meta/object_reference/sforce_api_objects_assignedresource.htm), [ProductRequired](https://developer.salesforce.com/docs/atlas.en-us.object_reference.meta/object_reference/sforce_api_objects_productrequired.htm) |
| 14:32 | Skill records support API creation/upsert, so guided setup/Classic are not the only creation paths. | [Skill](https://developer.salesforce.com/docs/atlas.en-us.object_reference.meta/object_reference/sforce_api_objects_skill.htm) |
| 16:5,11-37 | Skinny tables are targeted optimizations, not a generic answer to every slowdown. Diagnose selectivity, query plans, sharing, and locking first. Include Currency in supported types and the current 200-column cap. | [Skinny tables](https://developer.salesforce.com/docs/atlas.en-us.salesforce_large_data_volumes_bp.meta/salesforce_large_data_volumes_bp/ldv_deployments_infrastructure_skinny_tables.htm) |
| 20:25-27 | Salesforce Functions is retired; label the section historical rather than a current solution choice. | [Official Functions documentation index](https://developer.salesforce.com/docs/llms-salesforce-functions.txt) |
| 24:5 | Rates are not automatically a live feed, but API integrations can update them; manual human entry is not mandatory. | [CurrencyType](https://developer.salesforce.com/docs/atlas.en-us.object_reference.meta/object_reference/sforce_api_objects_currencytype.htm), [DatedConversionRate](https://developer.salesforce.com/docs/atlas.en-us.object_reference.meta/object_reference/sforce_api_objects_datedconversionrate.htm) |
| 25:7-9; 27:15-16 | Each entitlement process targets either Case or Work Order; a Case process does not also run on Work Orders. The original object restriction is correct but incomplete. | [SlaProcess](https://developer.salesforce.com/docs/atlas.en-us.object_reference.meta/object_reference/sforce_api_objects_slaprocess.htm) |
| 28:15 | 32-bit addressing describes IPv4, not all IP. IPv6 addresses are 128 bits. | [RFC 8200](https://www.rfc-editor.org/rfc/rfc8200.html#section-1) |
| 29:23 | Cases and Opportunities cannot be switched away from Controlled by Parent; that is not their OWD option. Contacts do have this option. | [Sharing architecture](https://architect.salesforce.com/fundamentals/platform-sharing-architecture) |
| 32:3,13,29 | External Services accepts OpenAPI 2.0.x/3.0.x JSON or YAML. The definition need not be output/hosted by the endpoint. | [ExternalServiceRegistration](https://developer.salesforce.com/docs/atlas.en-us.api_meta.meta/api_meta/meta_externalserviceregistration.htm) |
| 32:15 | Modern Named Credentials need External Credentials/principals and permission to use them. Endpoint/auth configuration alone is incomplete. | [Named Credentials glossary](https://developer.salesforce.com/docs/platform/named-credentials/references/named-credentials-reference/nc-glossary.html) |
| 34:7-9 | There is no universal 2 GB file limit. Current lightning-file-upload documentation allows 10 GB, with different Experience/API limits. | [File upload](https://developer.salesforce.com/docs/platform/lightning-component-reference/guide/lightning-file-upload.html), [ContentVersion](https://developer.salesforce.com/docs/atlas.en-us.object_reference.meta/object_reference/sforce_api_objects_contentversion.htm) |
| 34:18-36 | Cloud Files Connect uses OAuth 2.0, not universally OIDC. Validate connector-specific support rather than treating Box/Quip as standard Files Connect connectors. | [ExternalDataSource](https://developer.salesforce.com/docs/atlas.en-us.api_meta.meta/api_meta/meta_externaldatasource.htm) |

Except for the noted Data Security update, these material claims remain in the compared newer technical pages. Release-current documentation must take precedence over this report's captured values too.

## Completeness Inventory

This table records observed coverage, not an official exam weighting.

| Pages | Assessment / missing content |
| --- | --- |
| Home | Explicit outdated/work-in-progress warnings. TOC omits chapters 22-36 and has mismatched URLs for governance, Field Service, and shortcuts. |
| 00 Exam | Personal experience mixes with policy. No adequate current prerequisite/stage guidance; old tools and retake statements conflict with the newer source. |
| 01 Diagrams | Useful concepts, but original example diagrams are placeholders. Newer page adds examples and distinguishes recommendations from requirements. |
| 02 Licenses | Substantial notes but commercial assertions need release/SKU/edition/contract qualification; marketing bundle is not equivalent to user license or permission-set license. |
| 03 Org strategy | Useful outline; needs quantified constraints, cross-org identity/master data, residency/legal boundaries, and operational costs. |
| 04 Governance | Original Data Steward/Custodian definitions are blank; newer guide fills them. Add decision rights, ADRs, exceptions, quality gates, ownership, and metrics. |
| 05 Data security | No meaningful destruction coverage despite the promise. Backup is largely a vendor list; needs RPO/RTO, restore drills, relationships/files/metadata, legal holds, and backup retention. |
| 06 IAM | Detailed but has verified fundamental errors; device/asset/password sections are minimal or blank. Add lifecycle, deprovisioning, token validation, rotation, and least-privilege service principals. |
| 07 Mobile | Needs current product/runtime matrix, synchronization/conflicts, offline authorization, local-data protection, and token lifecycle. |
| 08 Integration | Broad but unsafe absolutes; lacks operational failure/recovery design, modern APIs, API-specific atomicity, and source authorization. |
| 09 Cryptography | Definitions need corrections and safe-use boundaries: authenticated integrity, nonce/IV modes, signatures versus encryption, and key compromise/rotation. |
| 10 Reporting | Thin coverage; add dashboard running-user security, analytics dataset authorization, reporting limits, refresh latency, and cross-system strategy. |
| 11 Sales Cloud | Many Contact, Person Account, Opportunity, Product, and CampaignMember headings are empty; lead conversion/forecasting/territory knowledge is insufficient. |
| 12 Service Cloud | Only heading and introductory sentence. Needs Case lifecycle, assignment/escalation, Omni-Channel, intake, console/CTI, messaging, and object relationships. |
| 13 CPQ | Promised object section empty; needs current product boundary and relevant quote/subscription/rule model. |
| 14 Field Service | Useful partial notes; incorrect object naming, incomplete provisioning and assignment/scheduling/offline model. |
| 15 MuleSoft | Heading only, including the newer guide. Needs API contracts, orchestration, deployment/HA, error handling, authorization, observability, and governance. |
| 16 LDV | Too much emphasis on skinny tables; needs selectivity/query plans, lookup/ownership skew, locking, load concurrency, archival, and measurable retention policy. |
| 17 Shortcuts | Heading/introduction only. Wiki filename says Google Slides; body says PowerPoint; newer Lucidchart page still has the PowerPoint placeholder. |
| 18 Presentation | Heading only in both versions. Needs requirement traceability, timing, audience-level explanation, Q&A, and self-review. |
| 19 Packages | Mostly vendor links; CTI section empty. Needs security/licensing evaluation, lifecycle, support, upgrade risk, and exit strategy. |
| 20 Limits | Image contains errors; configuration-limit content absent; Functions retired. Needs context-specific official source references and mitigation approaches. |
| 21 Oddities | Several verified generalization errors; object behavior must be release/API/object-qualified. |
| 22 Sessions | Settings inventory, not a tested session policy; needs user/app/IP scenarios and control applicability. |
| 23 PRM | Mostly MDF; missing onboarding, lead distribution, deal registration/conflict, external security, and delegated administration. |
| 24 Currency | Needs effective-rate governance, record/corporate/user currencies, reporting limitations, activation consequences, and accounting snapshots. |
| 25 Entitlements | Five implementation headings are empty; needs process type, hours, timing, completion, violations, and versioning. |
| 26 mTLS | General concepts but no current inbound/outbound/API support matrix, certificate lifecycle, or enforcement tests. |
| 27 Work orders | Very short; needs ownership, line-item access, status, appointments, parts, maintenance, and licensing boundaries. |
| 28 Networking | Basic introduction, with IPv4 generalization; needs CIDR/IPv6, DNS/NAT, ingress/egress, TLS termination, and actual DDoS/WAF defenses. |
| 29 Object/field/record security | No substantive CRUD/FLS treatment; explicit sharing, roles, teams, and territories are empty headings. |
| 30 Knowledge | Public visibility and numerous feature sections empty; authorization and publication/version/translation lifecycle underdeveloped. |
| 31 Experience | Guest sharing, translations, topics, CMS, moderation, gamification, mobile, registration, and discovery headings empty. Guest exposure is a serious omission. |
| 32 External Services | Schema and credential guidance need corrections; add permissions, supported contexts, fault handling, and versioning. |
| 33 Payments | Vendor-led; missing trust/PCI boundaries, tokenization, callbacks, idempotency, capture/refund, reconciliation, and native licensed alternatives. |
| 34 DMS | Incorrect universal size/protocol claims; needs file access, connector matrix, retention/hold, and provider licensing. |
| 35 Storage | No reliable edition/SKU-qualified allocation model; distinguish data from file storage and measure actual usage. |
| 36 Privacy | Consent model/enforcement/retention conflated; authorization-form/subscription model, identity linking, and elevated API permissions missing. |
| Notes for Improvement | Personal coaching notes, not policy. Blanket middleware advice should not drive architecture. |

## Follow-Up Verification Status

Do not silently treat these as either true or disproved.

- **Exam materials, now verified:** The official [Review Board guide](https://help.salesforce.com/s/articleView?id=005298996&type=1&language=en_US) lists a Google Doc scenario, Slides, Sheet, blank Lucidchart with Salesforce Shape Library, and onscreen timer; no other materials are allowed and Lucidchart templates are prohibited. The [Evaluation guide](https://help.salesforce.com/s/articleView?id=005299002&type=1) also explicitly prohibits AI/templates and requires an external camera. The original wiki's paper/phone guidance must not substitute for current instructions.
- **Eligibility/stages, now verified:** The Evaluation requires Salesforce Certified Application Architect and System Architect certifications. The Review Board requires a passed Architect Review Board Evaluation. Keep the stages distinct. Evaluation-pass validity and detailed maintenance/booking conditions still require confirmation.
- **Timing, now verified:** The Review Board guide states 3 hours preparation, 45 minutes presentation, and 40 minutes Q&A. The Evaluation states 60/30/30 minutes plus 15 minutes feedback. These segments do not establish total booking duration including breaks and administration. The newer community guide's 317-minute component sum is not official policy.
- **Fees/results/retakes:** Still unverified. Do not assert free section retakes, removal dates, fees, or result timelines without current primary evidence/candidate communication.
- **Licensing/storage, partly resolved:** [Current Help](https://help.salesforce.com/s/articleView?id=xcloud.admin_monitorresources.htm&type=5) lists Professional/Enterprise data storage as 10 GB/org plus 20 MB per eligible user license, and Performance/Unlimited as 10 GB plus 120 MB, except Lightning Platform Starter at 20 MB. Thus universal 120 MB/internal user is incorrect. Mixed/custom licenses, actual org entitlements, and the old 102 GB example require explicit assumptions. The current file-storage table does not establish a universal per-user increment. Other chapter 02 commercial claims remain unverified.
- **Experience roles, partly resolved:** [Official record-access guidance](https://developer.salesforce.com/docs/get_document_content/draes/draes_group_membership.htm/en-us/262.0) places customer/partner account roles beneath the account owner's role, not the enabling administrator's. This applies to role-based external models; high-volume users have a different model without roles/groups. The wiki's conflicting external-role-count figures still require current limit-taxonomy verification.
- **mTLS:** Chapter 26:18 describes event support as permanently unavailable; :34 generalizes ticket enablement and port 8443. Verify by direction, API, endpoint, and current release. Historical known issues do not establish permanent future behavior.
- **Payment PCI:** Chapter 33 implies package use makes an implementation PCI-compliant. Treat it as unsafe guidance pending provider/PCI review; do not assume compliance without merchant responsibilities and architecture controls.
- **Field Service provisioning, partly resolved:** [ServiceResource](https://developer.salesforce.com/docs/get_document_content/object_reference/sforce_api_objects_serviceresource.htm/en-us/264.0) explicitly requires the Field Service Dispatcher PSL for dispatchers; mobile LWC functionality has additional mobile permission requirements. Service Cloud User/Knowledge checkboxes alone are not sufficient. The complete contracted persona/license/PSL/permission matrix remains to be verified.
- **Currency, partly resolved:** [DatedConversionRate](https://developer.salesforce.com/docs/get_document_content/object_reference/sforce_api_objects_datedconversionrate.htm/en-us/264.0) supports updates; effective dating does not imply immutable rate records or an accounting snapshot. Detailed historical-edit/reporting behavior remains unverified.
- **Other unresolved specifics:** The universal 50-million-record archival threshold, full Flow callout context matrix, current Dynamic Forms eligibility, and commercial Knowledge/PRM packaging need further primary-source validation.

## What to Supplement

The missing curriculum is not merely more product trivia. Add complete treatments of:

1. Security: CRUD/FLS, sharing/license eligibility, guest access, restriction rules, least-privilege integrations, and identity lifecycle.
2. Data: migration sequence/external IDs, reconciliation, cutover/rollback, skew/selectivity/locking, retention, backup, and tested restoration.
3. Integration: semantics/atomicity, idempotency, retries, observability, event recovery, API/version contracts, and operational ownership.
4. Delivery: environment strategy, source control, CI/CD, testing, release sequencing, rollback, governance, and adoption.
5. Communication: requirements-to-decisions traceability, end-to-end narratives, timed diagramming, technical/business tradeoffs, and live challenge handling.

Agentforce and Data 360 are useful adjacent product awareness, but do not allocate major study time solely because they are newer products. Confirm the official scope. Salesforce's June 2026 preparation article emphasizes Sales, Service, and Experience Cloud as in-scope data models, and Revenue Cloud/Field Service as adjacent models.

## Recommended Source Hierarchy

1. Current Salesforce certification guide/candidate instructions for policy and scope.
2. Official Architecture Center fundamentals, decision guides, and Well-Architected guidance.
3. Release-current Developer/Help/Object Reference documentation for implementation facts.
4. Public standards for OAuth/OIDC/SAML/cryptography.
5. Community guide and coach advice for practice methods, explicitly identified as opinion.

Start with [Salesforce's June 2026 CTA resource guide](https://www.salesforce.com/blog/architecture-resources-for-cta/). It provides current links to fundamentals, data models, decision guides, and communication resources. Use the newly retrieved official [Evaluation guide](https://help.salesforce.com/s/articleView?id=005299002&type=1) and [Review Board guide](https://help.salesforce.com/s/articleView?id=005298996&type=1&language=en_US) for stage requirements, objectives, timing, and materials; recheck candidate instructions before booking.

Use the companion `cta-realistic-study-plan-2026-10-23.md` for an actionable schedule.
