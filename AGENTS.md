# Repository Instructions

## Project

- This repository is the source for `https://zrongqing.github.io/`, built with Hexo 7 and the Butterfly theme.
- Treat `source/`, `scaffolds/`, `_config.yml`, `_config.butterfly.yml`, and `package*.json` as source of truth.
- Do not manually edit `public/` or tracked legacy generated HTML/CSS/JS to change blog content.

## Blog work

- Use the repository skill at `.agents/skills/maintain-hexo-blog/SKILL.md` for article writing, Front Matter, images, previews, builds, troubleshooting, and publishing.
- Preserve unrelated user changes and untracked files. Inspect `git status --short --branch` before and after work.
- Write technical posts primarily in Chinese unless the user requests another language. Do not invent commands, outputs, versions, citations, or verification results.
- Preserve a published post's non-empty `abbrlink` and original `date`. Update `updated` when materially revising an existing post.
- Do not commit secrets, `node_modules/`, `public/`, `db.json`, `.deploy*/`, or logs.

## Verification

- Use project-local commands through `npm run ...` or `npx hexo ...`; do not require a global Hexo installation.
- For content or configuration changes, run `npm run clean` followed by `npm run build`.
- Recheck the diff after building because the abbrlink plugin can write missing values into article Front Matter.

## Publishing

- The default release path is `main` -> GitHub Actions -> `gh-pages` -> GitHub Pages.
- Do not run `git push`, `npm run deploy`, or any other remote-changing command unless the user explicitly requests publishing.
- Never read, print, or commit the SSH private key behind the GitHub Actions secret `SSH_PRI`.
- This repository has no NuGet package. Do not run NuGet publishing commands without a separate, explicit package specification.
