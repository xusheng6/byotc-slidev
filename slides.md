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
<div class="cover-name">Xusheng Li | Vector 35</div>

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

<div class="variant-statement recap-statement">
  <div>Recap:</div>
  <div>Bring Your Own Vulnerable Driver (BYOVD)</div>
</div>

<!-- Introduce the full BYOVD name before its definition and attack sequence. Define BYOTC only after the access-control slide. -->

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

# Bring Your Own Trusted Caller (BYOTC)

<div class="byotc-definition">
  <div>Bring the client the driver already trusts.</div>
  <div>Run your code inside that client.</div>
  <div>The driver accepts its privileged request.</div>
</div>

<!-- Definition: attackers obtain code execution inside the user-mode program that the driver trusts, then use it to invoke the privileged driver interface. The caller identity check succeeds even though the request now carries attacker intent. Source: https://xusheng.dev/posts/byotc/main/ -->

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

<h1 class="si-long-title">Driver Classifies Clients<br>into Five Trust Levels</h1>

<div class="si-levels"><span>MINIMUM</span><span>LOW</span><span>MEDIUM</span><span>HIGH</span><strong>MAXIMUM</strong></div>
<div class="backup-statement compact">Process termination requires <span class="aqua">MAXIMUM.</span></div>

<!-- KSystemInformer evaluates cumulative process-integrity levels. MINIMUM checks the backing image; LOW adds signature verification; MEDIUM adds driver process protection; HIGH adds trusted loads/no debugger; MAXIMUM adds secure creation. Message permissions are operation-specific. KphMsgTerminateProcess requires MAXIMUM. Source: https://xusheng.dev/posts/byotc/main/ . -->

---

# Driver Protects the Client

<div class="si-handle-filter"><div>Other process</div><b>→</b><div class="si-filter">Object Manager callback<small>ObRegisterCallbacks</small></div><b>→</b><div class="si-protected">SystemInformer.exe</div></div>
<div class="si-filter-result">Newly obtained process / thread handles lose <strong>memory-write</strong> and <strong>thread-control</strong> rights.</div>

<!-- System Informer registers Object Manager pre-operation callbacks with ObRegisterCallbacks. Its KphApplyObProtections filters requested access on process/thread handle creation or duplication, stripping dangerous rights from outside callers. This is a simplified depiction; the creator-handle exception follows. Source: https://xusheng.dev/posts/byotc/main/ and https://github.com/winsiderss/systeminformer/blob/553d309edb07934bda9387cff29a82ca1714acbb/KSystemInformer/protection.c#L623-L829 . Microsoft API: https://learn.microsoft.com/en-us/windows-hardware/drivers/ddi/wdm/nf-wdm-obregistercallbacks . -->

---

# Be the Parent

<div class="si-creator-flow"><div class="si-evil">evil.exe</div><section class="si-create-arrow"><span>CreateProcessA</span><b>→</b></section><div class="si-final">SystemInformer.exe</div></div>
<div class="si-creator-rights">Parent receives <strong>PROCESS_ALL_ACCESS</strong> by default.</div>
<div class="si-creator-access"><span>So the driver still permits:</span><div class="si-permission-names">PROCESS_VM_READ | PROCESS_VM_WRITE | PROCESS_VM_OPERATION</div></div>
<div class="si-launch-warning">Deny these rights, and System Informer may fail to launch.</div>

<!-- CreateProcess returns creator handles with full access by default, subject to security restrictions: https://learn.microsoft.com/en-us/windows/win32/procthread/process-security-and-access-rights . The affected System Informer Object Manager callback could reduce that mask. Its creator exception added KPH_PROCESS_READ_ACCESS (which includes PROCESS_VM_READ), PROCESS_TERMINATE, PROCESS_VM_OPERATION, PROCESS_VM_WRITE. For threads it added KPH_THREAD_READ_ACCESS (including THREAD_GET_CONTEXT), THREAD_TERMINATE, THREAD_RESUME. THREAD_SET_CONTEXT was not in this creator exception. The slide lists selected process rights, not an exhaustive final handle mask. Removing the exception outright was reported to break process initialization; this is why the launch path needed a different trust root. Sources: https://xusheng.dev/posts/byotc/main/ , https://github.com/winsiderss/systeminformer/blob/553d309edb07934bda9387cff29a82ca1714acbb/KSystemInformer/protection.c#L581-L829 , https://github.com/winsiderss/systeminformer/blob/553d309edb07934bda9387cff29a82ca1714acbb/kphlib/include/kphapi.h . -->

