# Incident Report

**5-6 October 2026**

Published 6th October 2026 at https://legal.ii.corgi.st/05_10_26_organization/REPORT.md *(source code at https://github.com/iimenu/transparency/blob/main/05_10_26_organization/REPORT.md)*

---

**1. Capacity & What happened.**

I, **Florian Kolb** (known as github.com/**corgisolutions**, t.me/**florcorgi**) [**corgi@aster.cx**], write as an authorized representative of the operator of ii Reborn (iistupid.com, github.com/iimenu), which has authorized this disclosure.

On **5 October 2026**, the GitHub organization "**iireborn**" was taken over by a former member, **TheUselessCreator**, together with an external party, **heycanihavethis** (assuming "snake" or "lucent"), who had never been a member of the organization. The owner of the GitHub account heycanihavethis owns the repository "Poison" and is a primary contributor to it. Poison, formerly known as Seralyth, is a competing fork of the same menu. The accusations of malware against this project by that team are documented in the transparency record at [27_09_26_poison](../27_09_26_poison/README.md). This incident extends those accusations from code claims to direct action.

During the incident, King, the sole organization owner, was removed from the organization. The repositories were defaced, the transparency and legal documentation was deleted, a release binary was replaced, and production services were disrupted. On 6 October, GitHub Staff disabled the repository iireborn/menu. On 5 October, a bot inside our Discord server deleted channels, including the general chat. The bot was quarantined after two deletions and banned. All operations have been moved and are running normally.

**2. What you need to do.**

If you used the menu at any point before 6 October, reinstall it from the new release. That is the entire instruction. No other action is needed.

The current release requires a cryptographic signature for every update action. The release shipped on 5 October, three hours before the incident began, already carried this enforcement. It was the last release to go out before the takeover, and it is not affected by anything described in this report.

**3. Access.**

TheUselessCreator was removed from the organization at the start of October, following the removal of another member on 3 October for unrelated reasons documented in [03_10_26_telemetry](../03_10_26_telemetry/DISCLOSURE.md). At the time of the incident, **TheKing13245** (further: **King**) was the sole owner. No legitimate member of the organization re-added TheUselessCreator or invited heycanihavethis.

Two facts narrow how the access was regained. First, only an owner can add or re-add members, and King, the only owner, was asleep with his computer off during the incident window. Nobody inside the organization could have performed the action due to member status. Second, the organization enforced two-factor authentication, and no member account was compromised; no credentials, sessions, or authentication events were affected.

We believe the access was restored through GitHub Support, by a request that misrepresented the circumstances, and we have asked GitHub to confirm whether a support request touching the organization was processed between 3 and 6 October, and what was verified before any change was made. We hold no audit logs, so this is a belief and not a finding, but the alternatives are excluded: not billing, because the organization was on the free plan and no payment existed to verify against; not an invitation, because invitations expire and none was pending; not an application, because none was installed. Misrepresentation to a support channel is the only mechanism consistent with the facts. GitHub's own Impersonation policy states that "you may not misrepresent your identity or your association with another person or organization", including "accessing an account or organization with another user's token or credentials". We believe this constitutes exactly that.

**4. Timeline.**

All times UTC. Commit hashes are from the repository state preserved in the mirror, cloned during the incident.

- **Early October.** TheUselessCreator is removed from the organization. King is the sole owner. The organization has 2FA enforcement and no installed applications.
- **5 October.** Release 1.2.0 ships with cryptographic signature enforcement on all update actions.
- **5 October, later.** TheUselessCreator's account regains organization-level access. King is removed as owner. heycanihavethis, never a member, is granted access.
- **5 October.** The transparency repository is defaced and its contents deleted, including the legal correspondence with Goldentrophy Software, the GPL compliance record, the Poison chat transcript, and the telemetry disclosure. Commits `2ad9e43`, `a4f3a1c`, `d4494fd`, `40f5e3d`, `1ce04ec`, `85fbd81`.
- **5 October.** The menu repository README is replaced with defamatory text and links to two Discord servers. The repository files are erased.
- **5 October.** The release binary is replaced with a different DLL. The original release assets are deleted. In iireborn/metadata, commit `be84275` flips menustatus.json to false, kill-switching services that read it. In iireborn/menu, menuversion.json is set to 9.9.9 with a hash pointing at the substituted DLL, so that auto-updaters would always report an update available and download it.
- **5 October.** In the Discord server, the bot ii.Stupid.Menu#7483 deletes the `🦍 general` channel and the `📌 START HERE` channel. The bot is quarantined by automated systems after two deletions, the damage is contained, and the bot is banned. We do not own that application.
- **6 October.** We detect the incident and immediately publish a community alert warning that the endpoints must not be trusted and the menu must not be run until further notice due to potential malware campaigns.
- **6 October.** GitHub Staff disable iireborn/menu.
- **6–7 October.** Repositories are cloned during the incident and mirrored to **github.com/iimenu**. Endpoints are repointed. The substituted binary is no longer reachable through any endpoint we control.

**5. The substituted binary.**

The substituted DLL was reviewed during the incident and displayed defamatory messages repeating claims that a developer is a "ratter" and had "manipulated" the owner. It contained no auto-update functionality, which would have kept any user who installed it on the substituted build permanently, unable to receive the legitimate release. We identified no other functionality in the review. The binary itself sits on the disabled repository and we no longer hold a copy, so this review is recorded as it was performed, and no further analysis is possible from our side.

We believe replacing a release binary that auto-updaters download and execute, by falsifying the version manifest and its hash to point at it, constitutes tampering with a software distribution channel, within the meaning of 18 U.S.C. § 1030(a)(5)(A), which prohibits knowingly causing the transmission of a program and intentionally causing damage to a protected computer without authorization. The machines of users who pulled the build during the window are protected computers under § 1030(e)(2)(B), and the substitution is damage under § 1030(e)(8), which includes "any impairment to the integrity or availability of data, a program, a system, or information".

**6. The other actions.**

Regaining access after removal, and removing the sole legitimate owner, we believe constitutes unauthorized access under 18 U.S.C. § 1030(a)(5)(C), which prohibits intentionally accessing a protected computer without authorization and causing damage and loss. The Supreme Court in *Van Buren v. United States*, 593 U.S. ___ (2021), narrowed "exceeds authorized access" to cover insiders who misuse access they still hold. That narrowing does not reach this incident, because the access was not misused, it was reacquired after being revoked. *United States v. Nosal*, 676 F.3d 854 (9th Cir. 2012), distinguishes access restrictions from use restrictions; a removed member regaining entry circumvents an access restriction.

The deletion of the transparency repository we believe constitutes destruction of records, and, if made in contemplation of an investigation or with knowledge that the deleted material itself documented conduct that could be investigated, may engage 18 U.S.C. § 1519, which prohibits knowingly destroying records with intent to impede or influence the administration of any matter within federal jurisdiction. We record the deletion's target precisely: the correspondence, the compliance documentation, the chat transcript in which the same parties conceded they could identify no malicious code, and the telemetry disclosure. Deletion in git is a commit. Every deleted file is recoverable from history, and the record now shows both the original documents and their deletion, with authors and timestamps.

The kill switch flip and the forced version change we believe constitute disruption of production services and impairment of availability, within § 1030(a)(5)(A) and the definition of damage in § 1030(e)(8).

The Discord channel deletions were performed by a bot holding a token with elevated permissions in our server. The bot is not our application; its token had been held by TheUselessCreator on his backend, and we cannot determine who held or used it at the moment of the deletions. We believe using a bot token to delete channels in a server one no longer belongs to constitutes unauthorized access and intentional damage in its own right, and it fits the same pattern as the organization takeover in its timing and its direction.

If access was restored through misrepresentation to GitHub Support, we believe this constitutes a scheme to defraud executed through interstate wires, within 18 U.S.C. § 1343. This belief is stated in section 3 and is conditional on confirmation from GitHub.

**7. What was not affected.**

The domain, the website, payment processing, and user accounts were not touched. The incident was contained to the GitHub organization and the Discord server's channel structure. The menu source code was not modified below the level of defacement, and the git history preserves every deletion, this is a property of git, and the mirror carries the full history forward.

The kill switch is a status flag that clients read. Flipping it disabled services that check it. It does not execute anything on any machine and cannot.

**8. What changed.**

Release signing was already enforced as of 1.2.0, hours before the incident. All distribution and update endpoints are repointed away from the old organization and verified against known-good builds. The mirror organization is under our sole control, with 2FA enforcement, a single owner, no pending invitations, and no installed applications. The bot responsible for the channel deletions has been banned, the general channel has been recreated, and a full audit of remaining bot tokens and integrations in the server has been completed. Restoration of the original organization has been requested from GitHub; the incident has been reported; audit logs and the access mechanism question have been requested.

**9. Record.**

The transparency repository is restored from git history in the mirror, including the documents deleted during the incident, together with this report. The evidence is the commit hashes, the preserved history, and GitHub's internal records. We ask nothing of the community beyond section 2.

Nothing in this report waives any right or remedy, all of which are expressly reserved.

Florian Kolb (corgisolutions), Authorized representative for the operator of ii Reborn (iistupid.com). End.
