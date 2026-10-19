---
theme: default
title: "Bring Your Own Trusted Caller — BSidesPDX 2026"
author: Xusheng Li
aspectRatio: 16/9
canvasWidth: 1280
colorSchema: dark
fonts:
  sans: "Arial"
  mono: "Menlo"
---

# Bring Your Own<br><span class="aqua">Trusted Caller</span>

<div class="deck-subtitle">A New Way to Exploit Vulnerable Windows Drivers</div>
<div class="cover-name">Xusheng Li</div>

<!-- Opening sequence draft. Later case-study claims need exact evidence, versions, and scope. -->

---

# Who am I?

<div class="venn">
  <div class="venn-circle venn-top"><span>crackmes.one</span></div>
  <div class="venn-circle venn-left"><span>Vector 35</span></div>
  <div class="venn-circle venn-right"><span>Paged Out!</span></div>
  <div class="venn-circle venn-bottom"><span>Shellphish</span></div>
  <div class="venn-center">me</div>
</div>
<!-- The speaker's prior re//verse deck lists these four affiliations. The deliberately excessive Venn diagram is the joke. -->

---

# I am a…

<div class="role-list">
  <div>Software developer</div>
  <div>Reverse engineer</div>
  <div>Vulnerability researcher</div>
  <div>Malware analyst</div>
  <div>CTF player</div>
</div>

---

<div class="variant-statement">
  <div>Bring Your Own Trusted Caller (BYOTC)</div>
  <div>shares a playbook with</div>
  <div>Bring Your Own Vulnerable Driver (BYOVD)</div>
</div>

<!-- Set up the conceptual relationship before explaining BYOVD. Both attacks reuse a trusted component to reach a privileged driver operation, but their mechanisms and trust boundaries differ. -->

---

<h1 class="byovd-title">Bring Your Own Vulnerable Driver (1)</h1>

<div class="byovd-definition">
  <div>An attacker with local admin rights loads a legitimate, signed but vulnerable Windows kernel driver.</div>
  <div>Then abuses it for kernel-level control—often to disable security software.</div>
</div>
<div class="source-line">Definition adapted from <a href="https://www.picussecurity.com/resource/blog/what-are-bring-your-own-vulnerable-driver-byovd-attacks">Picus Security</a> · May 2026</div>

<!-- Definition paraphrased from Picus Security, https://www.picussecurity.com/resource/blog/what-are-bring-your-own-vulnerable-driver-byovd-attacks . Local admin is needed to install/load a new driver; mere initial code execution is insufficient. The objective and exact primitive vary by attack. -->

---

<h1 class="byovd-title">Bring Your Own Vulnerable Driver (2)</h1>

<div class="byovd-steps">
  <div v-click><b>1</b><span>Compromise a Windows host<br><small>phishing or exploitation</small></span></div>
  <div v-click><b>2</b><span>Run in user mode<br><small>standard user or administrator</small></span></div>
  <div v-click><b>3</b><span>Seek kernel-level capability</span></div>
</div>

<!-- Each click reveals one step. Initial access can be phishing or exploitation; these are examples, not required routes. A standard-user foothold would need additional elevation before loading a new kernel driver. Picus: https://www.picussecurity.com/resource/blog/what-are-bring-your-own-vulnerable-driver-byovd-attacks -->

---

<h1 class="byovd-title">Bring Your Own Vulnerable Driver (3)</h1>

<div class="byovd-prompt">What can kernel-level access enable?</div>
<div class="goal-grid byovd-goals">
  <div>Terminate AV / EDR processes</div>
  <div>Access privileged information</div>
  <div>Change kernel state</div>
</div>

<!-- Illustrative attacker goals, not a claim that every driver provides each primitive or can terminate every protected process. Sources: https://www.picussecurity.com/resource/blog/what-are-bring-your-own-vulnerable-driver-byovd-attacks ; https://blog.talosintelligence.com/exploring-vulnerable-windows-drivers/ -->

---

<h1 class="byovd-title">Bring Your Own Vulnerable Driver (4)</h1>

<div class="byovd-big">Windows checks <span class="aqua">kernel-driver signatures.</span></div>
<div class="obstacle-line">An unsigned attacker-built driver usually cannot load.</div>
<div class="source-line"><a href="https://learn.microsoft.com/en-us/windows-hardware/drivers/install/kernel-mode-code-signing-policy--windows-vista-and-later-">Microsoft · Driver Signing Policy</a></div>

<!-- Microsoft states that starting with Windows 10 1607, new kernel-mode drivers generally need Microsoft Dev Portal signing, with documented exceptions. Signing policy depends on Windows version and configuration; attackers can obtain signed malicious drivers. This slide explains why reusing existing signed drivers is attractive. -->

---

<h1 class="byovd-title">Bring Your Own Vulnerable Driver (5)</h1>