---

# MAXIMUM Requires a HIGH Parent

<div class="si-parent-comparison">
  <div class="si-parent-row"><div>SystemInformer.exe<small>HIGH</small></div><b>→</b><div>SystemInformer.exe<small>MAXIMUM</small></div><b>→</b><div class="si-allowed">Privileged request</div></div>
  <div class="si-parent-row"><div class="si-evil">evil.exe</div><b>→</b><div>SystemInformer.exe<small>not MAXIMUM</small></div><b>✕</b><div class="si-denied">Request denied</div></div>
</div>

<!-- In the affected pre-fix rule, a genuine HIGH System Informer instance could create a child eligible for MAXIMUM after the other checks passed. A direct child of evil.exe could not reach MAXIMUM and therefore could not send KphMsgTerminateProcess. This comparison isolates the secure-creation requirement; the creator-handle weakness was shown on the previous slide. Source: https://xusheng.dev/posts/byotc/main/ . -->

---

# So be the grandparent

<div class="si-grandparent"><div class="si-evil">evil.exe</div><b>→</b><div>SystemInformer.exe<small>HIGH</small></div><b>→</b><div class="si-final">SystemInformer.exe<small>MAXIMUM</small></div><b>→</b><div class="si-request">Privileged request</div></div>

<!-- The attacker creates the genuine intermediate System Informer instance and changes it through the retained creator handle. That altered HIGH process creates the genuine final instance; its creator handle can change the MAXIMUM child. Both images can still pass on-disk verification. This was the reported evil-parent chain; present it as the affected pre-fix design. Source: https://xusheng.dev/posts/byotc/main/ . -->

---

# The Fix Relies on PPL

<div class="si-grandparent si-fixed-chain"><div>System<br>Informer<small>LOW</small></div><b>→</b><div class="si-service-step">Create<br>service</div><b>→</b><div class="si-root">services.exe<small>PPL</small></div><b>→</b><div>System<br>Informer<small>HIGH</small></div><b>→</b><div class="si-final">System<br>Informer<small>MAXIMUM</small></div></div>
<div class="si-ppl-note"><div>Windows enforces PPL protection around <strong>services.exe.</strong></div><div>You can't easily inject code into a PPL process.</div></div>

<!-- The weaker System Informer instance creates and starts a temporary service. The service path runs through services.exe, a WinTcb-protected PPL, which creates the HIGH instance; that instance creates the MAXIMUM one. svchost.exe is not the creator in the reported chain. The slide simplifies the first instance as LOW. The fix roots secure creation in a protected services.exe process rather than letting an ordinary administrator manufacture the chain. It also prevents handle duplication across the verified-process boundary. The maintainer confirmed on August 29, 2026 that the updated signed driver shipped in the Release Channel. Source: https://xusheng.dev/posts/byotc/main/ and the System Informer maintainer's August 29, 2026 email in the disclosure thread. -->

---

# System Informer disclosure timeline

<div class="disclosure-timeline si-disclosure-timeline">
  <div><time>Apr 20, 2025</time><strong>Privately reported the issue</strong></div>
  <div><time>Apr 21, 2025</time><strong>Maintainer proposed a fix</strong></div>
  <div><time>Feb 1, 2026</time><strong>Rebased fix entered public history</strong></div>
  <div><time>Aug 29, 2026</time><strong>Signed driver shipped in Release Channel</strong></div>
</div>

<!-- Original disclosure email: Apr 20, 2025, 14:16 +0800. Johnny Shaw's Apr 21, 2025, 00:48 -0600 reply described changes he had implemented and linked the commits, but the updated signed driver was not yet released. Rebased equivalents entered public repository history Feb 1, 2026. On Aug 29, 2026 he confirmed the updated signed driver shipped in the Release Channel. Gmail thread: https://mail.google.com/mail/u/?authuser=xusheng%40vector35.com#all/19651b14e5b133c0 . -->

---

# VirtualBox Chooses a Different Design

<div class="vbox-big-lines"><div>Launch and prepare the child.</div><div><span class="aqua">Then</span> verify it for driver access.</div></div>

