# aws-leak-fixture

> **This is a test repository. Every AWS key in it is fake and useless.**

This repo exists only as a scan target for
[aws-secret-scanner](https://github.com/maormagori/aws-secret-scanner). Its history was built on purpose to contain
AWS-looking credentials in different places (branches, a tag, a pull request, a large file, a binary file) so the
scanner's results can be checked.

- None of the keys belong to a real AWS account. They are made-up values in the right format, plus AWS's own
  documented example key. They cannot be used to access anything.
- There is nothing to report or rotate. Please don't open security reports for these values.
- Secret-scanning alerts on this repo are expected.

What the scanner should and should not find is listed in [`EXPECTED_FINDINGS.md`](EXPECTED_FINDINGS.md).
