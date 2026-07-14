---
name: work-helper
description: Generate Git commit messages and Kanban work reports tailored to the user's preferred styles.
---

# WORK HELPER SKILL

This skill helps generate Git commit messages and Kanban board work reports exactly matching the user's preferred styles.

## Git Commit Message Style
When generating a commit message, follow these rules:
- **Language**: English.
- **Format**: Conventional Commits.
- **Header**: Use a type (e.g., `feat`, `fix`, `refactor`) and optional scope, followed by a concise summary.
- **Body**: Use a bulleted list starting with `-` containing general, high-level summaries of the changes rather than technical micro-details.

### Git Commit Example:
```git
feat(ap-report): implement new Accounts Payable report with Excel export

- Create a new matching report connecting A/P payments, vouchers, POs, and GRNs.
- Add an Excel export utility with proper data formatting and dynamic company headers.
- Support AJAX-based dynamic table reloading and debounced A/P number search.
- Implement search filters with a maximum 1-month date range restriction for performance.
```

---

## Kanban Work Report Style
When generating a Kanban board work report, follow these rules:
- **Language**: Indonesian.
- **Tone**: Natural but formal (natural tapi tetap formal). Avoid slang like "bikin", "gak", "nyocokin". Use standard, professional language.
- **Structure**: Group by columns (e.g., "Done", "In Progress", "To Do") with clear Task and Description fields.
- **Constraint**: The Description MUST be exactly one sentence (satu kalimat saja).

### Kanban Report Example:
```markdown
### 🟢 DONE
- **Task**: Pembuatan dan Penyempurnaan Fitur Laporan A/P
  **Description**: Mengembangkan modul laporan pencocokan transaksi A/P (Voucher, PO, GRN, Payment) dengan dukungan ekspor Excel dan restrukturisasi menu sidebar.
```