<div class="byovd-big">Some legitimate signed drivers already <span class="aqua">have the capability.</span></div>
<div class="driver-capability byovd-capability">
  <div><span>ANTIVIRUS APP</span><strong>Requests a process stop</strong></div>
  <div class="capability-arrow">IOCTL →</div>
  <div><span>SIGNED DRIVER</span><strong>Terminates the process</strong></div>
</div>

<!-- One illustrative product design; not every AV/EDR driver offers this feature. The application can request kernel work via DeviceIoControl/IOCTL. A flaw in device access control or IOCTL authorization can let an attacker invoke it. Source: https://learn.microsoft.com/en-us/windows-hardware/drivers/kernel/irp-mj-device-control -->

---

<h1 class="byovd-title">Bring Your Own Vulnerable Driver (6)</h1>

<div class="byovd-big">Bring the signed driver.<br><span class="aqua">Call its vulnerable functionality.</span></div>
<div class="byovd-flow"><span>Compromised host</span><b>→</b><span>Vulnerable driver</span><b>→</b><span>Privileged action</span></div>

<!-- A common BYOVD chain, not a universal sequence. Loading a new driver requires local admin rights and a driver allowed by the target's policy; some incidents instead abuse an already installed driver. The user-mode attacker typically sends an IOCTL request to invoke the driver's vulnerable capability. Source: https://www.picussecurity.com/resource/blog/what-are-bring-your-own-vulnerable-driver-byovd-attacks ; https://blog.talosintelligence.com/exploring-vulnerable-windows-drivers/ -->

---

# BYOVD reports

<div class="article-montage">
  <div class="article-card"><div class="article-image"><img src="/clippings/talos.png" alt="Cisco Talos research article headline"></div><div class="article-label">CISCO TALOS · 2024</div><div class="article-caption">BlackByte brought four vulnerable drivers</div></div>
  <div class="article-card"><div class="article-image"><img src="/clippings/unit42.png" alt="Unit 42 extortion actor research headline"></div><div class="article-label">UNIT 42 · 2024</div><div class="article-caption">An extortion actor tested EDR evasion</div></div>
</div>

<!-- Cropped screenshots of source pages captured 2026-10-18; captions are paraphrases. Talos observed four drivers in its BlackByte investigation. Sources: https://blog.talosintelligence.com/exploring-vulnerable-windows-drivers/ ; https://unit42.paloaltonetworks.com/edr-bypass-extortion-attempt-thwarted/ . -->

---

# More BYOVD reports

<div class="article-montage">
  <div class="article-card"><div class="article-image"><img src="/clippings/kaspersky.png" alt="Kaspersky Securelist driver of destruction article headline"></div><div class="article-label">KASPERSKY · 2025</div><div class="article-caption">A signed driver used against AV processes</div></div>
  <div class="article-card"><div class="article-image"><img src="/clippings/eset.png" alt="ESET research EDR killers article headline"></div><div class="article-label">ESET RESEARCH · 2026</div><div class="article-caption">EDR killers are a repeatable tool class</div></div>
</div>

<!-- Cropped screenshots of source pages captured 2026-10-18; captions are paraphrases. These are selected reports, not a census. Sources: https://securelist.com/av-killer-exploiting-throttlestop-sys/117026/ ; https://www.welivesecurity.com/en/eset-research/edr-killers-explained-beyond-the-drivers/ . -->

---

<h1 class="research-title">Detecting BYOVD at scale</h1>

<ul class="research-bullets">
  <li><div><strong><a href="https://github.com/0dayResearchLab/msFuzz">msFuzz</a></strong><span>100 vulnerabilities found in 100 days</span></div></li>
  <li><div><strong><a href="https://www.ndss-symposium.org/ndss-paper/unveiling-byovd-threats-malwares-use-and-abuse-of-kernel-drivers/">Unveiling BYOVD Threats: Malware’s Use and Abuse of Kernel Drivers</a></strong><span>NDSS 2026 · Analyzed malware loading 773 signed drivers</span></div></li>
</ul>

<!-- Examples of systematic work, not an exhaustive survey. The msFuzz authors report 100 vulnerabilities in 100 days: https://archive.codeblue.jp/2024/en/program/time-table/day2-010/ . Their public materials do not state the number of distinct drivers examined; do not present 100 as a driver count. The NDSS 2026 study analyzed 8,779 malware samples loading 773 distinct signed drivers and flagged suspicious behavior in 48: https://www.ndss-symposium.org/ndss-paper/unveiling-byovd-threats-malwares-use-and-abuse-of-kernel-drivers/ . Fuzzing for new flaws and observing BYOVD behavior are distinct tasks; do not present them as equivalent scanners. -->

---

# What do we do with a vulnerable driver?

