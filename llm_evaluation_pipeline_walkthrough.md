# How This LLM Evaluation Pipeline Works (Plain English Walkthrough)

This document explains, in simple words, what happens when this project runs. No technical background needed.

## What is this project, really?

Imagine you built a chatbot that answers IT support questions ("how do I reset my password," "my Wi-Fi isn't working," etc.). Before you let real people use it, you'd want to check two things: does it actually give good answers, and does it refuse to help with bad requests (like "how do I hack my coworker's email")?

This project is an automatic checker that asks the chatbot 32 different questions, reads its answers, and grades whether each answer was good, safe, and correctly formatted. It does this every time someone changes the code, so nobody accidentally makes the chatbot worse without noticing.

## What you need before you can run it (Prerequisites)

Think of these as the ingredients you need before you can cook the recipe:

1. **An OpenAI account and API key.** This is like a password that lets the project "phone" the AI model (GPT-4o-mini) and ask it questions. Without this key, nothing can talk to the AI at all.
2. **Node.js installed.** This is a piece of software that lets your computer run the grading tool (called Promptfoo). Think of it as the engine the grading tool runs on.
3. **Python installed.** A second piece of software needed to install some helper packages the project uses.
4. **The project's code downloaded onto your computer** (or available in GitHub, if you're using automatic checks).
5. *(Optional)* **A Langfuse account.** Only needed if you want a fancier dashboard showing every single AI call in detail. The project works fine without it — explained more below.

That's it. No deep programming knowledge is required to run the checks themselves.

## The big picture, in one paragraph

Every time someone updates the project's code and pushes it to GitHub, a robot automatically wakes up, reads a list of 32 test questions, sends each one to the AI chatbot, checks whether the chatbot's answer passes a set of rules, and then produces a report card showing what passed and what failed. You never have to remember to run this by hand — it just happens.

## Step-by-step walkthrough

### Step 1 — Something triggers the check

Whenever code is pushed to the main version of the project (or someone proposes a change), GitHub automatically starts the checking process. You don't click a button — it just happens in the background. This automatic "trigger and run" behavior is called **CI/CD**, explained in its own section below.

### Step 2 — The list of test questions is loaded ("load test config")

The project reads one file that contains everything it needs to know:

- The instructions given to the chatbot (e.g., "you are an IT support assistant, only answer IT questions, refuse anything harmful, and always reply in a specific structured format").
- Which AI model to use (GPT-4o-mini) and how creative it's allowed to be (set to the lowest creativity setting, so it gives the same kind of answer every time instead of being random).
- The actual list of 32 test questions, split into three groups:
  - **12 "does it work" questions** — normal IT questions like "why won't Outlook open," to check the chatbot gives a sensible, on-topic answer.
  - **10 "is it safe" questions** — tricky or harmful questions like "how do I bypass my admin restrictions," to check the chatbot correctly refuses.
  - **10 "is it formatted right" questions** — checks that the chatbot's reply always comes back in the expected structure, not random text.

Loading this file is like a teacher pulling out the answer sheet and question list before grading a test. Nothing has been asked yet — this step just gets everything ready.

### Step 3 — Each question is actually asked to the AI

Now the project goes through the 32 questions one at a time, sends each one to the AI model, and waits for an answer. Each answer comes back as a small structured note with three pieces of information: what category the question falls into, the actual answer text, and whether the request was judged safe, unsafe, or worth a second look.

### Step 4 — Each answer is checked against the rules

For every answer, the project runs a few automatic checks, such as:

- Is the answer actually in the expected structured format (not just plain free text)?
- Does it contain exactly the right pieces of information, nothing missing and nothing extra?
- For the "is it safe" questions — did the chatbot actually refuse, instead of explaining how to do the harmful thing?
- For the "does it work" questions — is the answer genuinely relevant and helpful? (This particular check is itself graded by a second AI call, acting like a teacher marking an essay.)

### Step 5 — Each test is marked pass or fail

If everything about a question's answer checks out, that test is marked as passed. If even one rule is broken — wrong format, unsafe content slipped through, irrelevant answer — that test is marked as failed, and the project keeps a record of exactly what went wrong so a person can look into it later.

### Step 6 — A report card is produced

Once all 32 questions have been asked and graded, the project adds everything up: how many passed and failed in each of the three groups (works correctly / stays safe / formatted right). This can be viewed as a simple summary in the automated check's log, or opened as a visual dashboard in a web browser for a closer look at every single question and answer side by side.

## What is "CI/CD," really?

CI/CD stands for "Continuous Integration / Continuous Delivery," but forget the long name — here's the simple idea:

Imagine every time you changed a recipe, a robot automatically cooked the dish and tasted it for you, immediately telling you if it tastes good or bad — without you having to remember to do it yourself. That's CI/CD. It means: **whenever code changes, automatically run the checks, every single time, with no human needing to remember to do it.**

In this project, that "robot" lives inside GitHub (a tool called GitHub Actions). It watches for any update to the code, and the moment one happens, it automatically sets up everything it needs and runs all 32 test questions. This matters because it's very easy for a person to forget to test their changes manually, but a robot never forgets.

## What is Langfuse, and why is it mentioned here?

Langfuse is an optional tool for watching, in much more detail, exactly what happened during every single AI call — what question was sent, what answer came back, how long it took, and how much it cost.

Think of the basic pass/fail report from this project as a school report card — it tells you the grades. Langfuse is more like a security camera recording every single moment of the exam — useful if you want to go back and watch exactly what happened on one particular question, rather than just seeing the final grade.

This project doesn't require Langfuse to work — it's entirely optional. The project simply leaves a "plug" ready (a few settings) so that if someone wants to connect Langfuse later for deeper inspection, they easily can, without changing any of the core checking logic.

## A quick recap, start to finish

1. A code change happens → 2. GitHub automatically wakes up the checker (CI/CD) → 3. The checker loads its list of 32 questions and instructions (load test config) → 4. Each question is asked to the AI → 5. Each answer is checked against the rules → 6. Each test is marked pass or fail → 7. A final report card is produced, with an optional deeper-detail view available through Langfuse.

That's the whole pipeline — nothing more mysterious than a recipe that gets automatically cooked and tasted every time the ingredients change.
