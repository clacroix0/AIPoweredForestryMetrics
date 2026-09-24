# Public-release checklist

Complete this checklist before the first public push and before each material update.

## Authorization

- [ ] Publication has been approved by the appropriate owner or organization.
- [ ] The selected GitHub organization and account are approved for official use.
- [ ] Branding, communications, records, privacy, accessibility, and legal requirements have been considered.
- [ ] The licensing status in `LICENSE_STATUS.md` is acceptable for release.

## Repository contents

- [ ] No managed or unmanaged Power Platform solution is present.
- [ ] No `.zip`, `.msapp`, `.pbix`, `.pbit`, solution XML, flow definition, or deployment package is present.
- [ ] No operational forestry PDF or extracted source text is present.
- [ ] No real record, name, email address, internal URL, or screenshot is present.
- [ ] No tenant, environment, workspace, report, site, list, connection, or other internal identifier is present.
- [ ] No credentials, tokens, keys, signed links, or secrets are present.
- [ ] All sample filenames and values are clearly synthetic.

## Quality and accessibility

- [ ] `README.md` renders correctly on GitHub.
- [ ] `docs/index.html` opens locally without external dependencies.
- [ ] Keyboard navigation reaches every interactive element.
- [ ] Text has sufficient contrast and remains readable at 200 percent zoom.
- [ ] The page works at desktop and mobile widths.
- [ ] Images have useful alternative text or are marked decorative.
- [ ] The human-review requirement is prominent.

## Git and repository settings

- [ ] The public repository was created from this clean package, not from internal Git history.
- [ ] Branch protection is enabled.
- [ ] Secret scanning and push protection are enabled when available.
- [ ] Private vulnerability reporting is enabled.
- [ ] GitHub Pages is configured from `main` and `/docs` only after content approval.