<div class="response-list">
  <div class="response-item"><span>01</span><div><strong><a href="https://learn.microsoft.com/en-us/windows/security/application-security/application-control/app-control-for-business/design/microsoft-recommended-driver-block-rules">Microsoft vulnerable driver blocklist</a></strong><p>Report it to Microsoft. Confirmed risky versions may be blocked from loading.</p></div></div>
  <div class="response-item"><span>02</span><div><strong><a href="https://github.com/magicsword-io/LOLDrivers">LOLDrivers</a></strong><p>Submit verified samples and hashes so defenders can hunt for them.</p></div></div>
</div>

<!-- Microsoft's blocklist can block known vulnerable versions; inclusion is not automatic: https://learn.microsoft.com/en-us/windows/security/application-security/application-control/app-control-for-business/design/microsoft-recommended-driver-block-rules . LOLDrivers is a community catalog, not a blocking control: https://github.com/magicsword-io/LOLDrivers . -->

---

# How do we fix the vulnerability?

<div class="fix-types">
  <div><span>01 · CODE DEFECT</span><strong>Buffer or heap overflow?</strong><p>Repair the faulty code.</p></div>
  <div><span>02 · ABUSED CAPABILITY</span><strong>Process termination?</strong><p>The driver may need this function.</p></div>
</div>

<!-- The second case concerns authorization of an intended capability, not a memory-safety bug. ZwTerminateProcess is the documented kernel-mode routine for terminating a process: https://learn.microsoft.com/en-us/windows-hardware/drivers/ddi/ntddk/nf-ntddk-zwterminateprocess . Do not imply that every AV or EDR driver uses this exact routine. Removing process-termination functionality may break a security product. -->

---

# A common fix: access control

<div class="decision-flow">
  <div class="decision-node">Caller</div>
  <b class="decision-arrow">→</b>
  <div class="decision-node decision-check">Driver checks<br>who is calling</div>
  <div class="decision-fork"><svg viewBox="0 0 70 227" aria-hidden="true"><path d="M0 113 H26 V52 H62 M26 113 V175 H62"/><path d="M62 46 L70 52 L62 58 M62 169 L70 175 L62 181"/></svg></div>
  <div class="decision-branches">
    <div class="decision-path decision-trusted"><span>TRUSTED</span><strong>Privileged operation</strong></div>
    <div class="decision-path decision-denied"><span>UNTRUSTED</span><strong>Deny</strong></div>
  </div>
</div>

<!-- Conceptual model. The driver should authorize the requested operation, not merely assume any caller that can open the device is trusted. This sets up the next section: caller checks can themselves rely on spoofable or controllable trust signals. -->

---

# Malwarebytes Chameleon

<div class="mb-pair">
  <div><span>USER-MODE CLIENT</span><strong>MBAM.exe</strong><p>Signed Malwarebytes app</p></div>
  <b>→</b>
  <div><span>KERNEL DRIVER</span><strong>mbamchameleon.sys</strong><p>Can terminate a process</p></div>
</div>

<!-- The genuine MBAM.exe is the user-mode client in the PoC; mbamchameleon.sys is the analyzed driver. Exact driver SHA-256: d86b9b20788b6bff70a1a4c4111b2ea33b9ec705cc6b8fe869362fc3899820a3. Sources: https://xusheng.dev/posts/byotc/main/ ; original Malwarebytes HackerOne report submitted June 15, 2025 (local transcript: /Users/xusheng/Downloads/malwarebytes_hackerone/malwarebytes_hackerone.txt). -->

---

# How it normally works

<div class="mb-horizontal-timeline">
  <div class="mb-track"></div>
  <div class="mb-milestone"><span>01</span><strong>Client<br>registers</strong><small>IOCTL: 0x222008</small></div>
  <div class="mb-milestone"><span>02</span><strong>Driver checks<br>signature</strong><small>Signed by<br>Malwarebytes</small></div>
  <div class="mb-milestone"><span>03</span><strong>Client provides<br>target PID</strong><small>IOCTL: 0x222024</small></div>
  <div class="mb-milestone"><span>04</span><strong>Driver terminates<br>process</strong><small>ZwTerminateProcess(PID)</small></div>
</div>

<!-- Normal authorized sequence in the analyzed sample: the client sends IOCTL 0x222008 to register; the driver checks the caller image is signed by Malwarebytes; the client sends IOCTL 0x222024 with a PID; the driver reaches ZwTerminateProcess. The signature check is part of registration, not repeated as a separate user-mode step before each termination request. Sources: https://xusheng.dev/posts/byotc/main/ ; original June 15, 2025 report and its library.cpp attachment. -->

---

# Signature decides whether the driver acts

<div class="mb-signature-paths">
  <div><strong>Signed MBAM.exe</strong><i>→</i><span>Malwarebytes signature ✓</span><i>→</i><b>Trusted</b></div>
  <div><strong>Unsigned client</strong><i>→</i><span>No Malwarebytes signature ✕</span><i>→</i><b>Denied</b></div>
</div>

