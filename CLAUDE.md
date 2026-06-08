# IT Audit: bWAPP Lab

## Purpose

College IT Audit coursework. Web application penetration test against bWAPP
(Buggy Web Application, by Malik Mesellem), a deliberately vulnerable PHP app.
Findings are mapped to OWASP categories and compiled into a formal audit report
(laporan audit). Author: Ravi Arnan Irianto.

## Lab Setup

- bWAPP runs in Docker, image `hackersploit/bwapp-docker`, published on
  `http://localhost:8080`.
- This image serves bWAPP at the web root, not under `/bWAPP/`. Pages are at
  paths like `http://localhost:8080/login.php` and `.../sqli_1.php`.
- Run `http://localhost:8080/install.php` once to initialize the MySQL DB.
- Web login credentials: `bee` / `bug` (default bWAPP lab creds, intentionally public).
- Difficulty is controlled by the `security_level` cookie (0 = Low).
- Backend stack observed: PHP 5.5.9, Apache 2.4.7, MySQL >= 5.1, Ubuntu Linux.

## Structure

- `it_audit.md`: research/setup transcript and per-slide presentation notes
  (topic selection, Docker setup, troubleshooting).
- `doc/`: all evidence and the final report.
  - `laporan_audit_akhir.{html,docx,pdf}`: the final audit report (HTML is source of truth).
  - Per-tool scan evidence, each as `.html` plus rendered `.pdf`:
    `nikto_scan`, `sqlmap_scan`, `xss_scan`, `zap_analysis`,
    and the raw ZAP export `2026-05-24-ZAP-Report-.pdf`.
  - `cookies.txt`: Netscape-format session cookie used by scanners.
  - `sqlmap_output/`: raw sqlmap run. `localhost/target.txt` records the exact
    command; `localhost/dump/bWAPP/users.csv` is the dumped users table.

## Tools Used

Nikto (server scan), sqlmap (SQLi on `sqli_1.php` param `title`, dumped
`bWAPP.users`), manual/automated XSS, and OWASP ZAP (full report + analysis).

## Report Pipeline

Reports are authored in HTML, then converted:

- HTML to DOCX via headless LibreOffice:
  `libreoffice --headless --convert-to docx --outdir . laporan_audit_akhir.html`
  (use `--infilter='HTML (StarWriter)'` if the default importer misbehaves).
- Scan `.html` files are rendered to `.pdf` as evidence attachments.
- `pandoc` and `pdfinfo` are available for inspection/conversion checks.

## Conventions and Gotchas

- This is an intentionally vulnerable app. Keep it bound to `localhost` only;
  never expose the port or run scans against real targets.
- The sqlmap dump contains bWAPP's seeded fake users (e.g. `bee`), not real
  secrets. Do not treat dumped hashes/emails as live credentials.
- Style: no emojis, no em dashes. Use periods, commas, or parentheses.
- Do not auto-commit. User runs/reviews locally first.
