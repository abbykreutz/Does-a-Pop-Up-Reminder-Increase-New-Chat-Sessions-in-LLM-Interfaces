# Does a Neutral Reminder Increase New Chat Sessions in LLM Interfaces?

DATA 241: Experiments and Causal Inference | Summer 2026 | Final Project

Abigail Kreutz ([abby.f.kreutz@gmail.com](mailto:abby.f.kreutz@gmail.com)) | Jana Quan | Killian Carter | Luan Ye

## Overview

Recent work suggests that safety- and performance-relevant behavior in large language models can worsen as a conversation accumulates turns. Starting a new chat resets that context, but restarting is easy to overlook mid-task. This project ran a between-subjects randomized controlled experiment through a Chrome extension we built that instruments five LLM web interfaces (ChatGPT, Claude, Gemini, DeepSeek, and Grok). Treatment participants saw a neutral reminder to "create new chats often"; control participants saw the same platform disclaimer without that line.

Our research question: **does a neutral reminder shown when a user loads an instrumented LLM platform increase new-chat creation without reducing overall active time on the platform?**

We used a neutral message rather than a safety-framed warning so that any behavior change could be attributed to salience (the reminder simply makes restarting noticeable) rather than to persuasion or newly formed beliefs about AI safety.

## Experimental Design

- **Randomization**: assignment happens client-side on extension install. The extension generates a random `userId`, hashes it with the djb2 algorithm, and assigns treatment/control by hash parity — targeting an approximate 50/50 split, which we verified both by simulation before launch and by recomputing the hash against logged assignments after.
- **Treatment**: both arms see a pop-up on page load; the treatment arm's pop-up adds "Create new chats often" to the same base disclaimer, holding the interruption itself constant across arms.
- **Instrumentation**: the extension logs two event types to a Firebase Realtime Database — `LOG_SESSION_TIME` (active session duration and new-chat clicks per session) and `NEW_CHAT_CLICK` (a heuristic click detector for a "new chat" affordance, since the five platforms share no common markup).
- **Outcomes**: primary outcome is new-chat creation per participant (count and rate per 30 active minutes); secondary outcome is total active minutes.
- **Recruitment**: volunteers recruited via Slack, LinkedIn, and personal networks; eligible if 18+, an LLM user, and on a Chrome-based browser.

## Results

Of 34 randomized users, 12 remained in the analysis sample after an activity-based exclusion (4 control, 8 treatment). Treatment participants initiated 3.38 more new chats on average than control participants — directionally consistent with our hypothesis — but randomization inference does not reject the sharp null of no effect for any participant (p = 0.298), and realized power at the observed outcome variance was only about 0.06–0.07. The effect on active time was driven largely by which participants remained in each arm (attrition was 75% in control vs. 56% in treatment) rather than by a plausible treatment effect.

**The study is inconclusive on both hypotheses.** Its main contribution is a working, reusable design — randomization, instrumentation, CONSORT-style attrition accounting, and a full analysis plan (randomization inference, heteroskedasticity-robust and clustered standard errors, sensitivity checks) — for a better-powered replication at roughly 47x the sample size.

## My Contributions

My focus was participant-level data quality and the pre-registration/power side of the project, alongside co-developing the study design and finalizing the report.

- Co-developed the study design, hypotheses (H1/H2/H3), and analysis plan, and wrote the rough-draft R Markdown report that Killian iterated on in Google Colab; I finalized that draft into the version we submitted.
- Built the participant-level EDA notebook (`Abby_EDA.ipynb`) — sign-ups over time, event-type and platform breakdowns, and chat/session duration distributions (raw, log-scale, and empirical CDF) — used to sanity-check the Firebase logging pipeline before and after launch.
- Designed and ran the pre-launch power analysis: a simulation-based power curve (in `power_analysis_v2.Rmd`, cited directly in the final report) assuming an ATE of 0.2 additional new chats per 30 active minutes across low/medium/high variance scenarios, plus a supplementary Cohen's-d-based version (`Final_Project_Power_Analysis.ipynb`) bracketing effect sizes against nudge-literature benchmarks (DellaVigna & Linos, 2022; Mertens et al., 2022).
- Owned locking the experiment ahead of the final data pull and building the CONSORT-style participant-flow accounting (recruited → randomized → logged activity → met the active-time threshold → analyzed).
- Contributed to the randomization-inference testing, attrition/compliance diagnostics, and the discussion of study limitations in the final report.
- Co-led participant recruitment and distribution across Slack and LinkedIn.

## Repository Structure

`final_report.pdf`
The final report as submitted — design, instrumentation, results, and discussion in full.

`Final_Report.ipynb`
The analysis notebook (R via Google Colab) underlying the final report: data construction, randomization checks, the primary and alternative treatment-effect models, randomization inference, the CONSORT diagram, and the realized power calculation.

`Abby_EDA.ipynb`
My participant-level exploratory analysis of the logged extension data.

`power_analysis_v2.Rmd` / `Final_Project_Power_Analysis.ipynb`
My pre-launch power analyses: a simulation-based R version (cited in the final report) and a supplementary Python/Cohen's-d version.

## Key Takeaways

A neutral, salience-based reminder moved new-chat creation in the expected direction, but with only 12 analyzed participants the design had roughly 6-7% power to detect a plausible effect — a null result here is evidence the study couldn't decide the question, not evidence the reminder doesn't work. Differential attrition (participants who disliked the reminder could only escape it by uninstalling; control participants had no analogous exit) also meant the secondary outcome, active time, reflected who stayed in each arm more than what the reminder did to any individual's usage. The most durable output of the project is the instrumentation and analysis pipeline itself: a Chrome extension that cleanly randomizes and logs behavior across five LLM platforms, and an analysis plan — randomization inference, CONSORT accounting, sensitivity checks removing the post-assignment exclusion rule — ready to rerun at the sample size (roughly 560 participants) that this design would actually need.

## Note on the browser extension

The Chrome extension that ran this experiment (client-side randomization, Firebase logging, and the on/off toggle used to lock data collection) lives in a companion private repository and isn't included here.
