# Publish this showcase to GitHub

Use an organization and account approved for the project. Do not upload the original managed solution ZIP or any internal package to the public repository.

## 1. Review the package

Complete [`PUBLIC_RELEASE_CHECKLIST.md`](PUBLIC_RELEASE_CHECKLIST.md). Open `docs/index.html` locally and confirm that all visible content is appropriate for public release.

## 2. Create the repository

A descriptive repository name is:

```text
ai-powered-forestry-metrics-public
```

Create an empty repository. Do not add a separate README, `.gitignore`, or license during repository creation because those files are already addressed here.

## 3. Upload with Git

From inside this folder, run:

```bash
git init -b main
git add .
git commit -m "Create public project showcase"
git remote add origin https://github.com/ORG/REPOSITORY.git
git push -u origin main
```

Replace `ORG` and `REPOSITORY` with the approved GitHub organization and repository name.

## 4. Turn on GitHub Pages

In the repository:

1. Open **Settings**.
2. Select **Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**.
4. Select branch **main** and folder **/docs**.
5. Save the setting.

GitHub will display the public Pages address after deployment.

## 5. Recommended repository settings

- Protect the `main` branch and require pull-request review.
- Enable secret scanning and push protection when available.
- Enable private vulnerability reporting.
- Disable wiki and discussions unless there is a plan to moderate them.
- Keep issue forms limited to public-safe documentation requests.
- Add a short description such as: “Public showcase of a human-reviewed Power Platform workflow for forestry metric extraction and reporting comparison.”
- Consider topics such as `forestry`, `power-platform`, `power-apps`, `power-bi`, `responsible-ai`, and `document-processing`.

## 6. Before changing visibility to public

Review the entire Git history, not only the current files. A deleted secret or internal file can remain in earlier commits. Create the public repository from this clean package rather than from a clone of an internal repository.
