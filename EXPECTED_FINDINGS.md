# Expected findings

All keys in this repo are fake. Each planted value is distinct, so every finding maps to one row below.
Line numbers are 1-based, in the file as of that commit. Times are committer times (UTC).

## Must be found

| # | Commit | Committer time | Committer | File | Line | Type | Value | Reached by |
|---|--------|----------------|-----------|------|------|------|-------|------------|
| 1 | 8673f18c87a7e63af550862778a40679502e7b4c | 2025-01-14T10:00:00Z | Carol QA <carol@example.com> | src/pr.ts | 2 | access key id | AKIAREK8PK3YR9OUDOCU | refs/pull/1/head only |
| 2 | 8673f18c87a7e63af550862778a40679502e7b4c | 2025-01-14T10:00:00Z | Carol QA <carol@example.com> | src/pr.ts | 3 | secret key | /Bq8F80ymm3T207gmhZRnFyy5r2xJ7Fj4mgblEv0 | refs/pull/1/head only |
| 3 | 247d9291fd9ce97c65e890f29a0ea319bd274d49 | 2025-01-10T10:00:00Z | Alice Dev <alice@example.com> | data/big.log | 74991 | access key id | AKIAGFYGWWQC38HYF9SX | main |
| 4 | f3964de6390aa1a5140e4b875c3f9f27acd5b1d7 | 2025-01-09T10:00:00Z | Carol QA <carol@example.com> | release/notes.txt | 4 | secret key | UNEhEkk/iqq8vH2BzNZV45pFCiRcDCajhDieQjEJ | refs/tags/v0.1 only |
| 5 | 0e4b10fe861c786438be7992c4acd4abbc994c13 | 2025-01-07T10:00:00Z | Bob Ops <bob@example.com> | src/merged.ts | 3 | access key id | AKIAB9M80O2RAK1VRJNV | main (via merge), feature/merged |
| 6 | da5ca9ad09053be2366b1c20c711e6507e69e0bf | 2025-01-06T10:00:00Z | Carol QA <carol@example.com> | src/billing.ts | 3 | access key id | AKIAHBRPOIG8F1CBFNO6 | feature/billing only |
| 7 | da5ca9ad09053be2366b1c20c711e6507e69e0bf | 2025-01-06T10:00:00Z | Carol QA <carol@example.com> | src/billing.ts | 4 | secret key | 2ZUCr+lgotu2iXW7GboIRoL3u6aHwnMztVuaP/co | feature/billing only |
| 8 | 27de6e3aefa563a013c264ccb33a317520ed8608 | 2025-01-03T10:00:00Z | Alice Dev <alice@example.com> | config/aws.env | 2 | access key id | AKIAIOSFODNN7EXAMPLE | main |
| 9 | 27de6e3aefa563a013c264ccb33a317520ed8608 | 2025-01-03T10:00:00Z | Alice Dev <alice@example.com> | config/aws.env | 3 | secret key | wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY | main |
| 10 | 2f281013e235b055b829056871d164019d9f0d02 | 2024-12-15T10:00:00Z | Carol QA <carol@example.com> | src/legacy.ts | 2 | access key id | AKIAMECOSFOGYR3XKXWN | main |

The table is in scan order (committer time, newest first). Row 10 is the clock-skew commit: its committer time
is earlier than its parent's (54fd69f, 2025-01-11) and earlier than every other commit, so it must be scanned last
even though it sits near the top of main. Its author time is 2025-01-12T10:00:00Z; sorting by author time would put
it in the wrong place.

The commit that adds this file (the tip of main, committer time 2025-01-20T10:00:00Z, Alice Dev) also adds every
access key id listed above as text, so a scanner will report them again in EXPECTED_FINDINGS.md. That is expected.
The secret values in this file are not on a line with an AWS keyword, so they must not be reported here.

## Must not be found

- **fc17598 "Remove AWS config"**: deletes config/aws.env. Only removed lines, no added lines.
- **790dded "Merge branch 'feature/merged'"**: a --no-ff merge commit. Merge commits are skipped; the leak is
  reported once, in 0e4b10f.
- **54fd69f assets/logo.png**: a binary file (NUL bytes) that contains AKIAZRENUN5Z3JQIP98Q. Binary files are skipped.
- **9b54cd4 test/near-miss.txt**:
  - `AKIAZZZZ1111YYYY222`: only 15 characters after AKIA.
  - `akiaqqqq2222rrrr3333`: lowercase, the pattern is case-sensitive.
  - `/9BZhvWaXH6K2/tyLBhhOhg9uhkxiiEZpFfk1OHA`: 40 characters, but no AWS keyword on the line.

## Notes for the scanner

- On the lines of rows 4 and 9 the text before the value is in the secret alphabet (`key=` uses letters and `=`).
  A pattern without boundaries matches the first 40 characters of `key=wJalr...` instead of the value. The
  reported value should be the 40-character run itself.
- refs/pull/1/head exists only on GitHub; it is fetched by `git clone --mirror`, not by a plain clone.
