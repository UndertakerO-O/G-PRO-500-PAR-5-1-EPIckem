# CI and Epitech synchronization

The public repository runs CI on every pull request and push to `main`. It
checks the Vue frontend (formatting, unit tests, TypeScript and production
build) and the backend (lint, tests, build and Prisma schema validation).

After CI succeeds on `main`, `Sync validated code to Epitech` pushes that
validated commit to the private school repository.

## Required GitHub secret

In the public repository, add an Actions secret named `EPITECH_PUSH_TOKEN`.
Use a fine-grained personal access token with access only to
`EpitechPGE3-2026/G-PRO-500-PAR-5-1-professionalwork-15` and **Contents: Read and
write** permission. The token owner must have write access to the Epitech
repository. Never commit the token.

Keep `main` protected on the public repository and require the `Frontend` and
`Backend and Prisma` checks before merging pull requests.

The sync push requires the Epitech `main` branch to be a fast-forward of the
validated commit. If the branches diverge, the job stops instead of rewriting
the school repository history; resolve the divergence with a reviewed change.
