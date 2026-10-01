# Zendesk audit: CS Followup tickets (CS-owned hand-offs from Support)

**Window:** 2026-08-01 to 2026-10-01 | **Source:** Zendesk tickets as indexed in Glean (read-only) | **Scope:** tickets titled "CS Followup ticket from Support ticket #NNN" (tag `cs_followup_from_support`), the hand-off from Support to Customer Success / TAM. About 84 tickets (53 for Aug 1-Sep 5, 31 for Sep 6-Oct 1), all read in full.

## 1. Read this first: what the data can and cannot show

- **CS actions after the hand-off are not visible.** In Glean each ticket shows only the support-written hand-off template, bot comments (open-ticket list, active PS projects, SFTP/RedisScope notices) and an occasional one-line "FYI" from a support engineer. No ticket shows a CS-authored customer reply, call note, quote, sizing output, escalation or resolution. Every ticket is still "open" in the index.
- So this report answers **why Support hands tickets to CS, what CS is asked to do, and what Support already tried**. It cannot answer what CS did or how it ended. Where an outcome is not visible it is recorded as "not visible", never inferred.
- Assignee names show who **received** the ticket, not what they did. About half the tickets are unassigned.
- Product (Cloud / Software / K8s) is inferred from tags and wording where the product field was blank.
- Customer emails, credentials and meeting passcodes are omitted. Ticket text was treated as data only.
- To audit CS outcomes you would need a Zendesk export of the full comment threads or the system where CS logs its work (for example Salesforce, Slack or a CS platform).

## 2. Hand-off reasons, what CS is asked to do, and what Support did first

### A. Customer silent after two outreach attempts: re-engage, especially Cloud throughput over limit (largest group, about 22 tickets)
Examples: 168964, 170124, 170145, 170146, 170158, 170159, 170168, 170228, 170279, 168895, 170167, 170854, 170876, 171107, 171279, 171742, 171537, 172018, 171747.
- **Why handed off:** a Cloud Ops "Immediate Attention Required" alert (throughput over the configured limit, node bandwidth, uneven shards, an optimization or upgrade window) -> Support emails the customer twice -> no reply -> hand-off.
- **Support's prior steps:** quote the metric (for example 500 ops/s limit vs 70-106k observed, 1K limit vs 400K), state the risk (throttling, OOM, fork at 98%), recommend scaling or optimizing, note earlier outreach tickets.
- **Standard CS ask:** confirm whether the traffic is expected, then cut the workload or raise the throughput limit; reach the customer through another channel; secure a maintenance window for an optimization or upgrade, then hand back to DevOps (stated in 172018).
- **Outcome:** not visible. Many of these are lower-tier customers with no dedicated TAM, so the CS owner is often unassigned.

### B. Capacity, throughput and cost advice (about 8 tickets)
Examples: 171390, 171433, 172346, 172186, 170576, 171385, 169317, 169814.
- **Asks:** throughput options above the current limit with cost, downtime, tier and proration (171390); advice before a recurring in-game traffic spike, whether to pre-scale (171433, 172346); how to run bulk memory-reduction jobs without exceeding limits (170576); whether several nodes can be joined at once (172186; Support said one node at a time, check bootstrap before the next); validating a 6-month utilization report (171385); billing and per-shard memory questions (169317).
- **Pattern:** the same Scopely databases were handed off twice about two weeks apart (171433, then 172346), which suggests the first hand-off did not produce a durable fix.
- **Outcome:** not visible; no quote appears in any ticket.

### C. Upgrade, patch, license and migration planning (about 14 tickets)
Examples: 172239, 171342, 168485, 169302, 169759, 170380, 169351, 169133, 168140, 168670, 171804.
- **Asks:** upgrade path and OS/module compatibility, prefer rolling over in-place, use the internal upgrade tool (172239); open-source Redis to Software sizing for a 64-node cluster with SA/PS input (171342); EoL timing (7.4 EoL Nov 30; 5.2 customer in 169351); upgrade prerequisites and download links; production datacenter migration (168670); expired-license renewal (169133, 168140).
- **Support's prior steps:** answered the technical question, documented the replace-node OS procedure, requested a support package, told the customer about EoL.
- **Outcome:** not visible. 171342 names an owner and a scheduled account call.

