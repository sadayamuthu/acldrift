# ACLDrift: thesis

**In one line:** the most common enterprise RAG leak is a vector index that
is looser than the documents it came from, and it's found by comparing the
two directly.

## The problem

RAG pipelines copy documents out of systems with fine-grained permissions
into vector stores that have none, or a simplified copy that goes stale. An
HR-only page becomes a chunk anyone can retrieve, and permission changes
after ingestion never reach the index.

## The bet

1. **The source system is the truth.** A mechanical comparison of each
   chunk's effective access with its source document's current ACL finds the
   leaks, with no heuristics and no model.
2. **Leaks cluster by cause.** Most overshared chunks come from a few
   ingestion jobs that drop restrictions or sync rarely, so grouping by cause
   turns thousands of findings into a few fixes.
3. **Read-only, least-privilege connectors** are what security teams will
   approve to run against production indexes and source systems.

## Why now

Enterprise RAG over Confluence, Drive, SharePoint and GitHub is now common,
built quickly by application teams, and rarely audited for permission
fidelity.

## What success looks like

- On real deployments, ACLDrift finds overshared chunks the owning team
  didn't know about and confirms them as real.
- Cause grouping reduces findings to a short list of ingestion fixes.
- Runs on a schedule against pgvector plus Confluence and Drive with
  documented minimum scopes.

## What would prove us wrong

- Indexes rarely keep a usable link back to source documents, so the
  comparison can't be made.
- Source ACL APIs are too slow, rate-limited or incomplete (inherited and
  group permissions) to compute effective access at scale.
- RAG platforms ship permission-aware retrieval that makes drift rare.

## Not a bet on

Detecting leaks after the fact (Tripwire) or poisoned content (HubScan).

## Where it fits

Area IX, AI Data Security & Privacy. Pairs with
[Tripwire](https://github.com/sadayamuthu/tripwire); findings map to
[OCBC](https://github.com/sadayamuthu/ocbc) controls.
