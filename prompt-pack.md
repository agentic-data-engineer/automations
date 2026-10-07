# The prompts

Every instruction Erum gave during the build, exactly as typed, typos included, from the first prompt to
sign-off. Letters and numbers ("A:", "D6", "R1") answer questions the AI asked first. Two long pastes are
summarized in brackets. One outside link is replaced by a note in brackets, at Erum's request.
Shared with permission.

## Requirements

**1.**

> Let's start the requirements phase for a new data product.
>
> The problem: after a hospital treats a patient, it bills the insurer. Sometimes the insurer denies the claim, and sometimes it pays less than the contract says. The proof is spread across four places: the claim, the payment record, the contract rates and the insurer's rules. So staff miss money they're owed.
>
> What I want: a workbench on Databricks that finds denied and underpaid claims, shows the evidence behind each one, and ranks them by dollars at risk so staff work the biggest first.
>
> Ground rules: a person always makes the final decision. The tool never submits claims, files appeals, collects money or makes medical decisions. All data is made up for this demo.
>
> Don't build anything yet. Tell me back what you understand, what's out of scope, and ask me anything that's unclear.

**2.**

> Here are my answers: 1. Any shortfall agasint the contract rate. 2. The full contract expected amount. 3. Fee schedule per procedure code only. 4. Label likely correct 5. Yes, roll up to claim. 6. appeal deadline 7. read-only 8. Dashboard 9. Drafting appeal is out of scope 10. Reasonable size synthetic data. 11. No limit, design to achieve the goal.

**3.**

> Here are my answers: A: Rank by dollars, largest first, and break ties by earliest deadline. Claims past their deadline sort to the bottom and are labeled "deadline passed." B: Stay in worklist below the other denials C: Patient's share should be accounted for D. Use synthetic codes

**4.**

> E & F, proceed with recommendation. G. Approved.

**5.**

> Yes proceed with acceptance criteria

**6.**

> For each test case, give me the input and the result we expect, written before any code exists, so the code can't grade its own homework. Include the tricky cases, not just the easy ones.

**7.**

> Yes proceed in converting to JSON. Also ensure following are covered: What about a duplicate payment that hides an underpayment? What about a claim that's short by exactly one dollar? What about a contract rate that changes partway through the year?

**8.**

> Here are my answers: For H: Go with line goes with review with the reason "payment record held".

## Design

**9.**

> Run the design phase.

**10.**

> Use [link to an outside article on Databricks naming conventions] for naming conventions and also create a document from this that the skill will reference next time. D1- use claims_recovery. D2, don't use workspace, I will create if refused. D3. one catalog D4 user account owns it. D5 Hold both copies and add new test case

**11.**

> D6. Use fact_. Also are you creating SDP pipelines?

**12.**

> Please create SDP

**13.**

> Here are my answers: Artifact List approved. Cost Limit of $50 approved. Modify Skill to use fact_ and add SDP as design preference.

**14.**

> Add below to this skill: [pasted: a Databricks architecture guide, "Declarative First": use Spark Declarative Pipelines by default and justify any exception. Third-party text, not reproduced.]

**15.**

> P1. Yes proceed.

**16.**

> Yes commit, then start development with bronze slice. I get an error when I ran the bash command.

**17.**

> Before we start with bronze layer and loader. Review the skill and recommend if anything is missing which would further standardize the design.

**18.**

> Review once again with best practices for databricks and then apply all gaps before proceeding with build

## Development

**19.**

> R1, split raw ingestion and transformation pipelines. R2 , proceed with recommendation. R3, Proceed. R4. Each viewers own permission. R5. Yes.

**20.**

> Catalog created, proceed with bronze slice

**21.**

> Commit and go with first option.

**22.**

> Proceed with silver

**23.**

> Commit and proceed with gold slice

**24.**

> Commit and proceed with audit views slice

**25.**

> Approve tiles, yes read both files, commit and proceed.

**26.**

> Commit and proceed with generator and scans

## Unit testing

**27.**

> Commit and proceed with unit testing phase

**28.**

> Add TC-32 and TC-33, commit, and rerun mutations

**29.**

> Explain exactly what this process is doing and what is the end goal

**30.**

> Codex review completed. Please review brief.

**31.**

> It is here:

**32.**

> [pasted: the Codex review findings, five rows with severity, what is wrong, an input that shows it, and a suggested fix. Shown in Episode 7.]

**33.**

> Approve R7 and R8, commit, and no need to run an additional focused review

## Dry run

**34.**

> Proceed with the dry run

**35.**

> All 18 signed off, commit and close the phase
