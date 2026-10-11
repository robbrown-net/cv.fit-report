# cv.fit-report

How well does this CV fit this job, and **why**? An AI-assisted report that scores a CV against a
job description on ten weighted criteria, adjusted for how strict the employer is, plus bonus and screening points. Every judgement is backed by a
verbatim quote or a cited web source, and every number can be recomputed by hand.

Works with **Claude Code**, **Codex**, **Cursor**, or any agent that reads `AGENTS.md`.

## Read your score as a hypothesis, not a verdict

The score is a hypothesis about how this CV will land with this employer. It is not a decision
about whether to apply.

- **A low score is not a reason to hold the CV back.** Send it anyway, and learn from what
  happens. A rejection, a silence or an unexpected call back tests the hypothesis and tells you
  which criteria matter to this employer.
- **A high score is not a guarantee.** It does not promise a call back, an interview or even
  feedback. Send the CV, and treat the outcome as evidence in exactly the same way.
- **A strong referral to the hiring manager overrides the score completely.** When someone
  the hiring manager trusts puts your name forward, the conversation no longer depends on how a
  screener reads your CV, so no assessed score should stop you.

Use the report to improve the CV before you send it, and to understand the outcome afterwards.
Every application is an experiment: record the score, send the CV, and compare the result with
the report.

## What it scores

| # | Criterion | Default weight |
|---|---|---|
| 1 | Current or most recent role: title match, CV headline match (small weight), same industry, competitor, supplier | 50% |
| 2 | Employer strictness from the hiring company's BCG growth-share quadrant plus hiring buzz. A modifier, not weighted: it sets the exponent that turns the core score into the adjusted score | modifier |
| 3 | Years with the tools, services and suppliers the JD names | 6.25% |
| 4 | AI experience, and the outcomes it delivered | 6.25% |
| 5 | Likelihood that the CV is AI-generated (penalty or bonus, your choice) | 6.25% |
| 6 | Spelling (minus points per error) | 6.25% |
| 7 | Grammar (minus points per error) | 6.25% |
| 8 | STAR or Problem-Action-Result shape, checked on every achievement and experience bullet | 6.25% |
| 10 | Qualifications and certifications against the JD | 6.25% |
| 11 | The candidate's own access: previously worked there, referrals by route and seniority | bonus points |
| 12 | Tenure: median length of completed roles against the employer's expected longevity | 6.25% |
| 14 | Screening signals: time at the target seniority, employment gaps, short recent roles, contract history, phrases copied from the JD, sector recency, conventional certifications and conflicts of interest (clamped between -15 and +10). The report also flags date ranges an applicant tracking system may not read, a blank education field and application form questions | plus and minus points |
| 15 | Prior elite indicators: state honours, founder exits, top university, elite scholarships and employers, top consultancies, fellowships, bestselling authorship, national awards and similar, each needing a public source and discounted when unrelated to the role. Capped at +15 | bonus points |

The report also has an unscored "Package and location" section: the posted package, an estimated pay band with cited comparables, the London premium, the work pattern and the employer's location strategy. It never changes the score.

Elite indicators measure how pedigree is likely to be perceived, not merit, so the section is capped and every weight can be changed.

`total = clamp(adjusted + c11 + c14 + c15, 0, 100)`, where `core` is the weighted mean of criteria 1, 3 to 8, 10 and 12, `adjusted = 100 * (core/100)^gamma` and gamma comes from c2.
The full logic, with every formula, is in [docs/rubric.md](docs/rubric.md). Change any weight in
`config/weights.local.json`, or live with the sliders in the HTML report.

## How it stays honest

- **The AI judges, the script counts.** The agent writes evidence and judgements to
  `assessment.json`. `bin/score.js` computes every score, so the AI never asserts a number.
- **Quotes are verified.** Every CV or JD quote is checked against the extracted text, and one
  that is not there is flagged in red on the report.
- **Sources are cited.** Industry, competitor, supplier, elite-indicator and BCG
  judgements need web sources, each with the URL, the date accessed and the supporting sentence.
- **No evidence means no points**, marked low confidence. It is never a guess.

## Quick start

```bash
git clone https://github.com/robbrown-net/cv.fit-report.git
cd cv.fit-report
npm install
```

Open the folder in your agent and say:

> Assess my CV against this job. *(attach or paste both)*

The agent follows [AGENTS.md](AGENTS.md): intake, a couple of questions about referrals,
web research, evidence, scoring, then `report.html` and `report.pdf` in `reports/<folder>/`.

Requirements: Node.js 18 or later, and an agent with web search, which industry and market
judgements need.

## Example

[examples/acme-head-of-operations/](examples/acme-head-of-operations/) is a complete fictional
run: CV, JD, evidence file and the finished report.

```bash
node bin/score.js examples/acme-head-of-operations
node bin/report.js examples/acme-head-of-operations
```

## Privacy

CVs are personal data. `reports/` and `inputs/` are gitignored, and the agent is instructed never
to send a CV to a third-party service without permission.

## Caveats

- AI-detection (criterion 5) is unreliable everywhere. It is shown as an indication, never as proof.
- Criteria 1, 2, 9 and 13 depend on public information that may be dated or incomplete. Check the
  sources the report cites.
- This is a self-assessment aid, not a hiring decision tool.

## License

MIT
