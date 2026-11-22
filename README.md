<div align="center">

# ACLDrift

<h3>Permission drift detection for RAG indexes</h3>

[![Status](https://img.shields.io/badge/status-design%20stage-orange?style=flat-square)](ROADMAP.md)
[![Go 1.26+](https://img.shields.io/badge/go-1.26+-00ADD8?style=flat-square)](https://go.dev/dl/)
[![License](https://img.shields.io/badge/license-Apache%202.0-blue?style=flat-square)](LICENSE)

---

[How it works](#how-it-works) · [Thesis](THESIS.md) · [Roadmap](ROADMAP.md) · [Contributing](CONTRIBUTING.md) · [Security](SECURITY.md)

</div>

---

## What is ACLDrift?

Checks that your vector index still respects the permissions of the documents
it came from. `acldrift` compares every indexed chunk with the access list of
its source document (in Google Drive, Confluence, SharePoint, GitHub and
similar systems) and reports chunks that more people can retrieve than could
ever read the original.

> **Status: design stage.** There is no code yet. The commands and output
> below are the proposal, and they will change. Part of the
> [OpenAstra](https://openastra.org) research agenda, area IX: AI Data
> Security & Privacy.

---

## The problem

To build an enterprise RAG system, teams copy documents out of systems with
fine-grained permissions and into a vector store that often has none, or has
a simplified copy that goes stale. A document that only HR could open becomes
a chunk anyone in the company can retrieve. Permissions also change after
ingestion: someone is removed from a folder, but the index still lets them
retrieve its contents. This may be the most common data leak in enterprise
RAG today, and it's invisible without comparing the two sides.

---

## How it works

1. **Read the index.** For each chunk, read its source reference and whatever
   access metadata the index holds.
2. **Read the source.** Fetch the current access list of the source document
   through the source system's API.
3. **Compare.** Flag chunks where the index allows more principals than the
   source does, chunks whose source was deleted, and chunks with no source
   reference at all.
4. **Report and fix.** Group findings by cause (an ingestion job that drops
   ACLs, stale sync, orphaned chunks) and optionally update or remove the
   affected chunks.

---

## Example (proposed)

```console
$ acldrift scan --index kb-prod --source confluence
scanned 182,440 chunks from 9,312 source pages

OVERSHARED   4,108 chunks   retrievable by: all-employees
             source allows: hr-team (12 people)
             cause: ingestion job `confluence-sync` drops page restrictions
STALE          611 chunks   source permissions changed after last sync (avg 41 days)
ORPHANED        93 chunks   source page deleted

Run `acldrift fix --dry-run` to see proposed changes.
```

---

## Planned surfaces

- Index connectors: pgvector, Elasticsearch/OpenSearch, Qdrant, Pinecone
- Source connectors: Confluence, Google Drive, SharePoint, GitHub
- CLI and scheduled job; JSON and SARIF output
- `fix` with dry-run: rewrite chunk ACLs or remove chunks

---

## Maps to

| Framework | Items |
|-----------|-------|
| OWASP LLM Top 10 (2025) | LLM02 Sensitive Information Disclosure, LLM08 Vector and Embedding Weaknesses |
| NIST SP 800-53 Rev. 5 | AC-3 Access Enforcement, AC-6 Least Privilege, AC-2 Account Management |

---

## Design principles

- **The source system is the truth.** The index may be stricter, never looser.
- **Read-only by default.** `fix` always has a dry run.
- **Least-privilege connectors.** Each connector documents the minimum API scopes it needs.

---

## Roadmap

The plan, from the first working version to 1.0, is in [ROADMAP.md](ROADMAP.md). The reasoning behind the project, and what would make us stop, is in [THESIS.md](THESIS.md).

---

## Related OpenAstra projects

- [Tripwire](https://github.com/sadayamuthu/tripwire): detects leaks that happen anyway
- [OCBC](https://github.com/sadayamuthu/ocbc): the access controls each finding maps to
- AgentID (in the lab): retrieval on behalf of a specific user

---

## Contributing

Design feedback, real-world cases and fixes are welcome. See [CONTRIBUTING.md](CONTRIBUTING.md) for how to help while the project is at the design stage, and [GOVERNANCE.md](GOVERNANCE.md) for how decisions are made.

---

## Security

Please report vulnerabilities privately through [GitHub security advisories](https://github.com/sadayamuthu/acldrift/security/advisories/new), not in a public issue. See [SECURITY.md](SECURITY.md) for what to include and what's in scope.

---

## License

[Apache License 2.0](LICENSE)

---

<div align="center">

**ACLDrift: permission drift detection for RAG indexes**

ACLDrift is managed by [OpenAstra](https://openastra.org)

[GitHub](https://github.com/sadayamuthu/acldrift) · [Thesis](THESIS.md) · [Roadmap](ROADMAP.md)

</div>
