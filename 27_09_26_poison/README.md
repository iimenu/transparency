## Accusations of malicious code (Poison, formerly Seralyth)

Around 26-27 September 2026, members of the Poison (formerly Seralyth) team alleged that ii Reborn's PowerShell installer for the menu, introduced by @corgisolutions (ian) in commit [c2b4e53](https://github.com/iireborn/menu/commit/c2b4e53) and finalized in commit [76e2fbf](https://github.com/iireborn/menu/commit/76e2fbf), constitutes malware. The previous .bat installer was removed in commit [210bcf9](https://github.com/iireborn/menu/commit/210bcf9). The recorded formulations:

- snake (owner of Poison), 05:47 UTC, 27 September: "ian ratted people in tha past including ii like 3 months ago and has the ability to rat all ur users at any time"
- snake, 20:19 UTC: "i just dont think giving a ratter access to rce users who also threatened one of ur admins saying he'd rat them is a good idea"
- slimeydeity and spies, from 20:26 UTC: the installer "runs as admin" and "doesnt need that", and the hosted script URL can "be modified at any moment he wants"

The allegations, and what the published script actually does:

**"It runs as admin"** - **FALSE**. The script executes unelevated by default. Elevation is requested in exactly one place, the catch block of the BepInEx extraction step, which is reached only when writing to the game directory fails, and the script says so in its own comments: "This is ONLY requested if the game files cannot be written without Administrator!". The elevation is `Start-Process powershell -Verb RunAs`, which raises the standard Windows UAC consent dialog that the user must approve; it is not a UAC bypass and cannot elevate silently, and a guard variable (`$env:IIREBORN_ELEVATED`) prevents it from looping. The claim reads a conditional error-recovery branch as a default privilege requirement. spies's own test, writing a file into a Steam library without elevation, showed that where user writes succeed no elevation occurs at all, and when the UAC dialog was pointed out spies replied "i forgot abt uac". A real user's denied-write error, the only condition that triggers the branch, was shown in the conversation.

**"RCE" / "it can be modified at any time".** The installer is fetched from a public GitHub repository, as is the BepInEx release it installs, and as is Poison's own installer.bat, hosted at raw.githubusercontent.com. Any maintainer of any remotely hosted installer can modify it. **This is true of every auto-updating mod in this community**, which spies **acknowledged**: "nobody deep dives into a gtag menu". The relevant difference is auditability: the previous update path ran through a proprietary endpoint with no public history and no commit tracking, while the current one is public and versioned, and any modification is a visible commit. The accusation therefore describes an improvement over the prior state. You should be thanking me.

**"Malware dev history".** A claim about a person, not about code. **At no point in the exchange was any file, function, or behavior in the installer or the menu identified as malicious.** snake's own closing position assigns the decision to the user: "if the user wishes to run software made by a malware developer that is fine in my books as they are trusting the risk that goes by it".

The question "can you, or can you not, point out the malicious material and its location within my powershell installer, or in any parts of ii reborn, beyond theoretical concerns" was put to snake and spies repeatedly between 21:50 and 22:08 UTC on 27 September 2026, with a stated default that silence would be read as "no". snake did not answer yes or no. The recorded positions, verbatim:

- spies, 21:24 UTC: "**we said it was suspicious**"
- spies, 21:50 UTC: "**we never claimed that your installer was currently malware**, we pointed out the fact of which you could (at any point in time) replace the file with something malicious and people wouldnt know until others have been effected"
- snake, 21:24 UTC: "that is true", in response to "i dont think we ever said that you ALREADY ratted it", and "if the user trusts it then be it"
- snake, 21:56 UTC: "if the user wishes to run software made by a malware developer that is fine in my books as they are trusting the risk that goes by it"
- slimeydeity, 21:41 UTC: "in my opinion if you werent trying to rat you wouldnt care that much", stated expressly as opinion

An independent reading of the script by a team member, 21:26 UTC: "**the installer is not a rat** it runs as admin after it first fails which is rare of a shell command just randomly breaking."

**Witness statement.** Useless (GitHub @TheUselessCreator, see Signature below), 22:26 UTC, posted under his own account: "I, Useless, have witnessed the inability of snake, the owner of Poison, 27th September 2026, to prove that the PowerShell installer or any parts of ii reborn contain malware, beyond a theoretical concern like 'it could' 'he could' and witnessed Snakes silence for over 10 minutes."

**Effect on the project.** Per the account of the then head administrator (@iiDrifted) at 05:58 UTC on 27 September 2026, terms conveyed from the Poison side included getting the installer's developer (@corgisolutions/ian) "out of the picture entirely" and removing the 1.1.0 changes (same developer), on the **theory** that he "might still have access to pushing an update thatll rat everyone". Repository privileges were removed and the installer was modified on that basis, but the modification was reverted the same day. This is recorded because material action was taken on accusations that were never substantiated in the exchange above.

**Record.** The complete transcript is published at [27_09_26_poison/transcript.html](27_09_26_poison/transcript.html), unaltered, together with its [assets](27_09_26_poison/assets/). Messages deleted during the conversation appear as deleted in the export, nothing has been removed. **No malicious material has been identified in the installer or the menu as of your reading of this repository.** Future accusations are to be submitted by email with specifics, meaning file, function, and behavior, and will be published here together with any response, refer to root README.md.

## Authorization

The authorization of related persons to this response is recorded below. Each appends the statement below, completed with their own details, in a commit made under their own GitHub account.

```
I, [full name, or, privacy-respecting, any alias] (GitHub: [username], Discord: [handle, if any]), confirm that:

1. I am a related person in the cited group chat dated 27th September 2026
regarding malware concerns and an operator of iistupid.com / a contributor
to github.com/iireborn.

2. I have read this summary dated 27th September, 2026 published at
https://github.com/iireborn/transparency/27_09_26_poison/README.md in full.

3. I stand by my witness statement present in the Discord group chat
export, introduced in commit 640e65d63c18d3b152506b484495c3bfa3d1f041.

4. I wrote my statement voluntarily, under no pressure, and I was
not extorted into doing it or hacked.

5. I authorize Florian Kolb (corgisolutions) to transmit such points
and opinions on my behalf, and to correspond with Poison (Seralyth),
regarding any future concerns, accusations, and threats, on my behalf
in this matter.

Dated: 27th September, 2026
```

---

I, TheUselessCreator (GitHub: TheUselessCreator, Discord: theuselesscreator confirm that:

1. I am a related person in the cited group chat dated 27th September 2026
regarding malware concerns and an operator of iistupid.com / a contributor
to github.com/iireborn.

2. I have read this summary dated 27th September, 2026 published at
https://github.com/iireborn/transparency/27_09_26_poison/README.md in full.

3. I stand by my witness statement present in the Discord group chat
export, introduced in commit 640e65d63c18d3b152506b484495c3bfa3d1f041.

4. I wrote my statement voluntarily, under no pressure, and I was
not extorted into doing it or hacked.

5. I authorize Florian Kolb (corgisolutions) to transmit such points
and opinions on my behalf, and to correspond with Poison (Seralyth),
regarding any future concerns, accusations, and threats, on my behalf
in this matter.

Dated: 27th September, 2026
