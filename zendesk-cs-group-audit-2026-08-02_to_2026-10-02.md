# Zendesk audit: tickets owned by the CS group (assigned to CS engineers)

**Window:** created 2026-08-02 to 2026-10-02 | **Source:** Zendesk API via Zapier (group `CS`, id 360000253313), full comment threads via `all_comments` | **Read-only.**

## 1. Scope and coverage

- **Definition used:** tickets whose Zendesk **group is CS** and that are assigned to a CS engineer (the "CS / Satenik" style of ownership). This is a different, much larger set than the earlier "CS Followup" template tickets.
- **Tickets found:** 245 in the CS group. By category: 178 customer-opened or other tickets, 35 "CS Followup" hand-offs from Support, 18 automated "Missing License" tickets, 14 other API-created tickets.
- **Read in full:** about 210 of the 213 customer-opened and CS Followup tickets (five agents; none reported an unreadable ticket; the batch files had 213 IDs and the agents reported 210 read, a mismatch I did not resolve). The 32 automated and API tickets were counted but not read.
- **Gaps in the pull:** the Zendesk pulls for a few days timed out and were retried in smaller windows; weekend days with no tickets are normal. Treat counts as approximate (within a few tickets).
- **By assignee (all 245):** Pushpanjali Chauhan 33, Chatur Kaur 33, Amit Kumar 28, Alejandro Rosales Muñoz 28, Satenik Safaryan 28, Pooja Kumari 20, Rudy Gonzalez Lobo 19, Yashika Singh 19, Shefali Parmar 18, Ximena Gdur Vargas 14, unassigned 5.
- **Status (all 245):** 142 closed, 49 solved, 21 pending, 21 open, 12 hold.
- **Limits:** Slack, calls and meetings are not visible, so work done there appears as "internal note" or nothing. About 15 tickets closed because the customer stopped replying, so whether the advice worked is unknown. Support engineers' work that preceded or replaced CS work is noted where the thread shows it.
- **Data hygiene:** tickets 169277 and 171372 contain credentials or meeting passcodes in comment text, and 168732 and 170237 contain download-link passwords. They are omitted here and should be scrubbed in Zendesk.

## 2. Use cases: what customers ask and the steps CS takes

### A. Support hand-off ("@CS" note or "CS Followup ticket") (about half of all tickets)
Examples: 168085, 168893, 169173, 169302, 170168, 170279, 170337, 171107, 171390, 171200, 172244.
- **Sequence:** Forethought bot and Support triage -> Support tags `@CS` or the CS Followup ticket is created with a summary -> CS sends a templated acknowledgement -> CS asks for information or a support package, or gives an answer / routes it -> one to three "checking in" messages -> closed as "no response".
- **Proactive-alert variant (throughput over limit, uneven shards, Lua crashes, high traffic):** Support got no customer reply, so CS calls or emails the customer, often the same templated email 3-5 times, then closes (170159, 170337, 171107, 171390, 171279). Outcomes are rarely recorded in the closure note.
- **Strong hand-offs** carry a concrete next step (168670, 170674); weak ones just say "reach out" (170158, 171322).

### B. Health checks (Redis Software and Kubernetes)
Examples: 168793, 169428, 169519, 169865, 169962, 170774, 170765, 171021, 171061, 171330, 171340, 172309, 172568.
- **Sequence:** customer or CS asks -> CS sends the support-package upload link -> the analyzer (RedisScope) processes it -> CS writes findings (expired certificates, no replication, backups off, KEYS in the slowlog, version out of support or EOL Nov 30, single node, stale shard config) -> solutions architect reviews -> call -> follow-ups. Amit Kumar often starts these himself.
- **Where it breaks:** customers often never upload a package (170236, 170436, 171622); when something is broken the real diagnosis comes from Support/L3 (169428, 169865, 172568); several annual checks sat 22-26 days with no findings (171017, 171019, 171061, 169962).
- **Best examples:** 168793 (certificate renewal across about 9 clusters by screen-share), 171330.

### C. Upgrade, OS and version planning (Redis Software)
Examples: 168858, 169302, 169584, 169782, 169818, 169960, 170237, 170380, 170414, 171008, 171580, 172244, 172315.
- **Sequence:** confirm versions and the support matrix -> check with Product in #ask-pm where unsure -> recommend a path (direct to 8.2, staged 6.2 -> 7.8 -> 8.2, rolling upgrade, `rladmin upgrade db ... preserve_roles`, in-place vs replace-node) -> pre-upgrade checklist (backup, certificate, port, F5/LB warnings) -> offer a call; Support supplies the installer.
- **Typical timing:** same-day to one day for most; 169818 took 8 days; customers rarely reply afterward.
- **Best example:** 171008 (custom staged runbook).