<!-- Protection of new handles starts during process creation, but final trust is decided later, when the child opens the driver device. The distinction is between early handle filtering and later integrity verification, not an unprotected launch. Source: VirtualBox src/VBox/HostDrivers/Support/win/SUPDrv-win.cpp, VBoxDrvNtCreate and supdrvNtProtectVerifyProcess; Oracle disclosure S2240975. -->

---

# VirtualBox Launches Three Processes

<div class="vbox-process-chain"><div>Launcher<small>P1</small></div><b>→</b><div>Hardened stub<small>P2</small></div><b>→</b><div class="si-final">VM process<small>P3</small></div></div>
<div class="vbox-subline">Only P3 receives the privileged driver session.</div>

<!-- P1→P2→P3 is the speaker's notation from the January 20, 2026 Oracle email. In source, supR3HardenedWinReSpawn performs two respawns; the stub opens VBoxDrvStub and final VM process opens VBoxDrv. Labels are roles, not three different executable filenames. https://github.com/VirtualBox/virtualbox/blob/main/src/VBox/HostDrivers/Support/win/SUPR3HardenedMain-win.cpp -->

---

# Preparation and Verification

<div class="vbox-full-sequence"><div><strong>P2 creates P3</strong><span>Gets a writable handle</span></div><b>→</b><div><strong>P3 waits</strong><span>P2 prepares P3</span></div><b>→</b><div><strong>P2 closes handle</strong><span>Signals P3</span></div><b>→</b><div><strong>P3 opens VBoxDrv</strong><span>Driver verifies P3</span></div></div>

<!-- This is the P2→P3 sequence within the P1→P2→P3 chain. P3's supR3HardenedEarlyProcessInit signals kSupR3WinChildReq_PurifyChildAndCloseHandles and waits on hEvtChild. P2's supR3HardenedWinDoReSpawn calls supR3HardNtChildWaitFor, supR3HardNtChildPurify, supR3HardNtChildSanitizePeb, then supR3HardNtChildCloseFullAccessHandles. The last routine duplicates the process handle with restricted rights and closes the original, whose creator exception can include PROCESS_VM_WRITE. P2 calls NtSetEvent(This.hEvtChild). P3 resumes and opens the VBoxDrv device; VBoxDrvNtCreate calls supdrvNtProtectVerifyProcess for the unconfirmed VM process, checking live handles, image/process, parent, and token before granting the privileged session. Sources: https://github.com/VirtualBox/virtualbox/blob/main/src/VBox/HostDrivers/Support/win/SUPR3HardenedMain-win.cpp and https://github.com/VirtualBox/virtualbox/blob/main/src/VBox/HostDrivers/Support/win/SUPDrv-win.cpp -->

---

# The Driver Checks Live Handles

<div class="vbox-code-label"><span>SUPDrv-win.cpp:5441</span><span>supdrvNtProtectRestrictHandlesToProcessAndThread()</span></div>
<pre class="vbox-code-block"><code>if (handle targets P3 &&
    rights include PROCESS_VM_WRITE)
    verification fails;</code></pre>

<div class="vbox-code-caption">A writable parent handle must be gone before verification.</div>

<!-- Slide code is explanatory pseudocode, not a verbatim excerpt. Line 5441 is the GrantedAccess/SUPDRV_NT_EVIL_PROCESS_RIGHTS check in pinned VirtualBox revision a85a9b457af490dade7671c2c99de1db899804e1. supdrvNtProtectRestrictHandlesToProcessAndThread scans SystemExtendedHandleInformation and counts handles to the protected process with dangerous rights. The real code exempts handles within the protected process, a bounded CSRSS handle, and certain System handles, and also checks the primary thread and inheritable handles. SUPDRV_NT_EVIL_PROCESS_RIGHTS includes PROCESS_VM_WRITE, PROCESS_VM_OPERATION, and other dangerous rights. This is a current-state handle check, not a history of past writes. https://github.com/VirtualBox/virtualbox/blob/a85a9b457af490dade7671c2c99de1db899804e1/src/VBox/HostDrivers/Support/win/SUPDrv-win.cpp#L5441 -->

---

# Why the Earlier Attack Fails Here

<div class="vbox-contrast"><div>Keep a writable handle?<strong>Verification fails.</strong></div><div>Close it first?<strong>No handle after trust.</strong></div></div>

<!-- The direct evil-parent and evil-grandparent method from System Informer cannot simply carry a writable creator handle into the trusted P3 session. The object callback filters new handles early; the later driver-device open scans for existing powerful handles. This describes the intended barrier, not a claim that VirtualBox is unbreakable. Oracle disclosure S2240975; speaker's January 20, 2026 clarification. -->