<!-- At 0x222008 registration, the driver checks whether the calling process image is signed by Malwarebytes. An attacker cannot forge that vendor signature, but can reuse the genuine signed image. This is a simplified decision mockup, not an exact decompilation of the check. Sources: https://xusheng.dev/posts/byotc/main/ ; original June 15, 2025 report. -->

---

# Where is the gap?

<div class="process-gap">
  <div>Signed executable <code>!=</code> trusted runtime code</div>
</div>

<!-- A signature on the executable image does not establish the provenance of every instruction later executing in the process. In this administrator-level Malwarebytes PoC, a DLL is injected into the genuine MBAM.exe process. -->

---

# Ways other code can enter a process

<div class="code-entry-ways">
  <div><strong>DLL loading</strong><span>A loaded DLL may be untrusted.</span></div>
  <div><strong>Code injection</strong><span>Another process can introduce code.</span></div>
</div>

<!-- These are distinct mechanisms: an application may load dependencies or plugins whose provenance differs from its EXE, while process injection introduces code from outside. The Malwarebytes PoC on the following slide uses DLL injection into the genuine MBAM.exe process. This slide is conceptual; it does not claim that every Windows process allows either mechanism under every policy. -->

---

# A signed image can run injected code

<div class="mb-bypass">
  <div><span>01</span><strong>Launch genuine MBAM.exe</strong></div>
  <div><span>02</span><strong>Inject a DLL into its process</strong></div>
  <div><span>03</span><strong>Register, then request termination</strong></div>
</div>

<!-- In the reported administrator-level PoC, the injected DLL in the legitimate signed MBAM.exe registers via 0x222008 and invokes 0x222024 with MsMpEng.exe's PID. The demonstration terminates Microsoft Defender in that test environment. Do not claim standard-user access or other untested targets. Sources: https://xusheng.dev/posts/byotc/main/ ; original June 15, 2025 report and its library.cpp attachment. -->

---

# Malwarebytes BYOTC demo

<video class="mb-demo-video" controls playsinline preload="metadata" poster="/demos/malwarebytes-poc-poster.jpg" src="/demos/malwarebytes-poc.mp4" aria-label="Malwarebytes Chameleon proof of concept video"></video>

<!-- Local copy of the video embedded in https://xusheng.dev/posts/byotc/main/ . SHA-256 of public/demos/malwarebytes-poc.mp4: 3ba8b9931bf32a324ef4437bd6ff28479dfacae1c43bbb2d38f17da1339de128 . Original report video is also available locally as mbamchemeleon_byovd.mov; the published MP4 is used here for browser playback. -->

---

# Malwarebytes disclosure timeline

<div class="disclosure-timeline">
  <div><time>Jun 15, 2025</time><strong>Reported to Malwarebytes</strong></div>
  <div><time>Jul–Sep 2025</time><strong>Marked, then closed as duplicate</strong></div>
  <div><time>Oct 31, 2025</time><strong>Fix reported in progress</strong></div>
  <div><time>Sep 2026</time><strong>My retest: still reproducible</strong></div>
</div>

<!-- Original HackerOne transcript: /Users/xusheng/Downloads/malwarebytes_hackerone/malwarebytes_hackerone.txt . Submitted June 15, 2025; Malwarebytes said on July 2 that an earlier March 27 report by another researcher covered the same latest-driver IOCTL 0x222008 path; this report was closed as duplicate September 5; on October 31 the vendor said a fix was in progress and offered a future retest. Public account: https://xusheng.dev/posts/byotc/main/ . September 2026 retest result is the speaker's account in this conversation; preserve the tested build and evidence before presenting a stronger product-wide or current-status claim. -->

---

# Why no easy fix?

<div class="hard-fix">
  <div>I don't blame Malwarebytes.</div>
  <div>Reliable caller access control is <span>hard.</span></div>
</div>
<div class="hard-fix-return">We'll come back to this.</div>

<!-- Transition to System Informer: even a more thorough process validation scheme had a trust-boundary gap. This is the speaker's assessment, not a claim that the vendor's remediation process or current product status has been independently audited. Source: https://xusheng.dev/posts/byotc/main/ . -->

---

# Checking More Drivers

<div class="research-pivot">
  <div>Across antivirus and EDR products.</div>
  <div>That's a <em>lot</em> of reverse engineering...</div>
</div>

<!-- Set up the joke on the next slide. The study combined a broad driver corpus with focused security-product assessment; do not imply that every corpus binary was manually reverse engineered or exploited. Research inventory: /Users/xusheng/byotc/results/corpus_status.md and /Users/xusheng/byotc/results/byotc_security_product_candidates.md . -->

---

# The solution is...

<div class="ai-punchline">AI</div>

<!-- Humorous reveal. AI assisted with acquisition tooling, triage, and reverse-engineering work; the reported findings were checked against exact artifacts and bounded lab reproductions. Do not present an autonomous model verdict as proof of exploitability. -->

---

# Analysis Pipeline

