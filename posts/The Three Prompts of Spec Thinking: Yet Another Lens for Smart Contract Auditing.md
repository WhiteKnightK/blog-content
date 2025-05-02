---
title: "The Three Prompts of Spec Thinking: Yet Another Lens for Smart Contract Auditing"
date: 2025-04-03
updated: 2025-04-10
author: "Dravee"
categories: ["Smart Contracts", "Security", "Thinking"]
tags: ["audit", "methodology", "smart-contracts", "security"]
excerpt: "A minimalist but powerful framework for analyzing code through a threat modeling lens. Uncover vulnerabilities by examining expectations, possibilities, and assumptions."
---

# The Three Prompts of Spec Thinking: Yet Another Lens for Smart Contract Auditing

## Introduction

Smart contract auditing is often described as finding bugs in code (after all, a bug report is the expected output).

But many of you may have already realized this:

> Bugs are rarely just coding errors. They're broken assumptions. They're mismatches between the user's *expectations*, what the developer *intended*, and what the code *actually does*.

It's been shown time and time again: checklist-driven reviews are never enough. All top auditors eventually drop them. Instead, to uncover critical vulnerabilities and truly understand the code, they shift—consciously or not—toward a **spec thinking** mindset. One where checklists are made on the fly, born from uncovering assumptions and implicit rules.

This article introduces a minimalist but powerful framework I came up with, resulting from my years of work with Certora: **The Three Prompts of Spec Thinking**. It's a way to analyze code through a threat modeling lens. I hope it'll serve you well!

## What Even Is a Smart Contract (From First Principles)?

An **asset management system**, where **actors** interact to gain specific **benefits** through **actions**.

### What's an ASSET?

Money, power, or ownership assigned to a user.

👉 Try this cue: *"If it's something that can be transferred, it's an asset."*.

### What's an ACTOR?

Any interactooor with a smart contract:

* Other protocols (dependencies)
* Other users (permissioned or not)
* Yourself (as an attacker, alt account, or regular user)
* The protocol itself (`address(this)`)

👉 Try this cue: *If it can be identified by an `address`, it's an actor.*

### What are ACTIONS?

An **action** is when an actor initiates interaction with the system. This triggers some logic that takes in:

* Input parameters
* State variables
* External dependencies

…and produces:

* State changes (on the Smart Contract itself or on external dependencies, like balances)
* Returned values
* Either a success or a revert

The flow may span multiple steps, touch multiple assets, or impact multiple actors.

