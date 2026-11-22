# Security Policy

## Reporting a vulnerability

Please don't report security problems in a public issue, pull request or
discussion. Use
[**Report a vulnerability**](https://github.com/sadayamuthu/acldrift/security/advisories/new)
on the repository's Security tab instead. Only the maintainers can see the
report.

Include what you can of:

- the ACLDrift version (`acldrift version`) or commit, your Go version and OS
- how ACLDrift was used, and its configuration with secrets removed
- steps to reproduce
- what happened and what you expected

You'll get a reply when the report has been read, and again when a fix is
ready. A fix ships as a patch release; the advisory names you unless you'd
rather it didn't.

## Supported versions

ACLDrift is at the design stage and has no releases yet. Once it does,
fixes land in the latest release only, until 1.0.

## What counts

ACLDrift reads a vector index and the access lists of source documents through their APIs, using credentials you supply, and writes reports. `fix` can update or remove chunks in the index.

ACLDrift is a security tool, so anything that breaks one of these promises
is a security issue:

- **Read-only by default.** Nothing is written to an index or a source system unless you run `fix` without `--dry-run`. A path that writes otherwise is in scope.
- **Least-privilege connectors.** Each connector documents the minimum API scopes it needs. A connector that requests or uses more is in scope.
- **Credentials stay local.** Source and index credentials are never written to reports, logs or output. A path that leaks them is in scope.
- **No document content in reports beyond what is needed.** Reports identify chunks and principals, not document text. A report that copies document content is in scope.

These are by design, and not vulnerabilities:

- A finding names the users or groups that can retrieve a chunk. Treat reports as sensitive.
- ACLDrift trusts the source system's API. If the source system reports permissions wrongly, so will ACLDrift.

A wrong result that isn't covered above is a bug, not a vulnerability. Report
it in a normal issue; see [CONTRIBUTING.md](CONTRIBUTING.md).