<div class="manual-pipeline">
  <div>Search</div><b>→</b><div>Download</div><b>→</b><div>Install</div><b>→</b><div>Agentic RE</div>
</div>

<!-- "Install" is shorthand: some public packages were extracted offline; selected products were installed in an x64 lab. Agentic RE includes first-pass triage; findings still required validation. -->

---

# This isn't the AI way

<div class="manual-pipeline crossed-pipeline">
  <div class="crossed">Search</div><b>→</b><div class="crossed">Download</div><b>→</b><div class="crossed">Install</div><b>→</b><div class="re-only">Agentic RE</div>
</div>
<div class="pipeline-caption">Why automate only the last step?</div>

<!-- The red crosses represent the tempting but unnecessarily manual acquisition/setup workflow, not a claim that humans never reviewed or validated results. -->

---

# AI ran the whole pipeline

<div class="ai-pipeline-frame">
  <span class="ai-pipeline-label">AI</span>
  <div class="manual-pipeline ai-pipeline">
    <div>Search</div><b>→</b><div>Download</div><b>→</b><div>Extract</div><b>→</b><div>RE</div>
  </div>
</div>

<!-- AI-assisted tooling searched public sources, downloaded available installers, extracted drivers or installed selected products, and produced first-pass triage. Acquisition scripts: /Users/xusheng/byotc/catalog-downloader/ and /Users/xusheng/byotc/tools/ . Product scope: /Users/xusheng/byotc/results/byotc_security_product_candidates.md . Exact findings still required targeted RE and bounded runtime tests. -->

---

# In Case You Want My Prompt

<div class="prompt-card">
  <p>... <mark>find</mark> publicly available security products ...<br>... <mark>download</mark> their installers and <mark>extract</mark> the drivers ...<br>... <mark>analyze</mark> who can call each privileged operation ...</p>
</div>

<!-- This is a slide-writing paraphrase of the user's described one-shot request, not a verbatim quotation. Original prompt text was not located in the available local research artifacts; replace this with exact excerpts if the original conversation is found. -->

---

# Agentic Vulnerability Research

<div class="agentic-photo-placeholder">Photo placeholder</div>

<!-- Replace the placeholder with the speaker's photo of using AI agents on a phone while sitting on the couch. Talk track: this is often how the speaker does vulnerability research in free time while spending time with family. Keep the slide image-led and avoid adding a caption transcript. -->

---

# AI goes a long way

<div class="m5-problem">My M5 Mac's Windows on ARM VM can't load <strong>x64 kernel drivers.</strong></div>

<div class="m5-ai-frame">
  <span>AI handled the entire setup <em>without my intervention</em></span>
  <div><b>Downloaded x64 ISO</b><i>→</i><b>Built QEMU x64 VM</b><i>→</i><b>Installed Windows</b></div>
</div>

<!-- The speaker reports that the AI performed this lab setup without manual intervention. Windows 11 on ARM emulates x86/x64 user-mode applications, but kernel drivers must be native ARM64: https://learn.microsoft.com/en-us/windows/arm/add-arm-support . This is x86_64 system emulation in QEMU on the Apple Silicon M5 Mac, not native-speed virtualization. Local artifacts include /Users/xusheng/byotc/lab-atool-x64.qcow2, lab-gdata-x64.qcow2, lab-hitmanpro-clean-x64.qcow2 and /Users/xusheng/Downloads/PE_loading/Win11_25H2_English_x64_v2.iso . -->

---

# The research so far

<div class="research-scale">
  <div><strong>14</strong><span>products screened</span></div>
  <div><strong>8</strong><span>vulnerable drivers</span></div>
  <div><strong>6</strong><span>accepted into LOLDrivers</span></div>
</div>

<!-- These are related but not a single conversion funnel: 14 product families were screened in the recent public-download queue (/Users/xusheng/byotc/results/byotc_security_product_candidates.md), while eight reported findings span earlier and recent work. GitHub issue search for repo:magicsword-io/LOLDrivers author:xusheng6 returned accepted #434 Panda, #435 Malwarebytes, #440 Huorong, #441 360, #442 Antiy, #443 Comodo; open #438 Avast and #439 HitmanPro.Alert. The six accepted cases appear in merged PR #436, #444 and #446. HitmanPro.Alert dynamically demonstrated only a harmless gated request; do not imply a memory-read impact was executed. Panda and Comodo are conventional BYOVD, not BYOTC. K7 remains unresolved and is not counted. Status checked October 19, 2026. -->

---

# The eight vulnerable drivers

<div class="reported-driver-list">
  <div><strong>Malwarebytes</strong><span>accepted</span></div>
  <div><strong>Panda</strong><span>accepted</span></div>
  <div><strong>Huorong</strong><span>accepted</span></div>
  <div><strong>360 Total Security</strong><span>accepted</span></div>
  <div><strong>Antiy ATool</strong><span>accepted</span></div>
  <div><strong>Comodo</strong><span>accepted</span></div>
  <div><strong>Avast</strong><span class="open">open</span></div>
  <div><strong>HitmanPro.Alert</strong><span class="open">open</span></div>
