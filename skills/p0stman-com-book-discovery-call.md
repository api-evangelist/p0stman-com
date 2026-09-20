---
name: Book a discovery call or send an enquiry
description: On the user's explicit instruction, request a free 30-minute discovery call with p0stman's founder or send a project enquiry, using the provider's two MCP write tools - after confirming, because neither call can be undone or de-duplicated.
api: mcp/p0stman-com-mcp.yml
endpoint: https://p0stman.com/api/mcp
operations: [get_services, book_discovery_call, submit_inquiry]
side_effects: write
auth: none
generated: 2026-09-19
method: generated
---

# Book a discovery call or send an enquiry

Both write tools send a real message to a real person at p0stman. There is no idempotency key, no dry-run
mode and no cancel or withdraw tool (see `conventions/p0stman-com-conventions.yml`), so apply the
provider's own Agent UX rule: **read freely, confirm before acting.**

## Steps

1. **Ground the ask.** Call `get_services` first so the enquiry names a real service and the user has seen
   the published "from" price and timeline before committing.
2. **Collect the required fields from the user, verbatim.** `book_discovery_call` requires `name`, `email`
   and `project_description` (optional `preferred_time`, free text such as a timezone or "weekday
   mornings UK time"). `submit_inquiry` requires `name`, `email` and `message` (optional `project_type`,
   e.g. "AI voice agent", "MVP", "Agentic web audit"). Never invent or guess an email address.
3. **Confirm.** Show the user exactly what will be sent and to whom (hello@p0stman.com is the published
   contact) and get an explicit yes.
4. **Send once.** POST one `tools/call` with the chosen tool and arguments. Do not retry on a timeout
   without checking with the user - a retry is a second enquiry.
5. **Report the outcome.** Relay the `content[0].text` the server returns. If the response is an
   `error` object, read `error.data` before deciding whether to resend.

## Choosing the tool

- The user wants a call scheduled: `book_discovery_call`.
- The user wants to describe a project or ask a detailed question by message: `submit_inquiry`.
- A2A clients can reach the same booking behaviour through the `book` skill of the "Zero" agent at
  `https://p0stman.com/api/agent` (`message/send`, text/plain).

## Reversal

None published. To cancel or change a booking after the fact, the only path is a human email to
hello@p0stman.com; to have personal data deleted, privacy@p0stman.com (UK GDPR rights stated at
https://p0stman.com/privacy).