### D. Certificates, DR, HA and migrations
Examples: 168750, 169051, 169234, 169411, 170139, 170330, 171016, 171186, 172348, 172565.
- **Steps:** rotation order (proxy/syncer via `crdb-update`, internode certificates), replication checks, Replica-Of vs Active-Active guidance, quorum advice, rolling node replacement, test in non-production first; L3 validates high-risk production procedures before they go out (172348); Zoom/Teams calls with a recap.
- **Gaps:** 171186 (DC-to-DR switchover) closed with the question unanswered; 172565 urgent bank DR licence expired with no CS comment.

### E. Licensing and contracts
Examples: 168140, 168566, 168587, 168771, 168878, 169089, 169215, 169644, 170905, 171608.
- **Steps:** ask for cluster FQDNs or packages -> generate or reissue licence files (the model case is 169215: 6 licences reissued, review scheduled) or route to the solutions architect / account team / licensing team. Production keys may need approval (168587).
- **Gaps:** sales-routing tickets show no named contact for the customer; 168771 second licence took about 7 days; 169089 first CS reply took 7 days.

### F. Billing, credits, refunds, startup programme and Essentials overage (Redis Cloud)
Examples: 168051, 168185, 168476, 168918, 169846, 170024, 169315, 169362, 169555, 169673, 169879, 170640, 170674, 170996, 171033, 171319, 171536.
- **Steps:** review the invoice or cost report in the console, consult the billing peer, then give a policy answer. Refunds are almost always declined (168051, 168185, 170996) and startup credits or extensions refused because the programme is paused (169315, 169673, 168043). Credits require an approval flow (170674: $100 credit approved after SLA review, about 18 days). Back-end admin actions only CS can do: switch a payment method (169555), unmap a marketplace contract (169990), invite a customer to an account so they can cancel (171536).
- **Essentials overage ("High Usage Fix" process: 168918, 169846, 170024):** templated email resent about 3 times, then a check that usage is back within limits or the database was deleted; customers dropped usage, deleted the database, or moved to Pro.
- **Result:** refund denials led to CSAT 1 (168051, 168185); unanswered refund or usage-limit questions recur (171536, 169555).

### G. Security, CVE and compliance
Examples: 169030, 169277, 169880, 170891, 171029, 171573, 172130, 171968.
- **Steps:** confirm with the security team or Product, explain version vs managed patching, give a mitigation and the fixed version (169030 corrected ACL rule; 172130 minimum version 7.8.6-286). Compliance questions are posted to Slack and answered the same day (171029).
- **Gap:** HIPAA BAA request (171968) got only a generic acknowledgement after 4 days.

### H. Product how-to, architecture and capability questions
Examples: 168537, 168757, 169588, 169832, 170033, 170360, 170671, 171610, 171658, 172016, 171335.
- **Steps:** one researched written answer with docs, then close. Strong when specific (exact `rladmin` command in hours, 169832). Forethought sometimes answers first with the wrong product (170225 Cloud answer for on-prem) or inaccurately (170402, 171033), and CS corrects it.
- **Roadmap or undocumented items stay unanswered** (170402, 172296).

### I. Account access and admin
Examples: 168917, 171445, 172626, 168247.
- **Steps:** verify identity (payment evidence), enable the setting or send an invite. 171445 granted Owner access after identity checks that look thin.

### J. Programmes, quotes, training and non-technical
Examples: 168492, 169773, 170118, 170442, 171339, 171571, 172096, 172341.
- **Steps:** route to the programme, sales or account team and tell the customer. 168492 waited 29 days on the programme team; 169773 still open about six weeks.

## 3. Who handles what