</div>

<!-- Accepted: https://github.com/magicsword-io/LOLDrivers/issues/434 , /issues/435 , /issues/440 , /issues/441 , /issues/442 , /issues/443 . Open: /issues/438 Avast and /issues/439 HitmanPro.Alert, checked October 19, 2026. Panda and Comodo are conventional BYOVD. HitmanPro.Alert's demonstrated result is a harmless gated PID query, not an executed sensitive memory read. Avast research is marked private/embargoed in /Users/xusheng/byotc/README.md; review disclosure status before presenting details publicly. -->

---

# How did the checks fail?

<div class="failure-overview">
  <div><strong>Signed EXE</strong><span>The file is trusted; its running code can change.</span></div>
  <div><strong>Creator handles</strong><span>A parent can alter a trusted child.</span></div>
  <div><strong>Signed callback</strong><span>Avast's callback and stack check can be replayed.</span></div>
  <div><strong>Process name</strong><span>A name is easy to copy.</span></div>
  <div><strong>Weak or no gate</strong><span>Panda checks a public value; Comodo has no caller check.</span></div>
</div>

<!-- Overview of failure modes across eight reports, not eight mutually exclusive categories. Signed image: Malwarebytes, Antiy, Huorong. Creator handles: Huorong, 360, Antiy and Avast. Dynamic attestation: Avast. Basename: HitmanPro.Alert. Forgeable caller-owned handshake: Panda. No caller authorization on the sensitive operation: Comodo. The appendix slides provide a bounded example for each. -->

---

# Open Source

<div class="open-source-setup">These tools protect access to their drivers.<br><strong>Both protections can still be bypassed.</strong></div>

<div class="backup-pair">
  <div>System Informer</div>
  <div>VirtualBox</div>
</div>

<!-- Transition to two open-source case studies. Both products use more than a simple executable-name or signer check, but the demonstrated paths still cross their trust boundaries. System Informer has public analysis and a shipped fix: https://xusheng.dev/posts/byotc/main/ . The VirtualBox case is documented in Oracle disclosure S2240975 and the speaker's August 22, 2026 follow-up; use only within the speaker's disclosure plan. -->

---

# System Informer

<div class="backup-statement">A system management tool with a <span class="aqua">privileged driver.</span></div>

<!-- System Informer exposes powerful process-inspection and management capabilities through its kernel driver. Source: https://xusheng.dev/posts/byotc/main/ . This sets up the simplified protection model; do not suggest that every driver message has the same trust requirement. -->

---

# Five trust levels

<div class="si-levels"><span>MINIMUM</span><span>LOW</span><span>MEDIUM</span><span>HIGH</span><strong>MAXIMUM</strong></div>
<div class="backup-statement compact">Process termination requires <span class="aqua">MAXIMUM.</span></div>

<!-- KSystemInformer evaluates cumulative process-integrity levels. MINIMUM checks the backing image; LOW adds signature verification; MEDIUM adds driver process protection; HIGH adds trusted loads/no debugger; MAXIMUM adds secure creation. Message permissions are operation-specific. KphMsgTerminateProcess requires MAXIMUM. Source: https://xusheng.dev/posts/byotc/main/ . -->

---

# Protecting the client

<div class="si-handle-filter"><div>Other process</div><b>→</b><div class="si-filter">Object Manager callback<small>ObRegisterCallbacks</small></div><b>→</b><div class="si-protected">SystemInformer.exe</div></div>
<div class="si-filter-result">New process / thread handles lose <strong>memory-write</strong> and <strong>thread-control</strong> rights.</div>

<!-- System Informer registers Object Manager pre-operation callbacks with ObRegisterCallbacks. Its KphApplyObProtections filters requested access on process/thread handle creation or duplication, stripping dangerous rights from outside callers. This is a simplified depiction; the creator-handle exception follows. Source: https://xusheng.dev/posts/byotc/main/ and https://github.com/winsiderss/systeminformer/blob/553d309edb07934bda9387cff29a82ca1714acbb/KSystemInformer/protection.c#L623-L829 . Microsoft API: https://learn.microsoft.com/en-us/windows-hardware/drivers/ddi/wdm/nf-wdm-obregistercallbacks . -->

---

# The creator already has a handle

<div class="si-creator-flow"><div class="si-evil">Creator</div><b>→</b><div class="si-final">Protected child</div></div>
<div class="si-creator-rights">Under the affected rule, its handle keeps <strong>memory-write rights.</strong></div>

