# The Problem Nobody's Measuring

*February 24, 2026 · Ed Grzetich*

Your company tracks API latency, uptime, error rates, and developer satisfaction scores. But nobody's tracking what it costs AI tools to read your API documentation.

Open your observability dashboard. You can probably see API response times broken down by endpoint. Error rates by status code. Maybe even developer satisfaction from your last survey. If you're sophisticated, you're tracking time-to-first-hello-world for new integrations.

Now tell me: how many tokens does it cost an AI coding tool to read your API docs?

You don't know. Nobody does. There's no dashboard for this. No metric.

## The invisible transaction

Every time a developer points Cursor, Copilot, or Claude at your API documentation, a transaction happens. The AI tool reads your docs, converts them into tokens, and uses those tokens to generate integration code. The format of your documentation determines how many tokens that costs.

This isn't theoretical. If your docs are in a verbose format, the AI tool consumes more of its context window just reading the spec. That leaves less room for reasoning, for the developer's project context, and for generating quality code. In the worst case, the documentation fills so much of the context window that the model can't produce working code at all.

And it compounds. Every endpoint. Every developer accessing your docs. Every day.

## Why nobody's noticed

Documentation has traditionally been measured by human metrics: readability, completeness, accuracy, time-to-find-information. Those metrics still matter. But they're designed for a world where humans are the primary (or only) consumers of your docs.

That world is shifting. AI coding tools are increasingly the first reader of your API documentation. The developer asks the tool to integrate with your API, and the tool goes and reads your docs before generating code. The developer may never visit your docs site at all.

When the primary consumer changes, the metrics need to change too. But the tech writing community hasn't caught up. We're still optimizing for human readability while AI tools are silently consuming our docs in a completely different way, and paying a cost that nobody sees.

## The format question

Here's what makes this interesting: the same API information, documented in different formats, produces different token costs. Sometimes dramatically different. Two docs describing the exact same endpoints, the exact same parameters, the exact same constraints, but one could cost significantly more tokens than the other.

That raises an obvious question: does the format also affect the quality of the code the AI generates? Or just the cost?

And a harder question: if you tried to test this with a popular API like Stripe's, how would you know whether the AI is reading your docs or just drawing on patterns it already learned from training data? Every popular API is all over GitHub, Stack Overflow, and tutorial sites. You can't isolate the variable.

## What I'm doing about it

I've been researching this for the past year. I built two control APIs from scratch, APIs with no public footprint and no prior exposure in any training dataset. I documented each one in four different formats and ran over 21,000 integration tests across four AI models, from local models you can run on a laptop to frontier cloud APIs.

Clean data. No contamination question.

The results are coming in a book called *Tokens Not Jokin'*, out in March. But you don't have to wait for the book to start understanding the problem.

---

**See what your docs cost.** I built a free calculator that shows the token cost of your API documentation in real time. Paste your spec, see the numbers. It runs entirely in your browser.

[Try the Docs Cost Calculator →](https://doc-cost.vercel.app/)

[Pre-order the book on Leanpub →](https://leanpub.com/tokensnotjokin)
