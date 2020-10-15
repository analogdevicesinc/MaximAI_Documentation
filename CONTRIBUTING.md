# Contributing Guide

Please read and understand the contribution guide before creating an issue or pull request.

Remember that these are **public** repositories and anyone can see all contributions. Refer to LICENSE.md for more details.

## Procedures and Access Rules

- We will primarily be using the **GitHub Flow** model of development.  Note that this is **NOT** "git flow". More information is available [here](https://guides.github.com/introduction/flow/) and [here](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/github-flow).
- Development is to be done via Forking into your personal account. More [information](https://help.github.com/en/github/getting-started-with-github/fork-a-repo).
  *Note: After creating the fork, you must re-enable actions in the “Actions” tab of the repository on GitHub.*
- Only admins and maintainer currently have write access to the root repositories. This is designed to help curate the contents of the project, via code reviews on all pull requests.

**Before submitting a pull request:**

- Check the codebase to ensure that your feature doesn't already exist.
- Check the pull requests to ensure that another person hasn't already submitted the feature or fix.
- Pull requests are checked by the [GitHub Super-Linter](https://github.com/github/super-linter).

### Technical Requirements

- Submit pull requests to the **develop branch only**, unless the root repository only has a master branch.
- Before submitting your pull request, merge `develop` with your new branch and fix any conflicts. (Make sure you don't break anything in develop!)
- Commit Unix line endings.

### Credentials Setup

All contributors should correctly setup their git and GitHub credentials before committing or issuing pull requests.

- This is normally done when [setting up](https://help.github.com/en/github/getting-started-with-github/set-up-git) local git client.

- Emails and Names should be set for both local git tools, and your linked GitHub account.  Maxim email addresses and real names are preferred. More information [here](https://help.github.com/en/github/setting-up-and-managing-your-github-user-account/setting-your-commit-email-address) and [here](https://help.github.com/en/github/setting-up-and-managing-your-github-user-account/managing-email-preferences).

- The repositories may be accessed with HTTPS or SSH.

## Example Workflow

- [Setup Git](https://help.github.com/en/github/getting-started-with-github/set-up-git).
- [Fork desired repos](https://help.github.com/en/github/getting-started-with-github/fork-a-repo).
- [Clone](https://help.github.com/en/github/creating-cloning-and-archiving-repositories/cloning-a-repository) the new fork locally for development.
- [Create a topic branch](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-branches) and check it out.
- Make changes, validate correctness.
- Merge upstream changes from the root repositories, revalidate (test).
- Consider squashing local commits into one.  This will make it easier for reviewers to understand and review the change.
- Push the new (squashed) branch to the forked repository.
- [Initiate a pull request](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request) back to the root repository.  Include descriptions of the change, why it is necessary, etc.  Note, if this fixes an issue, include it in the description.  We may add a pull request template in the future.
- [Request code review of the pull request](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/requesting-a-pull-request-review) from one of the maintainers.
- After approval the maintainer will typically [rebase merge](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-request-merges) the pull request into either the `develop` or `master` branches of the root repository.
- Typically after successful pull request merges the originating branch is deleted.

## NOTES
- Please check this document for changes periodically as new requirements may be deployed.

- Additional Continuous Integration checks and other automated checks will likely be added to the pull request system in the future.
