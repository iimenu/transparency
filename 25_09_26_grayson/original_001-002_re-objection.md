Delivered-To: corgi@aster.cx
X-Spam-Status: Yes
Received: from sender5-of-o52.zoho.com (sender5-of-o52.zoho.com [165.173.182.52] (AS2639 ZOHO, US)) (using TLSv1.3 with cipher TLS13_AES_256_GCM_SHA384) by mx.astermail.org (Stalwart SMTP) with ESMTPS id 49A5F2B839DB1C2; Sat, 26 Sep 2026 15:59:40 +0000
Authentication-Results: mx.astermail.org; dkim=pass header.i=admin@goldentrophy.software header.s=zmail header.b=Qs7CdQWK; spf=none (mx.astermail.org: no SPF records found for postmaster@sender5-of-o52.zoho.com) smtp.helo=sender5-of-o52.zoho.com; spf=pass (mx.astermail.org: domain of admin@goldentrophy.software designates 165.173.182.52 as permitted sender) smtp.mailfrom=admin@goldentrophy.software; iprev=pass policy.iprev=165.173.182.52; dmarc=none header.from=goldentrophy.software policy.dmarc=none
Received-SPF: pass (mx.astermail.org: domain of admin@goldentrophy.software designates 165.173.182.52 as permitted sender) receiver=mx.astermail.org; client-ip=165.173.182.52; envelope-from="admin@goldentrophy.software"; helo=sender5-of-o52.zoho.com;
...
ARC-Authentication-Results: i=2; mx.astermail.org; dkim=pass header.i=admin@goldentrophy.software header.s=zmail header.b=Qs7CdQWK; spf=none (mx.astermail.org: no SPF records found for postmaster@sender5-of-o52.zoho.com) smtp.helo=sender5-of-o52.zoho.com; spf=pass (mx.astermail.org: domain of admin@goldentrophy.software designates 165.173.182.52 as permitted sender) smtp.mailfrom=admin@goldentrophy.software; iprev=pass policy.iprev=165.173.182.52; dmarc=none header.from=goldentrophy.software policy.dmarc=none
X-Spam-Result: RWL_MAILSPIKE_EXCELLENT (-0.40), DKIM_ALLOW (-0.20), SPF_ALLOW (-0.20), MIME_GOOD (-0.10), ARC_ALLOW (0.00), ARC_SIGNED (0.00), DBL_BLOCKED_OPENRESOLVER (0.00), DKIM_SIGNED (0.00), DNSWL_BLOCKED (0.00), DWL_DNSWL_BLOCKED (0.00), FROM_EQ_ENV_FROM (0.00), FROM_HAS_DN (0.00), HAS_ATTACHMENT (0.00), MID_RHS_MATCH_ENV_FROM (0.00), RBL_SENDERSCORE_REPUT_BLOCKED (0.00), RCPT_COUNT_ONE (0.00), RCPT_IN_BODY (0.00), RCVD_COUNT_ONE (0.00), RCVD_TLS_LAST (0.00), SOURCE_ASN_2639 (0.00), TO_DN_NONE (0.00), TO_MATCH_ENVRCPT_ALL (0.00), URIBL_BLOCKED (0.00), XM_UA_NO_VERSION (0.01), URI_COUNT_ODD (0.50), DMARC_NA (1.00), MID_RHS_MATCH_FROM (1.00), MIME_MA_MISSING_HTML (1.00), SEM_URIBL_FRESH15 (3.00)
X-Spam-Score: spam, score=5.61
Return-Path: <admin@goldentrophy.software>
X-Spamd-Bar: ----
X-Spam-Status: No, score=-4.00
Authentication-Results: Stalwart v0.15.5; none
...
X-Rspamd-Action: no action
X-Spamd-Result: default: False [-4.00 / 30.00]; REPLY(-4.00)[]; ASN(0.00)[asn:2639, ipnet:165.173.182.0/23, country:US]
X-Rspamd-Pre-Result: action=no action; module=replies; Message is reply to previously sent email
ARC-Seal: i=1; a=rsa-sha256; t=1790438375; cv=none;  d=zohomail.com; s=zohoarc; b=k7SRuniJlF6MJmtzffjdhkgMRxB6+xpEVK/w93pcRkUJxFAXEfbHpi+l/lUu9pdHoQoLfhFUGvyQf7FxrkSxpSZIDok2wCFjC+ecZTy2N6igYBFepmI/Gjqjw89mLnJ1xDCo01nxHjKbemhzZt5lfPdnPkKl99xFi18kwAo8thY=
ARC-Message-Signature: i=1; a=rsa-sha256; c=relaxed/relaxed; d=zohomail.com; s=zohoarc;  t=1790438375; h=Content-Type:Date:Date:From:From:In-Reply-To:MIME-Version:Message-ID:Subject:Subject:To:To:Message-Id:Reply-To:Cc; bh=vtFfW1BvN73vLKTpN886NYhreiDhwopTvo2wI6PXsqc=; b=YLqfG0qFMf7N422gEQa+3tRnOoZmGfa/MZ00AlnqCeBPPzeb5HRNoljDx1H1O0MiJSs9AIMDXlOzLIhg4pYq3iOafKIlAcVqHJQh4qBQes0c/UCkd/tUTxgN9nHy6JOTBSr1ApPJHThMU9fNYp+68dGRwze6ON6Bi3mM+qy6RHQ=
ARC-Authentication-Results: i=1; mx.zohomail.com; dkim=pass  header.i=goldentrophy.software; spf=pass  smtp.mailfrom=admin@goldentrophy.software; dmarc=pass header.from=<admin@goldentrophy.software>
DKIM-Signature: v=1; a=rsa-sha256; q=dns/txt; c=relaxed/relaxed; t=1790438375; s=zmail; d=goldentrophy.software; i=admin@goldentrophy.software; h=Date:Date:From:From:To:To:Message-Id:Message-Id:In-Reply-To:Subject:Subject:MIME-Version:Content-Type:Reply-To:Cc; bh=vtFfW1BvN73vLKTpN886NYhreiDhwopTvo2wI6PXsqc=; b=Qs7CdQWKmiUskZwY5RGFNHa4u30XSvPv4gGaeP2y1vprxu3CgjfQY7y5FhR3gIdg XHZDJJB4VbSbadl//Mf7TLM68pN21hjf6v6hOxpXBnmOf0hGQT5KF4r9V2yB2RAkfoB nepEHEeaTlUBx6KPxli2CzfbnFn1lOfz7nT645y4=
Received: from mail.zoho.com by mx.zohomail.com with SMTP id 1790438374570523.4879316275919; Sat, 26 Sep 2026 08:59:34 -0700 (PDT)
Date: Sat, 26 Sep 2026 11:59:34 -0400
From: goldentrophy <admin@goldentrophy.software>
To: <corgi@aster.cx>
...
Subject: Re: Response to your notices dated 25 September 2026, "Notice of GNU GPL v3 Violation" and "Cease and Desist"