<!-- Under the affected System Informer design, KphpShouldPermitCreatorProcess allowed the actual creator PROCESS_VM_OPERATION and PROCESS_VM_WRITE, even while the object callback blocked later outsiders. The handle is obtained during process creation and remains usable. This is not a claim that CreateProcess universally requires VM_WRITE, or that every creator handle is unrestricted. Source: https://xusheng.dev/posts/byotc/main/ and https://github.com/winsiderss/systeminformer/blob/553d309edb07934bda9387cff29a82ca1714acbb/KSystemInformer/protection.c#L581-L621 . -->

---

# MAXIMUM needs a trusted parent

<div class="si-parent-comparison">
  <div class="si-parent-row"><div>SystemInformer.exe<small>HIGH</small></div><b>→</b><div>SystemInformer.exe<small>MAXIMUM</small></div><b>→</b><div class="si-allowed">Privileged request</div></div>
  <div class="si-parent-row"><div class="si-evil">evil.exe</div><b>→</b><div>SystemInformer.exe<small>not MAXIMUM</small></div><b>✕</b><div class="si-denied">Request denied</div></div>
</div>

<!-- In the affected pre-fix rule, a genuine HIGH System Informer instance could create a child eligible for MAXIMUM after the other checks passed. A direct child of evil.exe could not reach MAXIMUM and therefore could not send KphMsgTerminateProcess. This comparison isolates the secure-creation requirement; the creator-handle weakness was shown on the previous slide. Source: https://xusheng.dev/posts/byotc/main/ . -->

---

# So be the grandparent

<div class="si-grandparent"><div class="si-evil">evil.exe</div><b>→</b><div>SystemInformer.exe<small>HIGH · controlled</small></div><b>→</b><div class="si-final">SystemInformer.exe<small>MAXIMUM · controlled</small></div><b>→</b><div class="si-request">Privileged request</div></div>

<!-- The attacker creates the genuine intermediate System Informer instance and changes it through the retained creator handle. That altered HIGH process creates the genuine final instance; its creator handle can change the MAXIMUM child. Both images can still pass on-disk verification. This was the reported evil-parent chain; present it as the affected pre-fix design. Source: https://xusheng.dev/posts/byotc/main/ . -->

---

# System Informer changed the root

<div class="si-family"><div class="si-root">services.exe<br><small>WinTcb protected</small></div><b>→</b><div>System Informer<br><small>HIGH</small></div><b>→</b><div class="si-final">System Informer<br><small>MAXIMUM</small></div></div>

<!-- The fix roots secure creation in a protected services.exe process rather than letting an ordinary administrator manufacture the chain. It also prevents handle duplication across the verified-process boundary. The maintainer confirmed on August 29, 2026 that the updated signed driver shipped in the Release Channel. Source: https://xusheng.dev/posts/byotc/main/ and the System Informer maintainer's August 29, 2026 email in the disclosure thread. -->

---

# VirtualBox hardens the VM process

<div class="backup-statement">A trusted VM process gets a <span class="aqua">privileged driver session.</span></div>

<!-- Oracle VirtualBox's Windows process-hardening design validates the final hardened process (P3) before it obtains the privileged VBoxDrv session. The related disclosure is Oracle S2240975, initially reported June 12, 2025. See the speaker's August 22, 2026 follow-up email with stock-signed 7.2.6a and 7.2.16 PoCs. Do not conflate this with a generic VirtualBox VM escape. -->

---

# VirtualBox also uses a chain

<div class="si-family"><div>Launcher<br><small>P1</small></div><b>→</b><div>Hardened child<br><small>P2</small></div><b>→</b><div class="si-final">Final VM process<br><small>P3 · trusted session</small></div></div>

<!-- Simplified P1→P2→P3 startup described in Oracle S2240975 correspondence. The final P3 is the process that obtains the privileged driver session. The August 2026 PoC crossed this trust boundary on stock signed releases. The labels are explanatory, not literal process names. -->

---

# The handle disappears; the change remains

<div class="vbox-sequence"><div><strong>1</strong><span>Parent changes child</span></div><b>→</b><div><strong>2</strong><span>Parent closes handle</span></div><b>→</b><div><strong>3</strong><span>Driver validates child</span></div></div>

<!-- Simplified VirtualBox S2240975 sequence. The product's creator exception allowed PROCESS_VM_WRITE for startup; the parent used that handle before the final child opened \\Device\\VBoxDrv, then closed it. Validation observed the signed image and no dangerous live handle, while attacker-controlled data/control flow remained. Windows itself does not require a parent to have VM_WRITE merely to create a child; this was corrected in the speaker's January 21, 2026 Oracle email. The full demonstration used code reuse rather than modifying signed executable pages. -->

---

# Validation passed. Control survived.

<div class="vbox-outcome"><div>Hardened VM process<br><small>attacker-controlled code</small></div><b>→</b><div>Trusted driver request<br><small>accepted</small></div></div>

