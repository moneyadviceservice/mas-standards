# Money Advice Service Code Review Guide

  1. [General Guidelines](#general-guidelines)
  1. [Create Feature Branch](#create-feature-branch)
  1. [Open Pull Request](#open-pull-request)
  1. [Code Review](#code-review)
  1. [Merge Pull Request](#merge-pull-request)
  1. [Code Review Checklist](#checklist)
  1. [Pull Request Format](#pull-request-format)


## General Guidelines

  - All changes must be reviewed, other than bumping the version of an internal gem
  - Before merging a PR, it should have positive review (thumb - :+1: / LGTM) by two or more developers
  - Anyone can review and reviewers should be careful when leaving reviews. (No offense / criticism / judgement )
  - A review submitter shouldn’t take the review personally
  - Always provide proper reason / facts to support your review / opinion
  - Link to the relevant section in the standards repo when commenting on code style issues
  - No changes should be made directly to the master branch
  - Pull requests should only be merged to master when they are ready to be deployed to staging and then production

  The steps involved in the process are:

  1. [Create Feature Branch](#create-feature-branch)
  1. [Open Pull Request](#open-pull-request)
  1. [Code Review](#code-review)
  1. [Merge Pull Request](#merge-pull-request)

## Create Feature Branch
  Prior to creating a new branch:

  - Ensure your master branch is up to date
  - Run all tests and ensure they are passing
  - Ensure there are no SASS or JS linting errors
  - Use Rubocop and ensure no cops are violated, where applicable. The official rubocop can be seen [here](https://github.com/moneyadviceservice/mas-standards/blob/master/.rubocop.yml)

  Create a new branch from master with a name that succinctly and accurately reflects the change and is preceded by the Target Process story id e.g.  "8083-global-nav". Commit your feature branch regularly to ensure work is not lost and to aid collaboration. If in the process of the life of your feature, you need to do a force push, use the `--force-with-lease` option.

## Open Pull Request
  When code is ready to be reviewed, rebase it against an up to date `master` branch and create a PR. Make sure the PR has the following characteristics, as they are used to expose progress, fixes and features outside the development team.

  The PR title should
  - make sense to any MAS Dev member
  - incorporate the Target Process story ID
  - Avoid technical and non-MAS acronyms, Some MASisms are fine
  - Write a complete sentence
  - Write in the present tense
  - Keep it as short as is practical but no shorter, e.g. "[8081] Make PLP out-of-stock styling clearer", Not "Added PLP CSS to make OOS marker pop more"
  - Use the temporary prefix, [WIP] to indicate a pr is not ready for review

  The PR description should include clear explanations of what the PR delivers

  Use acronyms, but explain them on first use. Except when you would genuinely expect readers to search them instantly.
  Use markdown / lists / etc liberally

## Code Review
  It is the responsibility of all members of the Developer Team to review PRs. Check the dev-feed slack channel which regularly issues reminders of open PRs.

  When giving feedback on a PR use common sense. Below are some guides for reviewing code

  - [Checklist for reviewing code](http://www.evoketechnologies.com/blog/code-review-checklist-perform-effective-code-reviews)
  - [More guidelines for code reviews](https://www.codeproject.com/articles/524235/codeplusreviewplusguidelines)

## Merge Pull Request
  When a PR has been reviewed, all comments addressed and has received 2 thumbs up, it may be merged to master.

  Before merging, rebase and squash commits. Consider grouping commits into chunks to ensure a clean and readable commit history, particularly for large PRs.

  Generally, the creator will merge the PR.
  Prefix the title with [DEV APPROVED].
  Once the pr has been merged, the feature branch should be deleted.

## Checklist

  ### General
  - Does the code work? Does it perform its intended function, the logic is correct etc.
  - Is all the code easily understood?
  - Does it conform to our agreed coding conventions?
  - Is there any redundant or duplicate code?
  - Is the code as modular as possible?
  - Can any global variables be replaced?
  - Is there any commented out code?
  - Do loops have a set length and correct termination conditions?
  - Can any of the code be replaced with library functions?
  - Can any logging or debugging code be removed?

  ### Security
  - Are all data inputs checked (for the correct type, length, format and range) ?
  - Where third-party utilities are used, are returning errors being caught?
  - Are output values checked and encoded?
  - Are invalid parameter values handled?

  ### Documentation
  - Do comments exist and describe the intent of the code?
  - Is any unusual behaviour or edge-case handling described?
  - Is the use and function of third-party libraries documented?
  - Are data structures and units of measurement explained?
  - Is there any incomplete code? If so, should it be removed or flagged with a suitable marker like “TODO”?

  ### Testing
  - Is the code testable? i.e. don’t add too many or hide dependencies, unable to initialise objects, test frameworks can use methods etc.
  - Do tests exist and are they comprehensive? i.e. has at least your agreed on code coverage.
  - Do unit tests actually test that the code is performing the intended functionality?
  - Are arrays checked for ‘out-of-bounds’ errors?
  - Could any test code be replaced with the use of an existing API?

## Pull Request Format
  - Prefix the branch & PR with the Target Process ticket ID eg. `[TP-000] Brief summary of branch`
  - Make sure to include:
    - Target Process ticket(s), please include Product Backlog & Team Development tickets
    - Summary description of the changes
    - Review Note - if your PR includes generated files that do not need to be reviewed or is incomplete please add a Review Note
    - Testing / QA notes
    - Example links / screenshots for UI changes

  ### Example PR
    - https://github.com/moneyadviceservice/wpcc/pull/24

  ### Pull Request Template

  ```
    **Target Process Tickets**

  https://moneyadviceservice.tpondemand.com/entity/8446-wpcc-store-app-specific-settings

  **Summary**
  Quick description of changes and expected behaviour.

  **Review Note**
  I want a review only for coding style / typo please (30% review)
  I want a review for all (styles / typo / logic / etc) (90% review)

  **Example**
  A few links or images showing before / after changes.

  **Testing / QA Notes**
  Brief notes to save your tester some time, and let them know / what to test for.
  ```