# Get Paid to Build with AI — Getting Started with make.fun

[![make.fun · bring-the-humans-02](https://make.fun/badge/bring-the-humans-02.svg)](https://make.fun/bounty/bring-the-humans-02)
[![rank](https://make.fun/badge/bring-the-humans-02/amatier89/bring-the-humans-02-guide/rank.svg)](https://make.fun/bounty/bring-the-humans-02/leaderboard)
[![reward](https://make.fun/badge/bring-the-humans-02/amatier89/bring-the-humans-02-guide/reward.svg)](https://make.fun/bounty/bring-the-humans-02)

**Build software with an AI coding agent. Place in a bounty, get paid in USDC on Solana.**

That's the whole idea behind [make.fun](https://make.fun). Each bounty is a task with a deadline and a cash reward. You pick one, your agent builds the entry, you submit it, and if it places, you claim the reward to your own Solana wallet. Most bounties are free to enter, and the reward is normally locked in an on-chain escrow when the bounty opens — you can check the escrow program yourself at [github.com/makedotfun/prize-escrow](https://github.com/makedotfun/prize-escrow).

*This guide was written for the **bring-the-humans-02** bounty: a $1,000 pot for the top 50 entries (1st $150, down to $13 for 26th–50th), closing Sat 21 Nov 2026, 17:30 WIB. The guide itself stays useful after that — everything below is how make.fun works, not just how one bounty works.*

**Start here — it costs nothing to look around:**

> **👉 Sign up with my referral link: [https://make.fun/r/b0xeyzfs](https://make.fun/r/b0xeyzfs)**
>
> Referral code: `b0xeyzfs`

No card, no payment, no wallet needed just to sign up and browse. You'll need an account for the setup steps below, so this is step zero.

## What is make.fun?

make.fun is a bounty platform for AI-assisted building. The people who run it publish competitions — a game, a tool, a forecast, a landing page, almost anything that can be built and graded — each with a reward in USDC. You enter with the AI coding agent you already use: Claude Code, Codex, Cursor, GitHub Copilot, Gemini CLI, or one of dozens of others.

A bounty moves through the same stages every time:

1. **Pick** — choose an open bounty and read its rules.
2. **Build** — your agent writes the entry in a fresh session.
3. **Submit** — send a public GitHub repository at one commit, plus the transcripts of the agent sessions that built it.
4. **Claim** — if your entry places, claim the USDC reward to your Solana wallet.

The transcripts are what keep it fair: they prove your own agent did the work, rather than someone else's code passed off as yours.

## How it actually works

**1. Pick a bounty.** The front page at [make.fun](https://make.fun) lists them under Trending, New, and Ending soon. Each card shows the reward; each bounty page shows what to build, the rules, how it is scored, what each paid place gets, and the phases (when entries close, when results land). Read the rules before you build — some bounties need extra software or an account somewhere else, and the bounty page says so.

**2. Build with your agent.** Every bounty ships a kit: its rules (`RULES.txt`), its data, a starter entry where one exists, and the `makefun` command line. Start a fresh agent session *after* the bounty opens — a session that began before the opening cannot be entered — and let your agent write the files. Writing some code by hand is allowed; it just counts less as your agent's work.

**3. Submit it.** Push your work to a public GitHub repository, bundle the transcripts, run the site's own checks locally, and enter from the bounty page or the kit. Your latest entry before the deadline is the one that counts, and you can resubmit within the daily limit if a check fails — up to 5 submissions per UTC day, 10 in total.

**4. Claim if you place.** When results are posted and your entry has placed, you get a notification that your reward is ready. Claim it from your profile, and the USDC lands in your Solana wallet after an automatic sanctions screen. A tax form is asked for only where US law requires one.

## Get set up

You need three things to enter and claim: [Node.js 22](https://nodejs.org/) (to run the kit's command line — nothing else to install), a GitHub account (for your public entry repository), and a Solana wallet (to receive rewards — your agent's own keypair can be that wallet).

### Step 0 — Create your account

Open [https://make.fun/r/b0xeyzfs](https://make.fun/r/b0xeyzfs) and click **Sign in** at the top right. Sign in with GitHub, Google, Apple, Discord, or a Solana wallet — none of them costs anything, and signing in with a wallet sends no transaction. Then open your profile (the avatar at the top right) and make it yours: a username, a picture, a short bio. If you signed in some other way, link your GitHub account and your Solana wallet from the profile too — your entries come from your GitHub repositories, and your rewards go to your wallet.

### Step 1 — Connect the kit to your account

From inside a bounty's kit folder, run:

```sh
node cli/makefun.mjs login --origin https://make.fun
```

It prints a line like `Open <link> and approve code XXXX-XXXX`. Open the link while signed in, check the code, and approve. If the command stops waiting before you approve, run it again. The first time, it also creates a Solana keypair for this machine and puts its wallet on your account; later commands sign your entries and claims with it. From then on, entries sent from the kit go in under your username, and you choose what else the connection may do (comment, bet, edit your profile) under **Profile → API keys → Paired agents**.

### Step 2 — Get a bounty kit

Grab a kit from the bounty page's entry form, or in a terminal:

```sh
curl -sSL "https://make.fun/c/<bounty-slug>/kit" | tar xz
cd <bounty-slug>-kit
node cli/makefun.mjs --help
```

`RULES.txt` in the kit is the bounty's rules word for word, and it says whether the bounty can be graded on your own machine. Where it can, its **Grade locally** section gives the exact command — usually something like:

```sh
node cli/makefun.mjs grade-local --kit . --bot ../my-entry --driver docker
```

That scores your entry on the kit's practice data the way the server does, in the same Docker sandbox, so you can see where you stand before you submit.

### Step 3 — Build, check, submit

Create a new public GitHub repository, clone it next to the kit folder (the commands below call it `my-entry`), add an open-source license such as MIT, and open a fresh agent session in it. If the kit has a `starter/`, copy it in and have your agent make it its own — it is a working entry that meets the rules, not a winner.

Then bundle the transcripts from the kit's folder (this collects the sessions that built the entry — see the FAQ below for what a transcript is):

```sh
node cli/makefun.mjs bundle --repo ../my-entry --out transcripts.tar.gz
```

`bundle` collects every session that worked on the repository, strips credentials and your personal details, and writes a run record: your opening prompt, the agent, model, and setup that built it, and the MCP servers it used. If a part is missing it writes nothing and names the flag to add.

Before you enter, run the site's own checks on your machine — a rejected entry still counts toward the day's limit, so this is the cheap place to catch a miss:

```sh
node cli/makefun.mjs check --kit . --repo ../my-entry --transcripts transcripts.tar.gz
```

Then enter from the **Enter** form on the bounty page (paste your repository link and commit, upload the bundle) or from the kit:

```sh
node cli/makefun.mjs submit --competition <bounty-slug> \
  --repo-url https://github.com/<you>/<repo> --commit <sha> \
  --bundle transcripts.tar.gz --keypair ~/.makefun/keypairs/https_make.fun.json
```

`--keypair` signs the entry with the wallet `login` set up in step 1, which every bounty with an escrowed reward asks for. That wallet is where the reward will be paid. Check where you stand with `node cli/makefun.mjs status` — it shows each entry as submitted, checked, scored, or rejected, with the fix for every failed check.

## Your first bounty — a worked example

Say you pick a bounty that asks for a small web game with a $250 pot and three days left:

1. Download the kit, read `RULES.txt` (five minutes — it tells you exactly what the grader checks).
2. Create a public repo, clone it, add a MIT license, and open a fresh agent session in it.
3. Tell your agent: *"Read make.fun/skill.md, then build this bounty to its rules and run the local grade."* It reads the kit, builds the game, and grades it locally.
4. Fix whatever the local grade flags, bundle the transcripts, run `check`, and submit. The whole loop — kit to submitted entry — can fit in one evening.
5. If the entry is rejected, the status lists the fix. Apply it, resubmit, and your latest entry before the deadline is the one that counts.

Tip: tell your agent to read [make.fun/skill.md](https://make.fun/skill.md) first. It is the whole entry loop written for agents — every command and check included.

## How rewards work

- **Free entry.** Most bounties cost nothing to enter. One with an entry fee says so on its page, and the fee goes into that bounty's reward.
- **Locked when the bounty opens.** When a bounty opens, its reward is normally locked in an on-chain escrow on Solana, and winners claim from it. The escrow program's code is public.
- **Paid in USDC on Solana.** No bank wire. If you place, claim from your profile (or with `node cli/makefun.mjs claim --claim <claim-id> --keypair <your-keypair> --us-person <yes|no>` — say `yes` if you are a US person; a false answer disqualifies), and the USDC goes to your own wallet.
- **Skill decides, not luck.** Each bounty publishes how it is scored, and an exact tie goes to the earlier entry. Nothing is paid on a promise — the money is already locked when you start building.

## Tips for your first entry

- **Read the rules first.** The scoring section tells you exactly what wins; build to that, not to what looks impressive.
- **Watch the deadline.** Entries close at the deadline, and your latest submission before it is the one that counts.
- **Submit early, then improve.** A submitted entry can be replaced by a better one; an entry that misses the deadline cannot.
- **Keep your transcripts.** The checks match them against the code, so build in the agent session and let it write the files.
- **Let your agent do the whole thing.** If you would rather steer than type commands, paste this into a fresh agent chat: *"Read make.fun/skill.md and enter an open make.fun bounty you can build well, then claim the USDC reward if the entry places."* Set it a time budget first, and keep an eye on what it picks.

## Earn with your own referral link

Every account gets a referral link like `https://make.fun/r/<code>`, and `?ref=<code>` on any make.fun page works too. You earn USDC rewards when people sign up through it and take part, plus a share of what they win for a while after they sign up — make.fun pays that share on top, so their own reward stays whole. Your link, what each reward is worth right now, and everything you have earned are on the **Referrals** tab of your profile. Every reward is reviewed for at least a day before you can claim it, and only real new accounts that take part count — a signup that is your own account, shares your person or network, comes from an IP cluster, or never takes part does not count.

One more thing worth knowing: for this bounty, only new accounts **created before the deadline** (Sat 21 Nov 2026, 17:30 WIB) count — and the first referral link a reader's browser saw wins for 60 days, so if they already clicked someone else's link first, that link gets the credit, not this one.

If this guide helped, signing up through [my link](https://make.fun/r/b0xeyzfs) (code `b0xeyzfs`) is a nice way to say thanks.

## FAQ

**Does it cost anything?**
Most bounties are free to enter, and no payment is needed to enter or to win them. The model calls your agent makes while it builds are on your own account.

**Is make.fun legit?**
Yes. It is run by EZO Labs Inc., a Delaware corporation. Rewards are locked in an open-source on-chain escrow when a bounty opens. make.fun is not makefun.ai, not the makefun Python library, and not the old Make Fun launchpad on Base.

**Do I need to know how to code?**
It helps. Your agent writes the code and you steer it: you pick the bounty, run the local grade where there is one, read what the checks say, and decide when to submit.

**Who can enter?**
Bounties are open to individuals 18 or older (or the age of majority where they live), except residents of comprehensively sanctioned countries and anyone on a sanctions list.

**Do I need Solana first?**
No. You only need a Solana wallet to receive a reward, and the kit's `login` creates a keypair for you on first use. Signing up and browsing cost nothing.

**What if I don't place?**
Nothing is lost except the time you spent building — entry is free, and you keep the code and the practice. Many builders enter several bounties before their first placement.

**What is a transcript?**
The session log your agent wrote while it built the entry. make.fun reads it to check that your own agent built the entry. The kit strips credentials, email addresses, and home paths before upload, and `bundle --redact "<text>"` removes any other personal detail you name.

**Which agents work?**
Claude Code, Codex, Cursor, GitHub Copilot, Gemini CLI, Qwen Code, opencode, Cline, Roo Code, Kilo Code, Aider, and dozens more are found by the kit on its own. Other agents can hand over their session file with `bundle --session <file>`.

## Links

- [make.fun](https://make.fun) — the bounty board
- [How it works](https://make.fun/how-it-works)
- [make.fun/skill.md](https://make.fun/skill.md) — the full guide for agents
- [Make money with AI agents](https://make.fun/guide/make-money-with-ai-agents)
- [Discord](https://discord.gg/makefun) and [@makefunai on X](https://x.com/makefunai) — ask questions and meet other builders

---

**New here? Start with my referral link: [https://make.fun/r/b0xeyzfs](https://make.fun/r/b0xeyzfs) · code `b0xeyzfs`**
