# Auto-rebase

A GitHub Action that automatically rebases pull requests.

## Usage

On first run, AutoRebase will create an `autorebase:opt-in` label on your repository.
Apply this label to pull requests you want to automatically be rebased on their target branch when out-of-date.
AutoRebase will then try to rebase these pull requests automatically.

If a pull request cannot be rebased because of merge conflicts, AutoRebase will apply a label `autorebase:non-rebaseable` label to the PR.
This label will automatically be removed if conflicts are resolved.

## Installation

-   Create a Github App token or a personal access token, granting write access on your repository.
-   Head over to https://github.com/{owner}/{repo}/settings/secrets and create a new secret `AUTOREBASE_TOKEN`, and paste the token.
-   Create a `.github/workflows/autorebase.yml` file in your repository and paste the following contents:

```yaml
on:
    # Run on every push on every branch
    push:
        branches-ignore:
            # Ignore branches automatically created by github-rebase
            - rebase-pull-request**
            - cherry-pick-rebase-pull-request**
    # Run when pull requests get labeled
    pull_request:
        types: [labeled]

jobs:
    auto-rebase:
        name: AutoRebase
        runs-on: ubuntu-latest
        steps:
            - uses: Label305/AutoRebase@v0.1
              with:
                  # We can't use the built-in secrets.GITHUB_TOKEN yet because of this limitation:
                  # https://github.community/t5/GitHub-Actions/Triggering-a-new-workflow-from-another-workflow/td-p/31676
                  # In the meantime, use a token granting write access on the repo:
                  # - a GitHub App token
                  #   See https://github.com/marketplace/actions/github-app-token.
                  # - a personal access token
                  #   See https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line.
                  github_token: ${{ secrets.AUTOREBASE_TOKEN }}
```

## Modification after a PR has been rebased

AutoRebase will 'rewrite history' when rebasing pull requests:
commits will be rewritten on top of the target branch and force pushed.

If you want to make changes to your pull request, you need to force push as well.
We recommend the following strategy when doing this:

-   Run `git fetch`
-   Ensure there are no other changes made remotely to your pull request branch
-   Run `git push --force-with-lease` to force push your changes.

## Attribution

AutoRebase is heavily inspired by [tibdex/autosquash](https://github.com/tibdex/autosquash) and [tibdex/autorebase](https://github.com/tibdex/autosquash).

# StoreFeeder deployment

To deploy run the following making sure to increase to version number.

`npm run build`

`git tag -a v1.0.0 -m "My first action release"`

`git push --follow-tags`