--- MESSAGE BEGINS NOW ---

Corgi,

I acknowledge receipt of your response dated 25 September 2026 on behalf of the operators of iistupid.com and github.com/iireborn. I'll address the points that remain open.
1. Deadline. The cure period runs 30 days from the Recipients' receipt of my notice, ending 25 October 2026, as stated in my revised notice.
2. Branding and artwork (your sections 3.1 and 3.4). As of 26 September 2026, the server reached through discord.gg/iidk, now named "ii Reborn", displays a banner reading "ii's Stupid Menu". A screenshot is attached. It also uses "ii's Stupid Menu" as branding, which is inconsistent with your statement that the Recipients' products carry <img width="660" height="432" alt="image" src="https://github.com/user-attachments/assets/9ba4d7a3-9627-4cc4-b8ad-0f7d9822552c" />
 only their own names. Please remove it. 
3. Vanity URL. discord.gg/iidk uses "iidk", my full handle and the name I have used for my software and community, not a two-letter fragment. I set that URL when I ran the server. I request that the Recipients stop using it. 
4. ii Engine (your sections 2.1 and 2.1.1). Section 2.1 says ii Engine fetches the menu from a backend the Recipients operate; section 2.1.1 says it fetches directly from the GitHub Releases of iireborn/menu. Please confirm which is accurate. If any binaries are served from your own server, section 6(d) requires that you maintain clear directions next to the object code saying where to find the Corresponding Source, and that the source correspond to the exact binaries served.
5. Tracker. I note your written confirmation that the ii Tracker backend contains none of my code and no derivative of it. I am not pursuing a code claim regarding the tracker at this time, and I reserve my rights should evidence to the contrary come to light.
6. Name and domain. I have noted your position on the names and on iistupid.com. I disagree with it and reserve all rights and remedies regarding both.

I will verify the changes described in your response and will write again if anything remains outstanding.
Nothing in this email waives any right or remedy, all of which are expressly reserved.