🔗 I recommend re-reading my post here: [https://justdravee.github.io/posts/thread-state-machine/](https://justdravee.github.io/posts/thread-state-machine/)

## What Even Is a Bug (From First Principles)?

Bugs fall into **three categories**:

### 1. **Missing Paths**

The user's expectations aren't met—because the code doesn't even support them.

> Example: You can deposit money… but never withdraw. The protocol's core logic isn't there.

This reflects a failure in **liveness**: something *good* was supposed to happen, but doesn't.

### 2. **Incorrect Happy Paths**

The feature exists and runs, but it doesn't behave correctly. Think of integration mistakes or faulty state transitions.

Think of it as "wrong result, right code path".

> Example: Rewards are miscalculated due to rounding errors or state not updated correctly

Often caught through unit tests, these are straightforward to spot but easy to miss in complex flows.

**Post-publication clarification (thanks to thoughtful feedback from our CTO):**

It can get quite confusing to distinguish between **"Incorrect Happy Paths"** and **"Unexpected Paths"** (more on that below).

Try to think as **"Incorrect Happy Paths"** being **"Unexpected Data-Flows"**, while **"Unexpected Paths"** would be **"Unexpected Control-Flows"**.

**"Missing Paths"** can be thought as **"expected entry point doesn't exist."**

### 3. **Unexpected Paths**

The system does *more* than intended. Abusable edge cases, unsafe flows, and poorly scoped permissions emerge here.

> Example: An attacker uses reentrancy to drain funds.

This is where your **threat modeling** kicks in the hardest.

## What Even Is a Critical Severity Issue (From First Principles)?

A critical issue arises when **an asset is compromised**. Here are four key threat categories:

* **Theft** – Unauthorized transfer or drain of user/system assets
* **Lock** – Permanent inaccessibility of funds or system features
* **Free-Riding** – Gaining rewards or access without contributing (e.g., bypassing fees, yield farming without staking)
* **Usurping** – Illegitimately taking over ownership, permissions, or governance

If an attacker can exploit the contract to trigger any of these, you're likely looking at a critical bug.

## What are Properties and Invariants?

These are your **specs**—they represent how the system is *supposed* to behave.

### Invariants

Statements that must *always* be true, regardless of how the contract is used (before and after any action).

> "The total supply must always equal the sum of all user balances."

These need some specific invariant testing tools (Certora Prover, Halmos, Foundry's invariant tests, Echidna, etc.)

### Properties (aka Functional Correctness Properties)

These define what should hold true after executing a specific function or state transition. They reflect **the intended behavior** of the protocol:

> "After withdrawing, the user's balance should decrease by the withdrawn amount."

**Postconditions** are a common way to express functional correctness. However, to verify that the contract's behavior matches its intent, you may need to check multiple rules or invariants **together**.

A full correctness property may require reasoning across multiple states and functions—often beyond the reach of unit tests.

A "Functional Correctness Property" (commonly seen as a `rule` with the Certora Prover) can include:

* Postconditions (see assert, require or satisfy statements)
* Assumed invariants within the function (see requireInvariant statements)
* Expected changes across multiple state variables (see Tracking changes with ghosts and hooks)

Together, they form the logic layer of the system's **expected functionality**.

### Why Do They Matter?

* **Bugs break them**: A false property = something's wrong.
* They help you **reason about intent**, not just code (Ever heard of code blindness? Ever heard of re-implementing functionalities your way to compare implementations?).
* They're easier to **brainstorm** than full path coverage.
* They work best when written in plain English first.
* I'll say this again, PLAIN ENGLISH IS SEVERELY UNDERRATED. More on that right now.

### 3-Layer Spec Abstraction

1. **High-level spec (Plain English)**: What should users be able to do?  
   * "A user cannot lose funds unless they voluntarily withdraw or trade."
2. **Mid-level spec (State Machine Model)**: What State variables enable that behavior?  
   * Track states like `Deposited`, `Locked`, `Withdrawn`.  
   * "From `Locked` → `Withdrawn` is only allowed if time has passed."
3. **Code-level spec**: What are the function-level conditions and transitions?  
   * Assert `if state == Locked && now >= unlockTime, then withdraw() allowed`.

🎯 This ladder helps you anchor all logic to something meaningful.

## Teachings from Formal Methods

### **Liveness Properties** = *Design-Level expectations* or "Something good eventually happens."

It's the **Design intentions** and **Implementation** layer.

They represent **what the system should enable**—they're about capability, availability, progress.

These align with :

* **What the user expects** to be able to do.
* **What the developer intended** (happy paths).
* **Missing or incorrectly implemented functionality.**

These map to **Bug Categories 1 & 2**:

> 1. **Missing functionality** (e.g., can't withdraw, can't claim rewards)
> 2. **Incorrect happy paths** (e.g., claims don't subtract balance, refunds miscalculated)

👉 Try this cue: *"Eventually, X must happen."*

**Post-publication clarification (thanks to thoughtful feedback from our CTO):**

Liveness is quite hard to prove (e.g. Solvency), but it's also one of the most interesting concepts.

In Formal Verification, one tip that is sometimes applicable is to reduce the problem to a set of safety properties.

### **Safety Properties** = *Control-level guarantees* or "Nothing bad happens."

It's the **Enforcement layer**.

They protect **what should never be allowed**—they're about bounds, rules, and forbidden states.

These align with **Abusable Behaviors**:

* **Over-permissiveness**
* **Edge cases**
* **Unexpected paths and flows**

These map to **Bug Category 3**:

> **The system can do more than it should** (e.g., double-claim, free rewards, theft via desync)

This is where most ideas for security-checks can be made. This is also where you can get quite creative in tackling assumptions or inferring behaviors on rare code paths (e.g. downcast overflow etc.).

👉 Try this cue: Start a sentence with *"Should only…"* or *"Should never…"*. Given that assumptions are conditions that are expected to be true, but aren't enforced by code: this here is where the implicit truths can be revealed explicitly, and thus can be checked for hidden bugs

### **Temporal Logic Thinking** = *Sequence/flow correctness*

A bit between both of the above.

These properties help define when **liveness should occur**, and when **safety must remain true** through transitions.

* Think in terms of: *Before/After/Always/Eventually must be true.*
* Super helpful for flow bugs, sequence errors, and edge states.

## The Three Prompts of Spec Thinking

### 1. **What is expected?** — Define Intended Behaviors (The Value Layer)

👉 Try this cue: "What *should* happen? What is the system *supposed* to do? What is the user *meant* to be able to do?"

Start from the surface: interfaces, comments, docs (if you're lucky)-and your own intuition.

What are the core promised behaviors of the system?

What flows or features need to be there for the system to work?

This sets the baseline: the **happy path** and the **liveness expectations**.

🔍 Finds (spec/design level issues, meaning *what should exist but doesn't*):

* Missing functionality
* Broken happy paths
* Failed integrations
* Violated liveness properties

🐛 Catches (actual bugs, meaning *what breaks at runtime*):

* Missing state transitions
* Misimplemented logic
* Incorrect or incomplete user flows

### 2. **What is allowed?** — Explore Possible Paths and Inputs (The Possibility Layer)

👉 Try this cue: "What can the system be made to do — whether intended or not?"

You start exploring what's **possible**, not just what was intended. You're hunting for **unexpected paths** (e.g., an unexpected path opened by a downcast overflow, an unexpected path opened by state manipulation before the user's call (frontrunning), an unexpected path opened by abused call orders or repeated calls…).

This is how you uncover what's **possible**, not just what was **intended**.

🔍 Finds (spec/design level issues, meaning *what should exist but doesn't*):

* Over-permissive flows
* Missing require statements
* Unsafe edge cases
* State exposed to frontrunning or ordering risk
* Violated safety properties

🐛 Catches (actual bugs, meaning *what breaks at runtime*):

* Incorrect input handling
* Edge-case abuse
* Reentrancy paths
* Unchecked states and unvalidated assumptions
* Dangerous combinations of state

💡 There's a beautiful post from [https://x.com/0xCharlesWang](https://x.com/0xCharlesWang) about the challenge of uncovering call paths here: [https://x.com/0xCharlesWang/status/1768004932936880273](https://x.com/0xCharlesWang/status/1768004932936880273)

### 3. **What was assumed but never enforced?** — Write Your Properties (The Enforcement Layer)

👉 Try this cue: "What does the code *assume*, but never check?" 👉 Answer with "Should only…" or "Eventually, … must happen"

Now you flip into **spec thinking** mode:

* What invariants or preconditions were silently relied upon?
* Are there conditions the code assumes to be true but never checks?

This is where you get precise and **start drafting properties**, invariants, or test rules to validate or break assumptions. You transition from questioning the system to **formally stating what must hold true**. This is where you surface the system's **implicit truths**-and make them **explicit**.

Use this phase to build:

* Properties (functional correctness)
* Invariants (always-true conditions)
* Flow assertions (e.g. ordering, permissions, thresholds)

🔍 Finds (spec/design level issues, meaning *what should exist but doesn't*):

* Implicit invariants
* Precondition gaps (Insufficient bounds for input parameters, state variables, or external dependencies)
* State desynchronization
* Order-dependency bugs

🐛 Catches (actual bugs, meaning *what breaks at runtime*):

* Invariant violations
* External/internal state mismatches
* Missing guards on critical paths

🧠 This is where your deepest bugs hide—and where your custom "checklist" builds itself naturally.

## Sink-Source Thinking: A Strategy for Using the Prompts

> "But ser… path explosion is real!"

Yep. That's why we use **Sink-Source Thinking** — an old gem mentioned by samczsun — to **prioritize effort** during audits or bounty hunting.

### What's a Sink?

Sinks are the **endpoints** of asset movement, permission changes or critical state transitions. They represent **the things you're trying to protect**.

These are the moments where:

* **Assets can be stolen**
* **State can be corrupted**
* **Control can be hijacked**
* **Trust can be broken**

#### Common examples of sinks

* Funds being transferred out
* A user receives accounting-based rewards
* An address is granted elevated permissions
* Ownership or authority is updated
* Tokens are minted or burned
* Critical system variables are modified

> **If a mistake here causes financial loss, permanent lock, or protocol takeover — it's a sink.**

👉 Try this cue: "If this goes wrong, what's the worst that could happen?"

### Why Start from Sinks?

Because **you don't have time to look at everything**.

You're not here to simulate every code path — you're here to ask:

> *"What are the most catastrophic outcomes possible in this system?"*

### Trace Backward from the Sink

Once you've identified a sink, work **backwards** from it:

* What inputs, state variables, or external calls **influence** that sink?
* What assumptions are made before it's triggered?
* Are those assumptions **validated** or just **hoped for**?

This turns **path explosion** into **a guided search for real threats**.

👉 Try this cue: "How could this sink be triggered? What could reach it?"

### **Key strategy**

1. **List your sinks** — all spots where real-world value or critical control is affected.
2. **Ask: what could make this sink fire unsafely?**
3. **Trace back** through:  
   * **Input params**  
         * Assume all inputs are evil (edge-cases)  
   * **Storage reads**  
         * Assume it can change at any time (especially before/after calls: "That balance you're reading? It might have changed. Are you relying on something staying the same?")  
         * First identify the bad states, then worry about actual state reachability. **By over-approximating**: you're staying focused on uncovering all threats, even unlikely ones.  
   * **External calls**  
         * Assume them to be chaotic liars. In the Certora Prover, the external calls are Havoc by default, meaning they can change any other state, can return unexpected values, can revert… this is **over-approximation** at its finest.  
   * **Call order/patterns** (reentrancy, timing, race conditions)
4. Use the **Three Prompts** to challenge everything influencing that sink.

## Final Insight

Of course, you won't manually explore all paths. It's usually impossible, and you don't have to.

But your job *is* to:

* Find where unsafe flows *could* happen
* Suggest guards or blocks on weird-but-plausible paths

Sometimes the impact isn't clear yet. Bug bounty hunters often spend days maximizing the impact after finding a Bug. That level of depth takes time. If you're auditing: maximum coverage should be your aim.

Remember: **your goal is to secure the protocol during the time you had to review it**.

It's perfectly fine (and often strategic) to maximize impact when bug bounty hunting (but someone may frontrun you). It can also be a strategy during contests.

If you want to brute-force some assumptions: use Fuzzing or Formal Verification tools!

### **In summary:**

1. **Use "Sink-Source Thinking" to guide your focus in identifying and hunting down the critical paths (the most impactful flows).** This is how you trade path explosion for precision.
2. **Use the "Three Prompts" to uncover what can go wrong along the way.** This is how you turn hidden risks into concrete findings.

The **Audit Loop** is:

1. Start with **what's expected**
2. Identify the critical **sinks**
3. Trace **what's allowed** — map all **levers of influence**
4. Challenge assumptions by writing precise **properties**

**Repeat. Refine. Uncover the truth.**

**TLDR: Start with what matters. Follow what's possible. Catch what's unsafe.**

Cheers everyone!
