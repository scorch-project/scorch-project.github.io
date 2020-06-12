---
permalink: /about/
title: "About"
toc: true

---

SCorCH will apply formal analysis tools to detect security issues in capability hardware. We give a brief overview of those concepts (in reverse order) here.


<h1>Capability Hardware</h1>

The underlying idea behind capability hardware is that the architecture is extended with so-called <em>capabilities</em> that provide enhanced security features. This project builds on the the CHERI project led by the University of Cambridge and SRI International since 2010. The  <a href="https://www.cl.cam.ac.uk/research/security/ctsrd/cheri/">CHERI website</a> contains a lot of information on the architecture and developments so far. They summarise CHERI as:

{: .notice--info}
CHERI (Capability Hardware Enhanced RISC Instructions) extends conventional hardware Instruction-Set Architectures (ISAs) with new architectural features to enable fine-grained memory protection and highly scalable software compartmentalisation. The CHERI memory-protection features allow historically memory-unsafe programming languages such as C and C++ to be adapted to provide strong, compatible, and efficient protection against many currently widely exploited vulnerabilities.

As described below, this additional hardware support is very welcome in avoiding certain classes of security vulnerabilities but now requires tools to ensure that software is using it correctly, which is the focus of SCorCH.

In 2019, UKRI announced the <a href="https://www.ukri.org/innovation/industrial-strategy-challenge-fund/digital-security-by-design/">Digital Security by Design Challenge</a> in partnership with ARM. As part of this challenge, ARM <a href="https://www.arm.com/blogs/blueprint/digital-security-by-design">announced the Morello Board</a>, a CPU, SoC, and board integrating CHERI support into an ARMv8-A baseline system. The CHERI website provides <a href="https://www.cl.cam.ac.uk/research/security/ctsrd/cheri/cheri-morello.html">further details</a> on what this will entail.


<h1>Making things Secure</h1>

<p>
SCorCH will focus on ensuring security by supporting the formal analysis of two classes of vulnerability mitigations.
</p>

<h2>Memory Safety</h2>

<p>The majority of code that runs will, at some point, rely on code written in C or C++. Much of our low-level infrastracture is written directly in C/C++ and the ecosystems of many higher-level languages often use C/C++ under the hood. A well-known issue with C and C++ is that it is not <em>memory safe</em>, e.g. it is possible to access arbitrary parts of the memory, which can cause many problems.</p> 

<p align="center">
<img src="/assets/images/chip.png">
</p>

<p>Common memory safety issues are well-known. For example, the classic <em>stack overflow</em> where a program writes off the end of a stack into memory it should not access. Other issues are more subtle, such as <em>use-after-free</em> where a dangling pointer is dereferenced.</p> 

<p>
So why do we care about memory safety? In a recent talk, Matt Miller from the Microsoft Security Response Center explained that <a href="https://github.com/microsoft/MSRC-Security-Research/blob/master/presentations/2019_02_BlueHatIL/2019_01%20-%20BlueHatIL%20-%20Trends%2C%20challenge%2C%20and%20shifts%20in%20software%20vulnerability%20mitigation.pdf">roughly 70% of vulnerabilities addressed through security updates were memory safety issues</a>. Malicious actors can take advantage of memory safety issues to subvert protection mechanisms, leak information, and more.
</p>

<p>What can we do? CHERI aims to build memory safety protection in at the architectural level via <em>smart pointers</em> called capabilities. However, this requires software to make proper use of these pointers. SCorCH will use formal analysis techniques (model checking) to statically analyse C programs using CHERI to detect potential memory safety issues.
</p>

<h2>Compartmentalisation</h2>

<p>Ultimately, given the complexity of modern systems, exploits and attacks are inevitable. So how do we minimise the impact if it happens? We want to restrict the damage to the smallest part of the system that we can. This is the idea behind compartmentalisation, to put up walls between distinct components of a system and enforce that separation.</p>

<p align="center">
<img width="400" src="/assets/images/compartment.png">
</p>

<p>CHERI provides hardware-enabled support for compartmentalisation through memory tags. However, again we need to ensure that the software is using these mechanisms correctly. SCorCH will utilise a combination of static and dynamic analysis to ensure that compartmentalisation strategies are implemented correctly in code.</p>

<h1>Formal Analysis</h1>

<p>SCorCH will leverage powerful and mature formal analysis techniques to provide a <em>SCorCH verification stack</em> that can automatically detect security vulnerabilities in code utilising capability hardware.</p>


<h2>Model Checking</h2>

<p>Abstractly, model checking is the process of checking whether a finite-state model or abstraction of a system meets a given specification. This specification may be as simple as <em>we never reach a bad state</em> or may relate to the order in which states of the system are reached (e.g. a pointer is never dereferenced after it is freed).</p>

<p align="center">
<img width="400" src="/assets/images/mc.png">
</p>

<p>Algorithmically, model checkers work by exploring the state space of the system. This runs into scalability issues when the state space is ample and decades of research has provided a range of solutions to this issue, including <em>symbolic</em> representation of the state space, and <em>bounded</em> model checking where the state space is explored at iteratively greater depths.</p>

<p>SCorCH will utilise two bounded software model checkers - <a href="http://www.esbmc.org">ESBMC</a> and <a href="http://www.cs.cmu.edu/~modelcheck/cbmc/">CBMC</a>, developed at Manchester and Oxford, to search for memory safety issues and will employ hardware model checking techniques to check security properties of the underlying architectural design.</p> 

<h2>Runtime Verification</h2>

<p>Whilst static analysis techniques such as model checkers have enjoyed considerable success in recent years, they can still struggle when we bring together many components of a system, thus exploding the state space. Additionally, static techniques must always approximate actual behaviour as some things are necessarily unknown.</p>

<p align="center">
<img width="400" src="/assets/images/rv.png">
</p>

<p>Conversely, runtime verification is a lightweight dynamic analysis technique that aims to check that the current run of a system conforms to the given properties. SCorCH will explore a hybrid, collaborative, approach where runtime verification and (bounded) model checking cooperate to detect security vulnerabilities.</p>

<h2>Automated Reasoning</h2>

<p>Automated theorem provers are the workhorse of the verification community. A typical approach is to translate a given task into a series of logical <em>proof obligations</em> to be handled by a theorem prover. This process includes the above model checking tools, as well as tools for directly proving security properties of hardware designs (utilised elsewhere in the CHERI ecosystem).</p>

<p align="center">
<img width="400" src="/assets/images/tp.png">
</p>

<p>
To reason about proof obligations involving capabilities, it is necessary to translate their properties into logic alongside the property being checked. SCorCH will extend world-leading automated theorem provers (Vampire and iProver) with native support for the theory of capabilities, thereby improving their performance in this setting.</p>
