# Adapted from https://github.com/ruby-grape/danger/blob/master/Dangerfile
# Q: What is a Dangerfile, anyway? A: See http://danger.systems/

BIG_PULL_REQUEST_LINES = 500
APP_FILES = /^(app|lib)/
TEST_FILES = /^(features|spec|test)/
ENGLISH_LOCALE_FILE = 'config/locales/en.yml'.freeze
WELSH_LOCALE_FILE =   'config/locales/cy.yml'.freeze
LOCALE_FILES = [ENGLISH_LOCALE_FILE, WELSH_LOCALE_FILE].freeze
TARGETPROCESS_TICKET_TEXT = "moneyadviceservice.tpondemand.com"

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
# You forgot to link the Pull Request to a Targetprocess ticket?
# ------------------------------------------------------------------------------
unless github.pr_body.include?(TARGETPROCESS_TICKET_TEXT)
  warn(
    "There is no Targetprocess ticket associated to this PR. "\
    "Please include the link to the corresponding task (TP-XXXX). "\
    "If no ticket matches these changes consider creating one and linking it here."
  )
end

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
  force_exclusion: true,
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

# ------------------------------------------------------------------------------
# Do you have a Rails engine and you didn't bump the version?
# ------------------------------------------------------------------------------
is_an_engine = Dir.glob('lib/*/engine.rb').any?

if is_an_engine
  version_file = Dir.glob('lib/*/version.rb').first
  unless git.modified_files.include?(version_file)
    warn(
      'It looks like this is a Rails engine, ' \
      "but no changes to #{github.html_link(version_file)} detected. " \
      'Did you forget to bump the version?'
    )
  end
end

# ------------------------------------------------------------------------------
# Did you update only the English locale forgetting Welsh one?
# Did you name the English/Welsh keys in different way?
# ------------------------------------------------------------------------------
def get_all_keys(hash)
  hash.each_with_object([]) do |(key, val), keys|
    if val.is_a?(Hash)
      keys.push(key, *get_all_keys(val))
    else
      keys << key
    end
  end
end

english_and_welsh = LOCALE_FILES.all? { |f| File.file?(f) }

if english_and_welsh
  only_one_changed = git.modified_files.one? { |f| LOCALE_FILES.include?(f) }
  if only_one_changed
    warn(
      'You modified the locale file only for one language. ' \
      "Are you sure you're not missing Welsh or English translation?"
    )
  else
    english_keys = get_all_keys(YAML.load_file(ENGLISH_LOCALE_FILE)['en'])
    welsh_keys = get_all_keys(YAML.load_file(WELSH_LOCALE_FILE)['cy'])
    missing_in_welsh = english_keys - welsh_keys
    missing_in_english = welsh_keys - english_keys

    if missing_in_welsh.any?
      warn(
        'English locale contains keys missing in Welsh locale. ' \
        "Missing keys: #{missing_in_welsh}."
      )
    end

    if missing_in_english.any?
      warn(
        'Welsh locale contains keys missing in English locale. ' \
        "Missing keys: #{missing_in_english}."
      )
    end
  end
end

if status_report.values.flatten.any?
  markdown(
    "At Money Advice Service, we use Danger gem, to ensure that our commits and pull requests follow " \
    "[standards](https://github.com/moneyadviceservice/mas-standards/blob/master/Dangerfile). " \
    "If you think one of the points here has been raised in error (or shouldn't be in our standards), " \
    "open an issue or pull request on [our Danger config](https://github.com/moneyadviceservice/mas-standards).")
end