---

# A ROP Chain Across Verification

<div class="vbox-attack-flow"><div><strong>P2</strong><span>Prepares P3's return path</span></div><b>→</b><div><strong>P2</strong><span>Closes its writable handle</span></div><b>→</b><div><strong>P3</strong><span>Passes driver verification</span></div><b>→</b><div><strong>P3</strong><span>Returns into the ROP chain</span></div></div>

<!-- The direct System Informer evil-parent attack cannot retain a writable handle across VirtualBox verification. The speaker's stock-signed VirtualBox 7.2.6a PoC instead arranged attacker-controlled ROP execution in P3 before P3 opened the driver, let the parent close its writable handle, and observed the ROP chain execute after the trust check. This was a code-reuse demonstration using signed-image gadgets, a marker write, and a return to the original continuation. It did not demonstrate arbitrary privileged driver operations. Oracle disclosure S2240975; speaker's January 20 and August 22, 2026 emails. -->

---

# Code Reuse Bridges the Trust Check

<div class="vbox-big-lines"><div>We cannot carry the handle through.</div><div>We carry <span class="aqua">future control flow</span> instead.</div></div>

<!-- The analogy is to exploitation under a no-shellcode constraint: reuse code already present in signed images. Scope: the stock-signed 7.2.6a PoC demonstrated a ROP marker and normal continuation in P3. Later 7.2.16 stack-consistency changes blocked that original stack-based route; a separate stock-signed 7.2.16 PoC used a different route to run code in confirmed P3 and successfully submit trust-gated SUP_IOCTL_GET_PAGING_MODE. These are distinct demonstrations, not evidence that this exact ROP chain worked on 7.2.16, nor a VM escape. Speaker's August 22, 2026 Oracle follow-up, S2240975. -->

---

# VirtualBox Disclosure Timeline

<div class="disclosure-timeline vbox-disclosure-timeline">
  <div><time>Jun 12, 2025</time><strong>I reported the bypass</strong></div>
  <div><time>Dec 22, 2025</time><strong>Six months later, Oracle disputed it</strong></div>
  <div><time>Jan 15, 2026</time><strong>Oracle asked for a working PoC</strong></div>
  <div><time>Jan–Jun 2026</time><strong>I was too busy to build the ROP chain</strong></div>
  <div><time>Jun 3, 2026</time><strong>Oracle declined the report</strong></div>
</div>

<!-- Oracle case S2240975. Original report sent June 12, 2025. Oracle acknowledged receipt that day and assigned a tracking number June 16; its first substantive assessment came December 22, 2025 (roughly six months later) and said the second process could not be controlled by its parent. The speaker clarified the time-of-check/time-of-use and P2→P3 sequence in December 2025 and January 2026. On June 3, 2026 Oracle said it did not consider the report a bug because the material had not demonstrated a feasible way to sustain the attack through the final child. The email says it declined the report, not explicitly that the case was closed. Later, on August 22, 2026, the speaker supplied stock-signed 7.2.6a and 7.2.16 PoCs; Oracle acknowledged the follow-up August 24. Sources: https://mail.google.com/mail/u/?authuser=xusheng%40vector35.com#all/197657d45a82b5ee and https://mail.google.com/mail/u/?authuser=xusheng%40vector35.com#all/19b4bc2aac6047de . -->

---

# How About Having AI Make the PoC?

<div class="vbox-patch-reveal">But the bug was already patched in <span class="aqua">April 2026.</span></div>
<div class="vbox-commit-lines"><div><a href="https://github.com/VirtualBox/virtualbox/commit/bcfbf910d779b927a94dbb5d82b6527664093592">bcfbf910</a><span>Return-address checks</span></div><div><a href="https://github.com/VirtualBox/virtualbox/commit/6485c4edbb37dd133c14f3f561c6eab69979000a">6485c4ed</a><span>Full stack-consistency checks</span></div></div>
<div class="vbox-no-notice">I was not acknowledged or notified.</div>
<div class="vbox-daybreak">Codex with Daybreak happily made the PoC. Too late for that route.</div>

