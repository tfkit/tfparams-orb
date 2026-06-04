# Maintainer setup (CircleCI orb)

One-time manual steps to publish this orb. These require a CircleCI account and
browser authorization, so they can't be automated.

1. **Connect this repo to CircleCI** — at [app.circleci.com](https://app.circleci.com/),
   set up the project and authorize the `tfkit` organization.
2. **Create a CircleCI personal API token** — User Settings → Personal API Tokens.
3. **Add a context** named `orb-publishing` — Organization Settings → Contexts —
   with an environment variable **`CIRCLE_TOKEN`** set to that token.

That's all. After this:

- The **namespace** (`tfkit`) and the **orb** (`tfkit/tfparams`) are created
  automatically and idempotently by the publish job (`.circleci/config.yml`).
- Pushing a **`vX.Y.Z` tag** packs, validates, and **publishes** the orb at that
  version. Every push/tag also runs the `validate` job.

> Note: claiming the namespace `tfkit` is one-time and effectively permanent.

Dependency updates for this repo are handled by the self-hosted **Renovate**
runner in [`tfkit/tfparams`](https://github.com/tfkit/tfparams) (see that
repo's `SETUP.md`); no setup is needed here.
