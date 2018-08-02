# Adapted from https://github.com/ruby-grape/danger/blob/master/Dangerfile
# Q: What is a Dangerfile, anyway? A: See http://danger.systems/

BIG_PULL_REQUEST_LINES = 500
APP_FILES = /^(app|lib)/
TEST_FILES = /^(features|spec|test)/

# ------------------------------------------------------------------------------
# Additional pull request data
# ------------------------------------------------------------------------------
pr_number = github.pr_json["number"]
pr_url = github.pr_json["_links"]["html"]["href"]

# ------------------------------------------------------------------------------
# What changed?
# ------------------------------------------------------------------------------
has_app_changes = !git.modified_files.grep(APP_FILES).empty?
has_test_changes = !git.modified_files.grep(TEST_FILES).empty?

# ------------------------------------------------------------------------------
# You've made changes to app, but didn't write any tests?
# ------------------------------------------------------------------------------
if has_app_changes && !has_test_changes
  warn(
    "There are code changes, but no corresponding tests. "\
    "Please include tests if this PR introduces any modifications in "\
    "behavior. If this is a refactor PR, please ignore the message."\
    "Refer to our mas standards guides about opening a PR"\
    "[here](link-to-the-mas-standards-about-opening-a-pr)",
    sticky: false)
end

# ------------------------------------------------------------------------------
# You've create a huge Pull request? You should try to split up better you work.
# ------------------------------------------------------------------------------
if git.lines_of_code > BIG_PULL_REQUEST_LINES
  warn(
    "This PR is huge. Consider next time to split up the work"\
    "into multiple PRs. A good guide about small PRs can be seen"\
    "here(link to mas-standards - small PRs guide)"
  )
end

# ------------------------------------------------------------------------------
# You opened a Pull Request with rubocop failing?
# ------------------------------------------------------------------------------
rubocop.lint(
  files: git.modified_files + git.added_files,
  report_danger: true
)

# ------------------------------------------------------------------------------
# Don't let testing shortcuts get into master by accident,
# ensuring that we don't get green builds based on a subset of tests
# ------------------------------------------------------------------------------
(git.modified_files + git.added_files - %w(Dangerfile)).each do |file|
  next unless File.file?(file)
  next unless file.end_with? '.rb'

  contents = File.read(file)
  if file.start_with?('spec')
    fail("`xit` or `fit` left in tests (#{file})") if contents =~ /^\w*[xf]it/
    fail("`fdescribe` left in tests (#{file})") if contents =~ /^\w*fdescribe/
  end
end

if status_report.values.flatten.any?
  markdown(
    "At Money Advice Service, we use Danger gem, to ensure that our commits and pull requests follow " \
    "[standards](https://github.com/moneyadviceservice/mas-standards/blob/master/Dangerfile). " \
    "If you think one of the points here has been raised in error (or shouldn't be in our standards), " \
    "open an issue or pull request on [our Danger config](https://github.com/moneyadviceservice/mas-standards).")
end
