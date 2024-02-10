<!-- {% raw %} -->
# Baler<img alt="A baler making bales of hay on a farm" title="A baler making bales of hay on a farm. Photo by Glendon Kuhns." width="30%" align="right" src="https://raw.githubusercontent.com/caltechlibrary/baler/main/.graphics/baler.jpg">

Baler (<em><ins><b>ba</b></ins>d <ins><b>l</b></ins>ink report<ins><b>er</b></ins></em>) is a [GitHub Action](https://docs.github.com/actions) that tests the URLs inside Markdown files of your GitHub repository. If any of them are invalid, Baler automatically opens a GitHub issue to report the problem(s).

[![License](https://img.shields.io/badge/License-BSD--like-lightgrey?style=flat-square)](https://github.com/caltechlibrary/baler/blob/main/LICENSE)
![GitHub](https://img.shields.io/badge/GitHub-%23000000.svg?logo=github&label=Actions&logoColor=white&style=flat-square)
[![Latest release](https://img.shields.io/github/v/release/caltechlibrary/baler.svg?color=b44e88&label=Release&style=flat-square)](https://github.com/caltechlibrary/baler/releases)
[![DOI](https://img.shields.io/badge/dynamic/json.svg?label=DOI&style=flat-square&colorA=gray&colorB=navy&query=$.pids.doi.identifier&uri=https://data.caltech.edu/api/records/0qetp-p3g60/versions/latest)](https://data.caltech.edu/records/0qetp-p3g60/latest)
[![GitHub marketplace](https://img.shields.io/badge/marketplace-Baler-green?logo=github&color=e4722f&style=flat-square&label=Marketplace)](https://github.com/marketplace/actions/baler-bad-link-reporter)


## Table of contents

* [Introduction](#introduction)
* [Installation](#installation)
* [Quick start](#quick-start)
* [Usage](#usage)
* [Known issues and limitations](#known-issues-and-limitations)
* [Getting help](#getting-help)
* [Contributing](#contributing)
* [License](#license)
* [Acknowledgments](#acknowledgments)


## Introduction

The URLs of hyperlinks inside Markdown files may be invalid for any number of reasons, including inaccuracies, typographical errors, and destinations that disappear over time. Manually testing the validity of links on a regular basis is laborious and error-prone. This is clearly a situation where automation helps, and that's where Baler comes in.

Baler (<em><ins><b>Ba</b></ins>d <ins><b>l</b></ins>ink report<ins><b>er</b></ins></em>) is a [GitHub Action](https://docs.github.com/actions) for automatically testing the links inside Markdown files in your repository, and filing issue reports when problems are found. It's designed to run when changes are pushed to a repository as well as on a regular schedule; the latter helps detect when previously-valid links stop working because of [link rot](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0115253) or other problems. Though it’s not the only GitHub Action available for this purpose, some features set Baler apart from the others:

* _Simplicity_: a single, short workflow handles both testing files and opening an issue.
* _Smart issue handling_: before it opens a new issue, Baler looks at open issues in the repository. If any previously reported the same URLs, Baler doesn't open a new issue.
* _Informative issue reports_: the issues opened by Baler not only list the URLs that failed; they also describe the reasons for the failures.
* _Simple exclusion list_: fake URLs meant as examples, or real URLs that nevertheless fail when tested from GitHub’s cloud runners, can be skipped by adding them to a file in your repository.

Baler lets you follow the proverb [“make hay while the sun shines”](https://grammarist.com/make-hay/) – take advantage of opportunities (in this case, easy automated testing) when they’re available.


## Installation

To use Baler, you need to create a GitHub Actions workflow file in your repository. Follow these simple steps:

1. In the main branch of your repository, create a `.github/workflows` directory if one does not already exist.
2. In the `.github/workflows` directory, create a file named `bad-link-reporter.yml`.
3. Copy and paste the [contents of `sample-workflow.yml`](https://raw.githubusercontent.com/caltechlibrary/baler/main/sample-workflow.yml) into your `bad-link-reporter.yml` file:

    ```yml
    # GitHub Actions workflow for Baler (BAd Link reportER) version 2.0.4.
    # This is available as the file "sample-workflow.yml" from the source
    # code repository for Baler: https://github.com/caltechlibrary/baler

    name: Bad Link Reporter

    # Configure this section ─────────────────────────────────────────────

    env:
      # Files to check. (Put patterns on separate lines, no leading dash.)
      files: |
        **/*.md

      # Label assigned to issues created by this workflow:
      labels: bug

      # Number of previous issues to check for duplicate reports.
      lookback: 10

      # Time (sec) to wait on an unresponsive URL before trying once more.
      timeout: 15

      # Optional file containing a list of URLs to ignore, one per line:
      ignore: .github/workflows/ignored-urls.txt

    on:
      schedule:  # Cron syntax is: "min hr day-of-month month day-of-week"
        - cron: 00 04 * * 1
      push:
        paths: ['**.md']
      workflow_dispatch:

    # The rest of this file should be left as-is ─────────────────────────

    run-name: Test links in Markdown files
    jobs:
      Baler:
        name: Link checker and reporter
        runs-on: ubuntu-latest
        permissions:
          issues: write
        steps:
          - uses: caltechlibrary/baler@v2
            with:
              files:    ${{github.event.inputs.files    || env.files}}
              labels:   ${{github.event.inputs.labels   || env.labels}}
              ignore:   ${{github.event.inputs.ignore   || env.ignore}}
              timeout:  ${{github.event.inputs.timeout  || env.timeout}}
              lookback: ${{github.event.inputs.lookback || env.lookback}}
    ```

4. Save the file, add it to your git repository, and commit the changes.
5. (If you did the steps above outside of GitHub) Push your repository changes to GitHub.


## Quick start

Once the workflow is installed in your repository on GitHub, Baler will run whenever a configured trigger event occurs. The trigger conditions are specified in the `on` statement of the `bad-link-reporter.yml` workflow file. The default workflow sets the conditions to be pull requests, a scheduled run once a week, and manual execution.

Right after installing the workflow in your GitHub repository, it's wise to do a manual test run in order to check that things are working as expected.

1. Go to the _Actions_ tab in your repository and click on the workflow named "Bad Link Reporter" in the sidebar on the left<p align="center"><img src="https://raw.githubusercontent.com/caltechlibrary/baler/main/.graphics/github-run-workflow.png" alt="Screenshot of GitHub actions workflow list" width="90%"></p>
2. In the page shown by GitHub next, click the <kbd>Run workflow</kbd> button in the right-hand side of the blue strip<p align="center"><img src="https://raw.githubusercontent.com/caltechlibrary/baler/main/.graphics/github-run-workflow-button.png" alt="Screenshot of GitHub Actions workflow run button" width="75%"></p>
3. In the pull-down, click the green <kbd>Run workflow</kbd> button near the bottom<p align="center"><img src="https://raw.githubusercontent.com/caltechlibrary/baler/main/.graphics/github-workflow-run-button.png" alt="Screenshot of GitHub Actions workflow run menu" width="40%"></p>
4. Refresh the web page and a new line will be shown named after your workflow file<p align="center"><img src="https://raw.githubusercontent.com/caltechlibrary/baler/main/.graphics/github-workflow-running.png" alt="Screenshot of GitHub Actions running" width="90%"></p>
5. Click the title of that running workflow to make GitHub show the progress and results.

At the conclusion of the run, if any invalid or unreachable URLs were found in your repository's Markdown files, Baler will have opened a new issue to report the problems. If Baler found no problems, it will only print a message to that effect in the job results page.


## Usage

Baler’s behavior is controlled by the `bad-link-reporter.yml` workflow file. There are two aspects of the behavior: (a) events that cause Baler to run, and (b) characteristics that can be controlled by setting  parameter values in the workflow file.


### Triggers that cause workflow execution

The default triggers in the sample workflow are:

* push requests that involve `.md` files
* weekly scheduled runs
* manual dispatch execution of the workflow

Triggering the workflow on pushes is typically the expected behavior: when you save changes to a file, you would probably like to be notified if it contains a broken link. Triggering on pushes also supports users who edit files on GitHub and use pull requests to add their changes, because it ensures the workflow is only executed once and not twice (which would happen if it also used `pull_request` as a trigger). When triggered this way, Baler only tests links in `.md` files that actually changed in the push compared to the versions of those files in the destination branch.

Triggering on pushes does have a downside: if you make several edits in a row, the workflow will run on each push (or each file save, if editing on GitHub). If there is a bad link in the file, it could lead to multiple identical issues being filed – except that it won't, because Baler is smart enough to check if a past issue already reported the same URLs. So although each push will trigger a workflow run, no new issues will be opened if nothing has changed in terms of bad links.

A once-a-week cron/scheduled execution is an important way to find links that worked in the past but stopped working due to link rot or other problems. If the Markdown files in your repository are not edited for an extended period of time, no pushes will occur to cause Baler to run; thus, it makes sense to run it periodically irrespective of editing activity, to make sure that links in the Markdown files are still valid. When invoked by cron, the workflow tests all `.md` files matched by the pattern defined by `files`, regardless of whether the files were modified in the most recent commit.

Finally, the manual dispatch lets you start the workflow manually. When invoked this way, the workflow again tests all `.md` files matched by the pattern defined by `files`, regardless of whether the files were modified in the latest commit. Rationale: if you're invoking the action manually, you probably intend to test all the files as they exist in the repository now, and not just the files changed in the last commit.

For more information about schedule-based execution, please see the GitHub document ["Workflow syntax for GitHub Actions"](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#onschedule). For more information about other triggers you can use, please see the GitHub document ["Triggering a workflow"](https://docs.github.com/en/actions/using-workflows/triggering-a-workflow).


### Parameters that affect Baler's behavior

A few parameters control the behavior of Baler, as described below.

#### `files`

The input parameter `files` sets the file name pattern that identifies the Markdown files Baler examines. A `*` in a pattern matches zero or more characters but does not match `/`; a `**` in a pattern matches zero or more characters including `/`. For example, the following matches any `.md` file anywhere at the top level and in any subdirectory:

```yml
files: |
  **/*.md
```

The following matches only top-level `.md` files and those at or below the top-level directory `docs`, but not other subdirectories:

```yml
files: |
  *.md
  docs/**/*.md
```


#### `labels`

When Baler opens a new issue after it finds problems, it can optionally assign a label to the issue. The value of this input parameter should be the name of one or more labels that is already defined in the GitHub repository's issue system. Multiple issue labels can be written, with commas between them. The default label is `bug`.

#### `lookback`

If Baler finds invalid URLs, then before it opens an issue, it checks the previous open issues in the repository and compares the reports. If a previous issue looks like a duplicate of what the new issue _would_ be, Baler does not create a new issue. The number of previous issues to check is determined by the input parameter `lookback`. The maximum number is 100.

#### `timeout`

The time (in seconds) that Baler should wait on a URL that doesn't respond before giving up and trying to reach the URL one more time. Baler will wait 15&nbsp;seconds between attempts. It will try an unresponsive URL a total of two times before reporting a timeout.

#### `ignore`

The value of the input parameter `ignore` should be a plain text file file containing URLs that Baler should ignore. The default value is `.github/workflows/ignored-urls.txt`. The file does not have to exist; if it doesn't exist, this parameter simply has no effect. The parameter can only reference a file in the repository and not an external file. Each URL should be written alone on a separate line of the file. They can be written as regular expressions; e.g., `https://example\.(com|org)`.

Telling Baler to ignore certain URLs is useful if some of your files contain fake URLs used as examples in documentation, or when certain real URLs are repeatedly flagged as unreachable when the workflow runs in GitHub's computing environment (see [next section below](#known-issues-and-limitations)).


## Known issues and limitations

Baler is designed to test only URLs that use the scheme `https` or `http`.

When Baler runs on GitHub, it will sometimes mysteriously report a link as unreachable even though you can access it without trouble from your local computer. It's not yet clear what causes this. My current best guess is that it's due to network routing or DNS issues in the environment where the link checker actually runs (i.e., GitHub's computing environment).

Baler may take a long time to run if one or more links in a file are timing out. The reason is that it has to wait for a timeout to, well, _time out_, and then wait some more before trying one more time (and then wait for another timeout period if the retry fails). It has to do this for every link that times out. The more URLs that do this, the longer the overall process will take. If you encounter links that time out when Baler runs as a GitHub Action but that resolve properly when you visit them from your browser, you can add those URLs to the ignore list (see [above](#ignore)) to skip testing them in the future.

Adding problematic URLs to the `ignore` file is a simple workaround, but there is a downside. If they are never tested, then Baler can't report if they really _do_ go stale in the future. A better solution would be to implement an adaptive algorithm by which Baler remembers timeout failures and stops testing the problematic URLs only for a time, then resumes testing them again automatically in the future. Unfortunately, this can't be implemented until the problem described in the first paragraph (that some URLs time out _only_ when Baler runs on GitHub) is resolved.

Finally, some sites deliberately block access from GitHub, presumably in an attempt to block scrapers or bots or other processes running from people's GitHub actions. This will typically show up in Baler's reports as HTTP code 403, "Failed: Network error: Forbidden". The only thing to do in such cases is to double-check that the URLs are valid from your local computer, and if they are, add them to the ignore list (see [above](#ignore)).


## Getting help

If you find an issue, please submit it in [the GitHub issue tracker](https://github.com/caltechlibrary/baler/issues) for this repository.


## Contributing

Your help and participation in enhancing Baler is welcome!  Please visit the [guidelines for contributing](https://github.com/caltechlibrary/baler/blob/main/CONTRIBUTING.md) for some tips on getting started.


## License

Software produced by the Caltech Library is Copyright © 2024 California Institute of Technology.  This software is freely distributed under a BSD-style license.  Please see the [LICENSE](LICENSE) file for more information.


## Acknowledgments

The image of a baler used at the top of this README file was obtained from [Wikimedia Commons](https://commons.wikimedia.org/wiki/File:Baling_Small_Square_Bales_with_Accumulator.jpg) on 2023-12-11. The photo was taken and contributed by [Glendon Kuhns](https://commons.wikimedia.org/wiki/User:Gkuhns) and made available under the [Creative Commons CC0 1.0 license](https://commons.wikimedia.org/wiki/File:Baling_Small_Square_Bales_with_Accumulator.jpg#Licensing).

Numerous other broken link checkers similar to Baler can be found in GitHub. Some of them served as sources of ideas for what to do in Baler, and I want to acknowledge this debt. The following are notable programs that I looked at (and if you are the author of another one not listed here, please don't feel slighted – I probably missed it simply due to limited time, inadequate or incomplete search, or lack of serendipity):

* [Broken Link Checker Action](https://github.com/marketplace/actions/broken-link-checker-action)
* [GitHub Repo README.md Dead Link Finder](https://github.com/MrCull/GitHub-Repo-ReadMe-Dead-Link-Finder)
* [linksnitch](https://github.com/marketplace/actions/linksnitch-action)
* [Markdown link check](https://github.com/gaurav-nelson/github-action-markdown-link-check)
* [md-links](https://github.com/raulingg/md-links)
* [My Broken Link Checker](https://github.com/marketplace/actions/my-broken-link-checker)

Baler makes use of the following excellent software packages and GitHub Actions:

* [lychee](https://github.com/lycheeverse/lychee) – fast, async, stream-based link checker written in Rust
* [peter-evans/create-issue-from-file](https://github.com/peter-evans/create-issue-from-file) – A GitHub action to create an issue
* [tj-actions/changed-files](https://github.com/tj-actions/changed-files) – GitHub action to retrieve files and directories
* [tj-actions/glob](https://github.com/tj-actions/changed-files) – GitHub action to match file glob patterns

This work was funded by the California Institute of Technology Library.

<div align="center">
  <br>
  <a href="https://www.caltech.edu">
    <img width="100" height="100" alt="Caltech logo" src="https://raw.githubusercontent.com/caltechlibrary/baler/main/.graphics/caltech-round.png">
  </a>
</div>
<!-- {% endraw %} -->
