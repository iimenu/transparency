# ii Engine telemetry disclosure

## 1. Scope

I, **Florian Kolb** (known as github.com/**corgisolutions**, t.me/**florcorgi**) [**corgi@aster.cx**], write as an authorized representative of the operator of ii Engine, which has authorized this disclosure. On 2 October 2026 I reviewed the engine's source repository and found a telemetry component that collected personal data the application did not disclose and had no consent step. This document records what it was, what it collected, what the commits and settings say, an access I made to the collected data, and which legal provisions the findings engage. Line numbers refer to the version reviewed that day (commit `ae1a79423fc9543fa3e03b74436d95026d0b86a9`).

## 2. The breach

In the course of the review recorded in this document, **I, Florian Kolb (also known as github.com/corgisolutions, t.me/florcorgi),** obtained a bot token from a staff member and used it to **reach the telemetry channel and the raw addresses and journals** described in section 3. The controller had not authorized that access. Article 4(12) counts as a personal data breach a breach of security leading to the "unauthorised disclosure of, or access to, personal data"; **this was one**. The only barrier to it was a single informally shared token, and anyone holding that token had the same reach, and nothing would have shown it. I record it here myself, first, and it was reported to the controller immediately. Articles 33 and 34 attach to this breach; the access-control failure it demonstrates is the one counted under Articles 5(1)(f) and 32(1).

## 3. The collection

Three files implement it: `apps/desktop/src-tauri/src/telemetry_host.rs`, `apps/desktop/src/telemetry.ts`, and `services/api/ii_api/telemetry.py`.

**3.1 Machine identity.** The native layer reads the Windows account name from the `USERNAME` environment variable and the computer name from `COMPUTERNAME`, then `HOSTNAME` (`telemetry_host.rs:47-65`). Both are stripped of control characters, collapsed to single spaces and capped at 64 characters (`:31-45`). They are sent as `pc_username` and `hostname`.

**3.2 Public IP.** It asks `api.ipify.org`, then `ipv4.icanhazip.com`, then `ifconfig.me` for the machine's public address, each with a 4-second timeout (`telemetry_host.rs:284-311`). Only a public IPv4 result is accepted; private, loopback, link-local and similar ranges are rejected (`:67-82`).

**3.3 The second address.** The app runs `powershell -NoProfile -Command "Get-NetIPAddress -AddressFamily IPv4 -ErrorAction SilentlyContinue | Select-Object -Property IPAddress,InterfaceAlias | ConvertTo-Json -Compress"` and reads the adapters from the JSON (`:114-127`). Adapter names are matched against a list of VPN products and tunnel drivers: vpn, wintun, wireguard, nordlynx, nordvpn, mullvad, proton, openvpn, tap-windows, tap-win, tun, tunnel, zerotier, hamachi, radmin, surfshark, expressvpn, cloudflare warp, warp, tailscale, utun (`:85-112`). A matching adapter is marked as a tunnel and left out of the probe (`:151-157`). For each remaining adapter, the app sends a STUN binding request from a UDP socket bound to that adapter's address to `stun.l.google.com:19302` or `stun1.l.google.com:19302`, with 900 ms timeouts (`:194-282`). It parses the reply itself, MAPPED-ADDRESS and XOR-MAPPED-ADDRESS per RFC 5389, and accepts only public IPv4 results (`:209-254`). If no adapter-bound attempt succeeds it retries from the default route (`:366-370`); if STUN then fails but the HTTPS lookup worked, the second address is set equal to the first (`:371-374`). The result is sent as `direct_ip`.

**3.4 VPN detection.** The app flags `vpn_suspected` when a tunnel-named adapter was seen, or when the first and second addresses are both public and differ (`:313-339`). A label travels with the flag: `vpn_exit_and_direct_differ`, `vpn_adapter_or_tunnel_suspected`, `single_public_ip`, or `ip_unavailable` (`:324-332`).

**3.5 The client payload.** Every upload carries app version, platform string, `pc_username`, `hostname`, `public_ip`, `direct_ip` and `vpn_suspected` (`telemetry.ts:45-55`).

**3.6 The session journal.** While the app runs it records feature-usage counts (`trackFeature`), AI prompts and responses (`recordAiTranscript`), and errors (`reportEngineError`). At the end of a session it renders the journal into text: a header carrying `reason`, `started`, `ended`, `app`, `platform`, `pc_username`, `hostname`; the action and AI entries; any BepInEx error lines it can extract; and the BepInEx log output; capped at 500,000 characters (`telemetry.ts:244-273`). The journal is also kept in the webview's local storage, so a session that ended without uploading is flushed at the next launch (`:102-109`, `:297-325`).

**3.7 Uploads and schedule.** At session end (kind `launch`) or when a Studio session ends (kind `studio`), the app POSTs the journal to `/v1/telemetry/session-log` together with the game path, up to 400 characters, the feature counts, the extracted BepInEx errors and the payload from 3.5 (`telemetry.ts:275-295`; `telemetry.py:43-55`). It also POSTs a smaller payload to `/v1/telemetry/hourly` about 2 minutes after startup and hourly after that while the app is open (`telemetry.ts:327-354`; `telemetry.py:58-68`). When the telemetry preference is `false`, nothing is sent (`:94-96`).

**3.8 What the server does with it.** `telemetry.py` validates the addresses, public ranges only (`:104-114`), adds the address it observes from the connection itself (`:100`, `:130`), and resolves the pair into two fields: `root_ip`, described in the source as the "best-effort non-VPN / direct public IP when available", and `vpn_ip`, the exit address when a VPN is suspected and the two differ (`:117-144`). It stores the session and per-user feature events, including an event named `root_ip` carrying the resolved non-VPN address (`:340-346`). It then posts a summary and the full journal as a file to the private staff Discord channel (`:252-257`, `:384-396`). The file is named `session-{kind}-{Discord user id}-{timestamp}.txt` (`:357`). Its header contains the kind, the user's Discord name and ID, their entitlements, app version, platform, `pc_username`, `hostname`, `ip`, `vpn_ip`, `root_ip` and `vpn_suspected` (`:358-369`). A server-side loop builds an hourly rollup for the same channel (`application.py:81`). Daily per-user budgets cap session-log uploads and hourly pulses (`telemetry.py:278`, `:425-427`).

**3.9 Defaults.** The telemetry preference starts as `true` (`store.ts:76`) and the check treats anything but an explicit `false` as on (`telemetry.ts:94-96`). The first-run tutorial has 8 stops, welcome, launch, health, mods, community, tracker, studio and ready, and none of them concerns telemetry (`Tutorial.tsx:25-92`). The only control is a checkbox in Settings that starts checked (`App.tsx:1646-1665`).

## 4. The commits

Subjects are verbatim.

| Commit | Date (UTC) | Subject |
|---|---|---|
| `37883872d1d7f300d8446cd19b3d0d354e2792a4` | 2026-09-20 00:52 | Add telemetry, usage stats, and community creator approvals |
| `81fe0a58d14bab79dc25f9879c162dc937b6823d` | 2026-09-20 00:52 | Widen the engine UI and rework mods, chat, and Studio setup |
| `3327fc420f0c601bb3c34316b8b56881d494638a` | 2026-09-22 00:28 | Add community /ai, Home menu assistant, and richer Discord telemetry |
| `e9612c0721af7c4bf0ca902c77579586cb24029c` | 2026-09-22 03:14 | Session telemetry with AI transcripts, membership cache, home AI fallback |
| `178b9ae5ca3243bed8d4d6a3f3a9fe26cfecd5a5` | 2026-09-29 12:23 | Improve telemetry digests with BepInEx errors and hourly unique-user rollups |
| `7cc1a6a96fd4b1e48d2beb6d37293114cb10eaec` | 2026-09-29 21:11 | Send expanded hourly telemetry to a dedicated Discord channel. |
| `51be278ca1f939fc58d71e00f4619101e5e00b9c` | 2026-09-30 11:47 | Include PC username, hostname, and client IP in telemetry. |
| `a51f6aae5f739e279c1a215adbfa4526cadd2555` | 2026-09-30 11:53 | Keep telemetry host identity to public IP only. |
| `06e51845a90fe05a6af9ccf8250f29f2c053ebbe` | 2026-09-30 11:55 | Restore PC username and hostname alongside public IP in telemetry. |
| `3f59ff608769da214e13ba574a8e5d999267e9d8` | 2026-09-30 23:28 | Report VPN exit and best-effort root IP in Engine telemetry. |
| `71f8e215d15c02172a1527a0fd8fd1f502107e25` | 2026-10-02 00:39 | Limit session telemetry to BepInEx logs and feature usage. |
| `83876c1a7e84579af3d25e57f0893729f4a722aa` | 2026-10-02 00:39 | Temporarily enable push trigger for telemetry privacy RC build. |
| `bdf5ecee7c9190bf39e87152360d4e6988d7d841` | 2026-10-02 00:40 | Restore RC workflow to workflow_dispatch only. |
| `77cfd6c4b5b5abe604f97eb6dcbc791dbcdfe96f` | 2026-10-02 00:46 | Keep telemetry collection; soften Settings copy and polish Scout UX. |

The body of `3f59ff60` says: "Probe default-route public IP plus STUN on non-tunnel adapters, then embed vpn/root (or a single root IP when no VPN signal)".

The body of `77cfd6c4` says: "Restore PC/IP/AI journal collection while Settings only mentions BepInEx logs and feature usage".

## 5. The settings text

In the version reviewed, the checkbox is labelled "Share session telemetry with staff", starts checked, and has this help text (`App.tsx:1651-1655`):

> "On quit, Engine can send BepInEx logs for debugging and feature usage counts so we can see where to improve. The diagnostics export below is separate and stays on your PC."

3 versions of this text exist in the repository. Since 25 September the main branch carried: "On quit, Engine sends one session journal (feature usage, AI transcripts, BepInEx logs, PC username/hostname, and public IP) to the private staff Discord channel" (`17d1d183d0da374a4f4dbc83285d4b11b6be6959`). On 2 October at 00:39, `71f8e215` removed the collection and added: "It does not send AI chats, your PC name, or your public IP". 7 minutes later, `77cfd6c4` put the collection back and removed that sentence, leaving the text first quoted above.

The privacy policy said this about telemetry (`docs/privacy-policy.md:37`):

> "Engine telemetry defaults on and can be turned off in Settings. When enabled, Engine may send feature-usage events, AI prompt/response transcripts, and launch/session console logs to a private staff Discord channel (as short summaries plus attached text files)."

## 6. What was not done

No consent was requested, and nothing in the first-run tutorial asks about telemetry. None of the collected categories, the account name, hostname, IP addresses or VPN probing, was disclosed in the application or in the policy. No retention limit or deletion path existed for telemetry records. No way existed to answer an access or erasure request for this data. No impact assessment was done.

## 7. Retention and deletion

The daily maintenance task deletes authentication requests, expired sessions, audit events, the announcement cache and AI usage records (`maintenance.py:22-68`, deletion statements at `:26-57`). Telemetry tables appear in none of them, at any revision. Account deletion removes the user record (`auth.py:778-809`) and nothing else; the telemetry rows and the Discord posts remain.

## 8. What this was not

The menu repository contains no code of this kind. The diagnostics export is made on the machine and stays there. The AI features have a separate per-request sharing option that is off unless turned on.

## 9. The law

I count 18 findings across 12 articles of Regulation (EU) 2016/679 for the processing described above, and a defensible minimum of 15 once overlapping provisions are merged.

**Application.** Article 3(2) applies the Regulation to "the processing of personal data of data subjects who are in the Union by a controller or processor not established in the Union", where the processing relates to, among the listed activities, "the monitoring of their behaviour as far as their behaviour takes place within the Union". The application was distributed to a community with members in the Union and collected from their machines once an hour. The data is personal data within Article 4(1): "any information relating to an identified or identifiable natural person", identifiable "by reference to an identifier such as a name, an identification number, location data, an online identifier". Recital 30 records that "natural persons may be associated with online identifiers provided by their devices, applications, tools and protocols, such as internet protocol addresses". The account name, computer name, addresses and VPN status collected here and connected to a Discord account ID are personal data, and the Regulation applies to them.

**Lawfulness.** Article 6(1): "Processing shall be lawful only if and to the extent that at least one of the following applies", and it lists 6 grounds. None applies here. Consent, ground (a), was never requested. The collection is not necessary to provide the service, ground (b), which is a free desktop tool. No legal obligation, vital interest or public task arises, grounds (c) to (e). Ground (f), legitimate interests, is available only "except where such interests are overridden by the interests or fundamental rights and freedoms of the data subject which require protection of personal data, in particular where the data subject is a child", and a module designed to probe around a protective measure the user chose to deploy does not survive that balance. **This constitutes a violation of Article 6(1).**

**Consent.** Article 4(11) requires "any freely given, specific, informed and unambiguous indication of the data subject's wishes", given "by a statement or by a clear affirmative action". A preference initialised to true, with no question asked and nothing disclosed, is neither a statement nor an affirmative action, and it is not informed. Article 7(1) requires the controller to "be able to demonstrate that the data subject has consented to processing of his or her personal data". There is nothing to demonstrate within the software. **This constitutes a violation of Article 4(11) and Article 7(1).**

**Fairness and transparency.** Article 5(1)(a) requires personal data to be "processed lawfully, fairly and in a transparent manner in relation to the data subject". The collection was not entirely disclosed, and its design included defeating a VPN. Article 13(1)(c) requires the controller to provide, at collection, "the purposes of the processing for which the personal data are intended as well as the legal basis for the processing". No legal basis was stated. Article 13(2)(a) requires "the period for which the personal data will be stored, or if that is not possible, the criteria used to determine that period". No period and no criteria were given. **This constitutes a violation of Article 5(1)(a) and Article 13.**

**Purpose and minimisation.** Article 5(1)(b) requires data to be "collected for specified, explicit and legitimate purposes and not further processed in a manner that is incompatible with those purposes". The purpose recorded by the module's author, to see what regions people use it in, cannot explain per-user files keyed by Discord ID, a resolved non-VPN address, or full session journals. Article 5(1)(c) requires data to be "adequate, relevant and limited to what is necessary in relation to the purposes for which they are processed". The set collected is not limited to any such purpose. **This constitutes a violation of Article 5(1)(b) and Article 5(1)(c).**

**Storage and security.** Article 5(1)(e) requires data to be "kept in a form which permits identification of data subjects for no longer than is necessary for the purposes for which the personal data are processed". No deletion path existed for telemetry at any revision, and nothing was ever deleted. Article 5(1)(f) requires processing "in a manner that ensures appropriate security of the personal data, including protection against unauthorised or unlawful processing and against accidental loss, destruction or damage". Article 32(1) requires "appropriate technical and organisational measures to ensure a level of security appropriate to the risk". Identifying data was posted to a consumer chat channel and kept there without limit. **This constitutes a violation of Article 5(1)(e), Article 5(1)(f) and Article 32(1).**

**Children.** Article 8(1) provides that "in relation to the offer of information society services directly to a child, the processing of the personal data of a child shall be lawful where the child is at least 16 years old", and below 16 "only if and to the extent that consent is given or authorised by the holder of parental responsibility over the child". The community using this application is in large part minors, and there is no age gate. No parental authorisation was ever sought. **This constitutes a violation of Article 8(1).**

**Rights.** Article 12(2): "The controller shall facilitate the exercise of data subject rights under Articles 15 to 22". No mechanism existed to do so for this data. Article 17(1) obliges the controller to erase personal data without undue delay where, among other grounds, "the personal data are no longer necessary in relation to the purposes for which they were collected or otherwise processed" or "the personal data have been unlawfully processed". Both grounds apply, and no erasure was performed or possible. **This constitutes a violation of Article 12(2) and Article 17(1).**

**Design, defaults and accountability.** Article 25(1) requires technical and organisational measures "designed to implement data-protection principles, such as data minimisation, in an effective manner". Article 25(2) requires measures ensuring that "by default, only personal data which are necessary for each specific purpose of the processing are processed", and states that this "applies to the amount of personal data collected, the extent of their processing, the period of their storage and their accessibility". A default-on collection of identity, addresses and VPN data is the opposite of that. Article 5(2): "The controller shall be responsible for, and be able to demonstrate compliance with, paragraph 1". Article 24(1) requires the controller to implement measures "to ensure and to be able to demonstrate that processing is performed in accordance with this Regulation". The operator can demonstrate neither. **This constitutes a violation of Article 25(1), Article 25(2), Article 5(2) and Article 24(1).** Article 24 is not listed in the fine tiers of Article 83; the same failure is sanctioned through Articles 5(2), 25 and 32.

**Impact assessment.** Article 35(1) requires that, "where a type of processing in particular using new technologies, and taking into account the nature, scope, context and purposes of the processing, is likely to result in a high risk to the rights and freedoms of natural persons, the controller shall, prior to the processing, carry out an assessment of the impact of the envisaged processing operations on the protection of personal data". Hourly monitoring, a largely minor population and a module built to see around a VPN meet that threshold. None was carried out. **This constitutes a violation of Article 35(1).**

The breach recorded in section 2 engages Articles 33 and 34 of the Regulation; those duties are addressed there and are not counted above.

I also considered and do not count: ePrivacy Directive 2002/58/EC Art. 5(3); Article 19 and Article 28(3).

**United States law.** The collection of personal information from children under 13 through an online service without direct notice to parents (16 C.F.R. § 312.4), without verifiable parental consent (16 C.F.R. § 312.5, and a settings checkbox is not parental consent), without reasonable security (16 C.F.R. § 312.8) and without a retention limit (16 C.F.R. § 312.10) falls under the Children's Online Privacy Protection Act, 15 U.S.C. §§ 6501-6506. Under the Federal Trade Commission Act, the omission of this collection from the policy and the settings is a material misrepresentation, 15 U.S.C. § 45(a), and the circumvention of a protective measure is an unfair practice within § 45(n). Cal. Bus. & Prof. Code § 22575 requires the disclosure of the categories of personal information collected, and this collection was not disclosed.

**Penalties.** Article 83(4) makes infringements of the obligations of the controller "pursuant to Articles 8, 11, 25 to 39 and 42 and 43" subject to administrative fines "up to 10 000 000 EUR, or in the case of an undertaking, up to 2 % of the total worldwide annual turnover of the preceding financial year, whichever is higher". Article 83(5) makes infringements of "the basic principles for processing, including conditions for consent, pursuant to Articles 5, 6, 7 and 9" and "the data subjects' rights pursuant to Articles 12 to 22" subject to fines "up to 20 000 000 EUR, or in the case of an undertaking, up to 4 % of the total worldwide annual turnover of the preceding financial year, whichever is higher", and Article 83(3) provides that where several provisions are infringed in the same linked processing, the total shall not exceed the amount specified for the gravest infringement. Article 82(1): "Any person who has suffered material or non-material damage as a result of an infringement of this Regulation shall have the right to receive compensation from the controller or processor for the damage suffered". COPPA penalties are assessed per violation and currently reach $53,088.

## 10. After the review

After the review recorded in this document, on 3 October 2026, the collection described in section 3 was removed from the repository and merged into the main branch. The commits are:

| Commit | Date (UTC) | Subject |
|---|---|---|
| `d682d2ba45ff2cdcebeb10dcb93cde75a5cf8074` | 2026-10-03 03:11 | Stop Engine telemetry from collecting hostnames, usernames and IP addresses |
| `47aa11c62a25726245c8f34534989ba8d491602f` | 2026-10-03 03:24 | Cover telemetry payloads with tests and make orphan recovery readable |
| `6f265687ebde495d6be3cfa415ea9432a7c36144` | 2026-10-03 03:49 | Temporarily allow push-triggered RC on telemetry privacy branch |
| `a7515bbda196f7e6f9fc0a85651855e33ef37e30` | 2026-10-03 04:16 | Remove temporary Windows RC push trigger after green privacy candidate |
| `69ccdd301f16cbe8345cd12cd327b231b7bb5055` | 2026-10-03 05:04 | Merge pull request #94 from iireborn/cursor/telemetry-privacy-strip-comments-1353 |

The body of `d682d2b` says: "Client: delete the Tauri telemetry host module that read the OS account name and computer name and probed public/direct IPs over HTTPS and STUN, and remove the two commands that exposed it."

**10.1 What was removed.** The file `apps/desktop/src-tauri/src/telemetry_host.rs` is deleted; the lookups of 3.2 and the STUN probing of 3.3 exist nowhere in the repository, and no code in the native layer reads the account name or computer name. The client no longer sends `pc_username`, `hostname`, `public_ip`, `direct_ip` or `vpn_suspected`, rewrites the account-name segment of file paths to `<user>` in journals and the game path, and rebuilds journals stored by older builds field by field so identifiers in them cannot replay (`telemetry.ts:41-46`, `:72-92`); tests fix the session payload to version, platform, feature counts and log text and the hourly payload to version, platform and feature counts (`telemetry.test.ts:39-82`). The server drops the 5 fields, ignores unknown keys, no longer resolves `root_ip` or `vpn_ip`, no longer takes an address from the connection, and applies a blocklist of 20 field names to submitted log text before anything is stored or posted (`telemetry.py:44-63`, `:92`, `:106`, `:119-142`). Migration `0028_purge_device_identifier_telemetry` deletes the stored events that carry those keys, and the daily task now deletes any that reappear (`maintenance.py:59-63`). The settings help text now lists what is sent, including AI prompts and replies, and states "Never your PC name, Windows username, or IP address" (`App.tsx:1618-1627`); the privacy policy was re-dated 3 October 2026 and describes the same changes (`docs/privacy-policy.md:37-41`).

**10.2 What the announcement said.** On 3 October 2026 an announcement stated that session diagnostics are "limited to what we need for support and reliability; things like Engine version, feature-usage signals, and optional BepInEx/debug logs when telemetry is enabled", and added: "(Just an extra note, all current telemetry logs will be deleted for safety reasons)". The first part does not match the code it accompanied: AI prompts and replies are still collected into the journals of 3.6 and still posted, and the settings text merged in `d682d2b` says so. The second part has no mechanism behind it: migration `0028` reaches the 20 identifier keys only, the session, engine-active and feature rows have no deletion path, as section 7 records, and no code deletes anything from the channel.

**10.3 What is still open.** Telemetry still starts as `true` and nothing asks at first run (`store.ts:71`), so the violations of Articles 4(11), 7(1) and 25(2) are unchanged. The remaining collection has no retention limit; the retention settings cover other records (`config.py:103-107`) and none covers `feature_usage_events`, and the policy states no period. No access or erasure path exists for the remaining telemetry, and account deletion is as section 7 records it. There is no age gate. The channel is untouched, and the posts described in sections 2 and 3 remain there with the account names, computer names and addresses, the token of section 2 was not rotated and still reaches them, and at the time of writing the channel still receives the hourly rollup. None of the 18 findings of section 9 is withdrawn; the removals end the collection of identifiers from the merged build forward, and leave the basis the processing rests on, and the record already held, as they were.

## 11. What ends here

On 3rd October 2026 the operator removed from the project the developer responsible for the collection and the statements recorded above. The telemetry code will be removed as well. When that lands there is no session journal, no hourly pulse, no posting to the staff channel and no telemetry preference left in the codebase, and the open items of 10.3 close with it.

This disclosure is closed.

---

**Correction to the announcement of 3 October 2026**

The announcement of 3 October 2026 stated: "We erased all of it, including the channel it was posted to and the database behind it".

The precise state of each measure is as follows. The 2 Discord channels containing the session journals and AI transcripts were deleted in full on 3 October, this material existed nowhere else and its deletion is complete. The identifier rows in the database, account names, computer names, and IP addresses, were purged by migration on 3 October, with a recurring task that removes any that reappear. The remaining telemetry rows in the database, feature usage counts and session presence records, had no deletion path in the code, and the database itself sits on infrastructure controlled by the former developer and is not accessible to us, its current state is not something we can verify. Database backups, if they exist on that infrastructure, expire up to 30 days after creation under the disclosed retention practice.

The original announcement was written to tell affected users, immediately and plainly, that the collection had ended and the collected material was being removed. The deletion of the channels and the purge of the identifier rows accomplish that purpose. The statement about the database was broader than what we could verify then and can verify now, and this correction states the record as it stands.

Published 7th October 2026, unprompted.

---