<!-- After Oracle declined the report, the speaker realized AI could help build the requested PoC. Public source commits bcfbf910d779b927a94dbb5d82b6527664093592 and 6485c4edbb37dd133c14f3f561c6eab69979000a, authored April 16, 2026, added return-address and full stack-consistency checks. The speaker's August 22 follow-up says these blocked the original stack-based route. The correspondence reviewed does not tie the commits to S2240975 or explain Oracle's reason for them. No notice of these changes appeared in that disclosure thread. Sources: https://github.com/VirtualBox/virtualbox/commit/bcfbf910d779b927a94dbb5d82b6527664093592 and https://github.com/VirtualBox/virtualbox/commit/6485c4edbb37dd133c14f3f561c6eab69979000a . -->

---

# AI Found a Different Vulnerability

<div class="vbox-exploit-lines"><div>.rwxpg holds executable jump-back code in P3.</div><div>P2 alters that page, then closes its writable handle.</div><div>Verification checks the page's protection, but not its bytes.</div><div>P3 later executes the altered stub as a trusted driver client.</div></div>

<!-- VirtualBox v7.2.16 source names a 4 KB read/write/execute PE section .rwxpg in SUPR3HardenedMainA-win.asm lines 627–633. SUPR3HardenedMain-win.cpp constructs executable jump-back stubs there and stores pointers for LdrLoadDll, KiUserApcDispatcher, and KiUserExceptionDispatcher; it later changes the page to PAGE_EXECUTE_READ. During normal VERIFY_ONLY, SUPHardenedVerifyProcess-win.cpp lines 1150–1201 verifies the section's expected protection, but its byte-comparison condition excludes writable sections of the main EXE. Thus a changed .rwxpg can retain expected RX protection while differing in contents, and a later call through a generated stub executes instructions from that page. Sources: https://github.com/VirtualBox/virtualbox/blob/v7.2.16/src/VBox/HostDrivers/Support/win/SUPR3HardenedMainA-win.asm#L627-L633 ; https://github.com/VirtualBox/virtualbox/blob/v7.2.16/src/VBox/HostDrivers/Support/win/SUPR3HardenedMain-win.cpp#L3281-L3290 ; https://github.com/VirtualBox/virtualbox/blob/v7.2.16/src/VBox/HostDrivers/Support/win/SUPHardenedVerifyProcess-win.cpp#L1150-L1201 . The August 22, 2026 Oracle email says the 7.2.16 PoC gained code execution in confirmed P3 and sent trust-gated SUP_IOCTL_GET_PAGING_MODE through its inherited driver session. The attached ZIP is unavailable to this mail connector, so the exact stub selected and trigger in that PoC remain unverified; avoid naming one as the PoC trigger without its source. -->

---

# What About WESP?

<div class="wesp-lines"><div>Windows Endpoint Security Platform moves AV policy to user mode, with shared kernel enforcement.</div><div>Its normal AV client path requires Antimalware PPL and a WESP permission.</div><div>Utilities like System Informer and VirtualBox still have their own driver-client boundary.</div></div>

<!-- WESP is currently a preview, not a universal replacement for vendor drivers. Microsoft's overview says WESP shifts AV enforcement toward user mode while Windows provides the platform: https://blogs.windows.com/windowsexperience/2025/11/18/preparing-for-whats-next-windows-security-and-resiliency-innovations-help-organizations-mitigate-risks-recover-faster-and-prepare-for-the-era-of-ai/ . The speaker's September 10, 2026 analysis of Insider build 29661.1000 in /Users/xusheng/wesp-analysis/REPORT.md finds that the ordinary production partner path requires both the WESP://Permission token attribute and Antimalware PPL (level 0x31), addressing normal administrative injection into that client. Do not say *all* WESP clients require PPL: the preview contains a distinct delegated permission value without the PPL check, whose issuer and scope remain unverified. WESP does not secure legacy vendor drivers or private IPC, and System Informer/VirtualBox are utility examples outside the normal AV WESP migration story. -->

---

# Questions?

<div class="closing-content"><div class="closing-links"><a href="https://xusheng.dev/posts/byotc/main/">xusheng.dev/posts/byotc/main</a><a href="mailto:xusheng@vector35.com">xusheng@vector35.com</a></div><img src="/images/byotc-qr.svg" alt="QR code for xusheng.dev/posts/byotc/main"></div>

<!-- The displayed URL and the QR code both lead to https://xusheng.dev/posts/byotc/main/ . The email address is the speaker's Vector 35 contact. Reserve the remaining ten minutes for Q&A. -->