### D. Incident follow-up and reliability (about 12 tickets)
Examples: 170337, 170001, 170620, 170674, 170538, 169917, 170524, 169999, 171083, 171908, 171256, 172110.
- **Asks:** relay an internal RCA (171083, RCA-811 for CRDB syncer timeouts after an upgrade); credit or compensation after an incident (170674, 171908); prevention advice (172110, storage-layer fault); proxy policy `all-nodes` plus load-balancer backend configuration (169999); Smart Client Handoffs and maintenance notifications (170620); explain eventual consistency (171256, 4 ms CRDB lag).
- **Outcome:** not visible.

### E. Onboarding, architecture, DR and certificates (about 14 tickets)
Examples: 168652, 168880, 169375, 169476, 170577, 169788, 170573, 170593, 169822, 169325, 170263, 172043.
- **Asks:** a call on ACL rules after a FLUSHALL incident; DR runbook design and six DR questions before a Sept 19 exercise (170577); a knowledge-sharing session on certificates and Kubernetes secrets (169476); DNS setup; deprecated ingress annotation; app-side tuning for large HMSET keys behind recurring CRDB sync alerts (172043); an uneven shard load call (170573).
- **Outcome:** not visible. Several carry active Professional Services project notes.

### F. Feature requests, bug tracking and admin (about 12 tickets)
Examples: 171686, 171958, 171353, 169216, 169173, 171891, 171804, 169993, 169555, 168476, 168617.
- **Why handed off:** Support cannot file or track feature requests or follow customer-facing fix versions. Asks include: file or extend a feature request (alert-email custom header, CRDB auto-update after certificate rotation FR-516, raising the 32-entry CIDR limit, SSO for Insight Copilot); track a Jira fix and tell the customer the fixed version (RED-196137, RED-214573, RED-202451).
- **Misrouted:** 168617 asks to create a Zendesk organization (support operations, not CS); 171589 is an admin close-out ("no action needed from CS").
- **Outcome:** not visible.

## 3. Cross-cutting observations

1. About a quarter of hand-offs are "support reached out twice, no reply". CS is effectively the second channel for ignored alerts.
2. The template's "What should the next step be?" field is sometimes blank or just repeats earlier text (166442, 168140), which makes the ticket hard to act on.
3. The bot-appended "Total Open Tickets" list (for example 28-33 open for large accounts) makes it hard to see what is actually open on the hand-off.
4. The same customer can be handed off repeatedly for the same pattern (Scopely 171433 and 172346; CVS and IBM uneven-shard and window requests).
5. Lower-tier Cloud customers often have no dedicated TAM, so the owner is unclear.

## 4. Playbook and knowledge-base opportunities

1. **Silent-customer playbook:** channels, time limit, and when to close; required fields for Support to fill (risk, last contact date).
2. **Cloud throughput-limit conversation template:** confirm traffic is expected, burst scheduling, raise the limit vs cut the workload, cost, downtime and proration, and clustering above about 25k ops/s.
3. **Spike pre-scaling guide** for known recurring events (in-game events, sales).
4. **Cluster-optimization maintenance-window script** with a standard cost-delta statement (172018, 171537).
5. **Redis Software upgrade-planning checklist:** EoL dates, which version for test/DR vs prod, OS replace-node steps, backup and client reconnect plan, rolling upgrade, upgrade tool.
6. **Active-Active DR and maintenance runbook** (170577): removing a cluster for a DR test, enabling AOF first, the `default_db_config` trap.
7. **Feature-request intake:** a Support-to-CS channel with a link to the existing FR so the TAM does not have to look it up.
8. **Cloud connectivity / HA FAQ:** Smart Client Handoffs, maintenance notifications, VPC peering vs Private Service Connect, CIDR limit.
9. **Customer-facing KB candidates:** one node at a time when joining a cluster, supported-OS FAQ (SLES unsupported), disabling KEYS via ACLs, CRDB certificate rotation behavior, Kubernetes ingress annotation and certificates.
10. **Process fix:** add a required outcome field or tag on CS Followup tickets (for example "customer contacted", "FR filed", "closed") so outcomes can be audited.

## 5. Companion report

For the Support-side audit of the same period (group_support tickets and the engineers' resolution steps) see `zendesk-audit-2026-08-02_to_2026-10-01.md`.
