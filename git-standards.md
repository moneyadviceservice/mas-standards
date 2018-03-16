## Git standards

This is a Git guide line motivated by various practices popular
among the community.

The goal is to use as a guidelines for developers opening a Pull request
and developers reviewing a Pull request.

## Branches

* Choose short and descriptive names.

```
# good
$ git checkout -b blog-migration

# bad - too vague
$ git checkout -b login_fix
$ git checkout -b refactor
```

* Identifiers from corresponding tickets in an external service (eg. Target
Process) are also good candidates for use in branch names. For example:

```
# Target process 8794 card
$ git checkout -b 8794-email-validation

# Target process 8795 card
$ git checkout -b 8795-fix-email-validation
```

* The type of the branch is also a good candidate for use in branch names.
The type can be one of these types:

   - feature: a new feature
   - fix: a bug fix
   - docs: changes to documentation
   - style: formatting, missing semi colons, etc; no code change
   - refactor: refactoring production code

```
# using the type of modification
$ git checkout -b feature/8794-email-validation
$ git checkout -b fix/8795-wrong-age-validation
$ git checkout -b refactor/8795-stamp-duty-calculator
```

* Use dashes to separate words.

```
# good
$ git checkout -b feature/add-pension-calculator

# bad
$ git checkout -b feature/add_pension_calculator
```

* Delete your branch from the upstream repository after it's merged, unless
there is a specific reason not to.

## Commits

* Each commit should represent a single logical change.
    > Don't make several logical changes in one commit. For example, if a patch fixes a bug and optimizes the performance of a feature, split it into two separate commits.

    > **Tip:** Use git add -p to interactively stage specific portions of the modified files.

* Don't split a single logical change into several commits.
    > For example, the implementation of a feature and the corresponding tests should be in the same commit.

* Commit early and often.
    > Small, self-contained commits are easier to understand and revert when something goes wrong. And this is particularly useful for using git-bisect.
    > **Tip:** Use git add -p to interactively stage specific portions of the modified files.

* Commits should be logically sorted.
    > If commit X depends on changes done in commit Y, then commit Y should come before commit X. Similarly, if commit A solves a bug introduced by commit B, it should also be stated in the message of commit A.

## Commit messages

* Use the editor, not the terminal, when writing a commit message:
  > Committing from the terminal encourages a mindset of having to fit everything in a single line which usually results in non-informative, ambiguous commit messages.

```
# good
$ git commit

# bad
$ git commit -m "Fix"
```

* Limit the commit subject line to 50 characters.
	> 50 characters is not a hard limit, just a rule of thumb.
  > Keeping subject lines at this length ensures that they are readable, and forces the author to think for a moment about the most concise way to explain what’s going on.
  > Tip: If you’re having a hard time summarizing, you might be committing too many changes at once.

* Capitalize the subject line and don't add periods at the end.

```
good:
Accelerate to 88 miles per hour

bad:
accelerate to 88 miles per hour.
```

* Use the imperative mood in the subject line
  > Tip: A properly formed Git commit subject line should always be able to complete the following sentence: If applied, this commit will "your subject line here"

```
good:
Refactor age validation for readability
Update getting started documentation
Remove deprecated methods
Release version 1.0.0

bad:
Fixed bug with Y
Changing behavior of X
More fixes for broken stuff
Sweet new API methods
```

* A commit message consists of three distinct parts separated by a blank line: the title, an optional body and an optional footer. The layout looks like this:

```
Short (50 chars or fewer) summary of changes

More detailed explanatory text, if necessary. Wrap it to
72 characters. In some contexts, the first
line is treated as the subject of an email and the rest of
the text as the body.  The blank line separating the
summary from the body is critical (unless you omit the body
entirely); tools like rebase can get confused if you run
the two together.

Further paragraphs come after blank lines.

- Bullet points are okay, too

- Use a hyphen or an asterisk for the bullet,
  followed by a single space, with blank lines in
  between

The pointers to your related resources can serve as a footer
for your commit message. Here is an example that is referencing
issues in a bug tracker:

Resolves: #56, #78
See also: #12, #34

Source http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html
```

* Use the body to explain what and why vs. how.

```
good:
   Simplify serialize.h's exception handling

   Remove the 'state' and 'exceptmask' from serialize.h's stream
   implementations, as well as related methods.

   As exceptmask always included 'failbit', and setstate was always
   called with bits = failbit, all it did was immediately raise an
   exception. Get rid of those variables, and replace the setstate
   with direct exception throwing (which also removes some dead
   code).

   As a result, good() is never reached after a failure (there are
   only 2 calls, one of which is in tests), and can just be replaced
   by !eof().

   fail(), clear(n) and exceptions() are just never called. Delete
   them.

bad: No context, no why and how.
Simplify serialize.h's exception handling
```

The above example is a real example from [bitcoin repository](https://github.com/bitcoin/bitcoin/commit/eb0b56b19017ab5c16c745e6da39c53126924ed6)

**Take a look at the full diff and just think how much time the author is
saving fellow and future committers by taking the time to provide this
context here and now. If he didn’t, the context would probably be lost forever.**

## Need Help ?

Always ask your team if you have any doubts about how to apply this guideline.