<!-- Bounded demonstrated result: the speaker's August 22, 2026 follow-up reported stock signed VirtualBox 7.2.6a ROP in P3; 7.2.16's added stack checks blocked that original route, but a separate stock-signed 7.2.16 PoC achieved code execution in confirmed P3 and sent SUP_IOCTL_GET_PAGING_MODE through the inherited driver session. A restricted-session negative control was denied. This is a trust-gated paging-mode query, not proof of arbitrary privileged driver operations or VM escape. Oracle acknowledged the follow-up on August 24, 2026; no vendor fix confirmation was found in the checked thread. Disclosure reference S2240975. -->

---

# A signed EXE can run other code

<div class="failure-explainer">Malwarebytes and Antiy trust the <strong>file on disk</strong>.<br>Injected code runs inside that trusted process.</div>

<!-- Malwarebytes mbamchameleon.sys checks the registering process image for a Malwarebytes signature; code injected into genuine MBAM.exe can register and send termination IOCTLs: https://github.com/magicsword-io/LOLDrivers/issues/435 . Antiy AToolsKrnl64.sys validates an Antiy-signed client image; creator-injected code in genuine AToolWTL.exe reaches its termination IOCTL: https://github.com/magicsword-io/LOLDrivers/issues/442 . The slide describes the general image-versus-runtime gap; Malwarebytes was already covered in detail earlier. -->

---

# The creator still has handles

<div class="failure-explainer">360 and Huorong classify a <strong>genuine client</strong> as trusted.<br>Its creator can still change what runs inside it.</div>

<!-- 360: the creator retained process-write and primary-thread APC capability after 360FsFlt classified genuine QHSafeMain.exe; the injected client reached 360Box64's sandbox termination path. /Users/xusheng/byotc/results/360_total_security_byotc_poc.md . Huorong: the creator retained enough memory-write authority to replace genuine SysDiag.exe's entry point after classification; the altered client reached HR::ActMon termination. /Users/xusheng/byotc/results/huorong_current_assessment.md . Both dynamic tests targeted only sacrificial children. -->

---

# A signed callback can be replayed

<div class="failure-explainer">Avast asks for a <strong>signed callback</strong> and expected stack state.<br>Attacker-controlled code can arrange both.</div>

<!-- In the confirmed Avast 26.8 ARM64 lab chain, authentic overseer.exe began below the needed class, creator-staged private code mapped genuine driver_bridge.dll as SEC_IMAGE, invoked its signed callback with expected stack state, obtained class 4, then used aswArPot.sys to terminate only a sacrificial child. /Users/xusheng/byotc/README.md and https://github.com/magicsword-io/LOLDrivers/issues/438 . This LOLDrivers issue is still open and the research repository flags disclosure status as private; review public presentation scope before presenting technical detail. -->

---

# A process name is easy to copy

<div class="failure-explainer">HitmanPro.Alert trusts <strong>LocalSystem</strong> plus the name <strong>hmpalert.exe</strong>.<br>An unrelated program with that name passed a gated query.</div>

<!-- The bounded test ran the same probe as LocalSystem under two filenames. Ordinary name failed; hmpalert.exe succeeded on harmless PID-query IOCTL 0x2222c4. The sensitive cross-process read handler was identified statically but not exercised. /Users/xusheng/byotc/results/hitmanpro_alert_byotc_assessment.md and https://github.com/magicsword-io/LOLDrivers/issues/439 . Open report; do not claim demonstrated memory disclosure. -->

---

# Sometimes the gate is missing

<div class="failure-explainer">Panda trusts a <strong>public magic value</strong> written by the caller.<br>Comodo exposes a <strong>process-handle request</strong> directly.</div>

<!-- Panda pskmad.sys 1.1.0.23 accepts a public success constant in a caller-controlled shared section. An unrelated unsigned administrator client forged the exchange and dynamically exercised only a benign self-read; the kernel-read branch is a static finding. /Users/xusheng/byotc/results/panda_current_assessment.md . Comodo ccekrnl.dat 12.2.2.8012 exposes an unrestricted process-handle broker to a sufficiently privileged caller; the bounded lab requested PROCESS_TERMINATE only for a sacrificial child. /Users/xusheng/byotc/results/comodo_current_assessment.md . These are conventional BYOVD, not BYOTC. -->

---

# Move AV enforcement<br><span class="aqua">out of the kernel?</span>

<div class="hero-sub">The client-to-service boundary still matters.</div>

<!-- Microsoft's WESP preview proposes moving some AV enforcement into user mode. This may reduce some kernel-driver risk, but it does not by itself authenticate a client or protect that client's runtime integrity. This is a design inference, not a claim about a final shipped architecture. Source: https://blogs.windows.com/windowsexperience/2025/11/18/preparing-for-whats-next-windows-security-and-resiliency-innovations-help-organizations-mitigate-risks-recover-faster-and-prepare-for-the-era-of-ai/ -->

---

# Questions?

<div class="deck-subtitle">xusheng.dev/posts/byotc/main</div>

<!-- Reserve the remaining ten minutes for Q&A. -->