| Engineer | Typical work | Notes |
|---|---|---|
| Pushpanjali Chauhan | Widest range: K8s/OpenShift, upgrades, certificates, security, health checks, billing, overage, handoff closures | Thorough and quick write-ups; best CSAT in several batches; many closures are internal-only. |
| Chatur Kaur | DR/HA, certificates, long-running enterprise cases, intros, CS Followups | High-quality answers; slow on follow-ups (11 days on 170168, 6 on 169030); quiet closes when the customer is silent. |
| Amit Kumar | Health checks, proactive templates, Essentials overage, billing escalations, sales routing | Largest health-check owner; resends the same template several times. |
| Alejandro Rosales Muñoz | Licences, upgrades and runbooks, TLS/certificates, Spanish and Portuguese customers | Best custom runbooks (171008, 169960); some licence delays. |
| Satenik Safaryan | Product/security questions, upgrades, pricing and scaling, programme and account-team hand-offs, trust/compliance | Fast, detailed answers; declined same-day calls in 169781 and 169865. |
| Rudy Gonzalez Lobo | K8s, CVE, upgrade guidance, billing and marketplace, takeovers | About 2-day replies but long gaps between rounds. |
| Yashika Singh | Billing and startup programme, networking (PrivateLink), certificates, health checks, DR cutover | Good documentation; uses a standard chaser. |
| Pooja Kumari | Short how-tos, licences and access, K8s/OpenShift, CVE/version, RFQ routing | Fast on short items; slow when waiting (169818). |
| Shefali Parmar | Billing/admin actions, licences, Cloud advice, K8s | Strong on admin actions; several slow first replies (6-10 days). |
| Ximena Gdur Vargas | TAM for named enterprise accounts: health checks, certificates, licence review | Excellent when engaged (168793, 169411); long silences on 169596, 171019, 171061. |

## 4. Gaps and risks

- **Urgent or unowned now:** 172565 (bank DR licence expired Sep 24, assignee on leave, customer chasing daily); 172669, 172626, 172635 unassigned with no CS action; 172568 and 172296 open with no CS reply.
- **Slow first CS reply (6+ days):** 168085 (14-day gap), 168185, 168476 (12 days), 168487, 168842 (9 days after Support's fix), 168893, 169030, 169089, 169673 (13 days), 169818, 170001 (14 days), 170158, 170168, 170263, 170495, 170640, 170996, 171168, 171536 (7 days to invite), 171571.
- **Health checks without findings after 3+ weeks:** 171017, 171019, 171061, 169596, 169962.
- **Never answered or only acknowledged:** 171186, 170776 and 170774 (certificate question), 172113, 171968, 172066, 172531, 171339.
- **Wrong or conflicting information:** 171948 (`/v1/metrics`, corrected later), 169990 (conflicting billing messages), 170263 (reply after the freeze window had begun).
- **Closed without customer confirmation or only internally:** 168331, 168487, 169783, 169351, 169814, 168247.
- **Bounced between people or groups:** 169758 (three engineers; customer complained), 169782, 171417, 168023, 168670, 171168, 170236.
- **Dependency risk:** single-owner long threads (169234 escalated when the owner was on leave).
- **Access gap:** CS cannot read the support-log S3 bucket (172048) or sometimes billing data (168476), so Support or DevOps must pull it first.
- **Duplicate or template fatigue:** the same template resent several times (168758, 169352, 171107, 171319, 171390); duplicate tickets merged (169121/169089, 171571/171339, 168153/168043).

## 5. Playbook and knowledge-base opportunities

1. **Hand-off SLA and fields:** a first-reply target for `@CS` and CS Followup tickets, required fields from Support (risk, last contact date, concrete next step), and auto-assignment so tickets are never unowned.
2. **Silent-customer playbook:** channels, time limit, and a closure note that records the outcome.
3. **Health-check workflow:** one owner, a findings template, a target turnaround (the best cases are 2-3 days), and a rule for when no package arrives.
4. **Upgrade planning checklist and runbook library:** supported paths, rolling vs in-place, certificates and ports, F5/load-balancer warnings, EOL dates; reuse the staged runbook from 171008.
5. **Certificate rotation guide:** proxy/syncer/internode order, ready-made responses (169411, 170139).
6. **Billing policy macros:** refund and credit decisions, startup-programme status, Essentials overage steps, marketplace unmapping, with the approval path for credits.
7. **Licence process:** who issues, expected turnaround, FQDN/cluster-name requirements; put a named contact on every sales-routing hand-off.
8. **Cover for leave:** a backup owner for long-running and urgent tickets.
9. **Bot accuracy:** check Forethought answers for product (Cloud vs Software) and for Essentials/Pro and Lua/ops-counting details.
10. **Customer-facing KB candidates:** per-database alert recipients (account-level, email only), Essentials ops/bandwidth counting and reset date, Vercel/Heroku add-on limits (no TLS), OSS cluster API with NAT, Starlette minimum Redis Software version, one-node-at-a-time cluster joins.

## 6. Related reports

- `zendesk-audit-2026-08-02_to_2026-10-01.md`: Support-group tickets and engineers' resolution steps.
- `zendesk-cs-followup-audit-2026-08-01_to_2026-10-01.md`: the CS Followup template tickets as seen through Glean (superseded here, since the CS replies were not visible there).
