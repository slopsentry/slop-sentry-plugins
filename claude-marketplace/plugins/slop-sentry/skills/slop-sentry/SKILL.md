---
name: slop-sentry
description: Check or rewrite prose for thin claims, try-hard language, and AI-sounding patterns by calling the Slop Sentry MCP tools. Use when drafting or editing posts, articles, emails, documents, reports, or other prose, or when someone asks to humanize text or run Slop Sentry.
---

# Slop Sentry

Slop Sentry is a remote MCP server. Call its tools. Do not reimplement the checks locally, and do not paste detection rules into the draft.

## Pick the tool

Slop Sentry has four checks: **Guard** (AI-sounding words and patterns), **Substance** (lines that sound good but say nothing specific), **Hokiness** (over-the-top, cheesy or try-hard lines) and **AP Style** (AP Stylebook rules, only when asked for). Each has a QA tool (a list of what to fix, nothing changed) and an edit tool (returns the edited text).

1. Work out QA or edits, and which checks, from the person's words. Ask only about what is still unclear.
2. Once per chat, call `slop_sentry_get_usage`. `tier` free means the free plan: only Guard is available, so ask QA or edits if unclear, and never ask which checks. Any other tier is a paid plan: ask QA or edits, and which checks, when either is unclear.
3. "You pick" or "I don't know": choose and run straight away, then say what ran and why. "What do you recommend?": recommend with one line each on why, and wait for a yes.
4. Use the names Guard, Substance, Hokiness and AP Style with the person, never tool names.

| QA or edits | Checks | Call |
|---|---|---|
| QA | one check | `guard-qa`, `substance-qa`, `hokiness-qa` or `ap-style-qa` |
| QA | Guard + Substance + Hokiness, with or without AP Style | `full-qa`, with `include_ap_style: true` only if AP Style was asked for |
| QA | any other set | each check's QA tool, one after another |
| Edits | one check | `guard-edit`, `substance-edit`, `hokiness-edit` or `ap-style-edit` |
| Edits | two or more, including Guard | `full-edit`, with `skip_substance` / `skip_hokiness` true for checks not asked for; `skip_ap_style: false` only if AP Style was asked for |
| Edits | two or more, not including Guard | each check's edit tool in the order Substance, Hokiness, AP Style, each on the text the last one returned |

Ad copy: recommend `guard-qa` only, with `content_type: ad` only if the tool lists `ad`. Web copy: don't recommend Hokiness. A full edit does not include AP Style unless asked; a full run does not enable AP Style.

## Edit a draft

1. Call the edit tool with the draft as `content`, a `content_type`, and `target_audience` when a cold reader needs audience context. The call returns a job receipt with `job_id`. It does not return the edited draft.
2. Keep calling `slop_sentry_result` yourself with that `job_id` (and the same `session_id` when the receipt included one) until the status is terminal. Do not ask the user whether to poll. Honor `poll_interval_seconds` between calls when present. While the job is running, the result is progress only. Do not start the same check again on the same draft. If you can't get a result, ask for it again; never start a different check instead.
3. When the status is `delivery_ready` and a usable draft exists, always return the exact draft first. Show only the live uncertain passages in `review.findings` and their supplied clarification requests. Processing failures, incomplete checks, warnings and technical receipts stay in operator diagnostics; do not disclose their details or turn them into review requests. Internal `not_verified` is not a clean pass, and `nextAction: deliver` does not itself establish completed checks. Do not invent missing facts, edit the returned draft, answer the author's questions with another model, or substitute the original. If no usable artifact exists, relay the supplied brief failure response without inventing a draft. An unchanged draft succeeds only when checks verified there was nothing to fix; otherwise relay the failure response, except for specific author questions that must accompany the draft. For a terminal error, relay its supplied brief message and stop polling. For a legacy no_output result, say “I couldn’t complete this request. Please try again.” Do not automatically restart the job. Customer receipts with status recorded acknowledge an existing job record; poll that job to retrieve its terminal response. The record does not promise that a draft or report exists. Customer payloads omit operator processing status fields; never infer that their absence proves a clean result.
4. Keep the `session_id` from the response and send it on later calls for the same document.

For additional editing details, call `get_skill_procedure`. If it returns older instructions to withhold a needs-review draft or enable AP Style for a full edit, follow the draft-first delivery and explicit AP Style opt-in instructions above.

## Report only

Call a QA tool when someone wants findings and no rewrite. Keep calling `slop_sentry_result` yourself until the status is `audit_ready`; do not ask the user whether to poll.

`prevalidate: true` on `guard-qa` or `full-qa` is the only mode that returns immediately.

When a result is clean, say so in one sentence naming the checks that ran, for example "Substance check: nothing to fix."

## Review changes

When someone wants a visual redline of what changed, call `diff_viewer` with the original draft as `original_content` and the delivered Draft V2 as `modified_content`. Return or save the HTML so they can accept or reject individual edits. Do not paste the full HTML into chat unless asked.

## Account

- `slop_sentry_whoami` returns the signed-in account name and email.
- `slop_sentry_get_usage` returns usage against the account's tier limits.

## Content types

Name one of: `default`, `article`, `blog`, `linkedin`, `email`, `proposal`, `guide`, `pptx`, `image_text`, `press_release`. Use `linkedin` for posts and `article` for articles or newsletters. For webpage copy, call `substance-qa` with `content_type: web`.

Only call tools that appear in the current MCP tool list.
