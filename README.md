# Baler<img alt="A baler making bales of hay on a farm" title="A baler making bales of hay on a farm. Photo by Glendon Kuhns." width="33%" align="right" src=".graphics/baler.jpg">

Baler (<em><ins><b>ba</b></ins>d <ins><b>l</b></ins>ink report<ins><b>er</b></ins></em>) is a [GitHub Action](https://docs.github.com/actions) that tests the URLs inside Markdown files of your GitHub repository. If any of them are invalid, Baler automatically opens a GitHub issue to report the problem(s).

[![License](https://img.shields.io/badge/License-BSD--like-lightgrey?style=flat-square)](https://github.com/caltechlibrary/baler/blob/main/LICENSE)
[![Latest release](https://img.shields.io/github/v/release/caltechlibrary/baler.svg?color=b44e88&label=Release&style=flat-square)](https://github.com/caltechlibrary/baler/releases)
[![DOI](https://img.shields.io/badge/dynamic/json.svg?label=DOI&style=flat-square&colorA=gray&colorB=navy&query=$.pids.doi.identifier&uri=https://data.caltech.edu/api/records/h75w5-y7y57)](https://data.caltech.edu/records/h75w5-y7y57)


## Table of contents

* [Introduction](#introduction)
* [Installation](#installation)
* [Usage](#usage)
* [Known issues and limitations](#known-issues-and-limitations)
* [Getting help](#getting-help)
* [Contributing](#contributing)
* [License](#license)
* [Acknowledgments](#acknowledgments)


## Introduction

The URLs of hyperlinks inside Markdown files may be invalid for any number of reasons: there might be typographical errors, or the destinations might disappear over time, or other reasons. Manually testing the validity of links on a regular basis is laborious and error-prone. This is clearly a case where automation is best. That's where Baler comes in.

Baler (<em><ins><b>Ba</b></ins>d <ins><b>l</b></ins>ink report<ins><b>er</b></ins></em>) is a [GitHub Action](https://docs.github.com/actions) for automatically testing the links inside Markdown files in your repository, and filing issue reports when problems are found. It is neither the first nor only GitHub Action for this purpose – so what sets Baler apart from the others?

* _Simplicity_: a single action for both testing files and opening an issue.
* _Smart issue handling_: before it opens a new issue, Baler looks at open issues in the repository and checks if any already reported the same URLs. If so, Baler doesn't open a new issue.
* _Informative issue contents_: the issues opened by Baler not only list the URLs that failed; they also describe the reasons for the failures.


## Installation

To use Baler, you need to create a GitHub Actions workflow file in your repository. Follow these simple steps.

### Add the workflow file to your repository

1. In the main branch of your repository, create a `.github/workflows` directory if this directory does not already exist.
2. In the `.github/workflows` directory, create a file named `bad-link-reporter.yml`.
3. Copy and paste the [contents of `sample-workflow.yml`](https://raw.githubusercontent.com/caltechlibrary/baler/main/sample-workflow.yml) into the file:

    https://github.com/caltechlibrary/baler/blob/9d83709020fe15744fd112d249ae045119e8ad58/sample-workflow.yml#L1-L50

4. Save the file, add it to your git repository, and commit the changes.
5. (If you did the steps above outside of GitHub) Push your repository changes to GitHub.

### Test the workflow

Once you have created the workflow file and pushed it to GitHub, it's wise to do a manual test run in order to check that things are working as expected.

[... FILL IN ...]


## Usage

The trigger condition that causes Baler to run is determined by the `on` statement in your `bad-link-reporter.yml` workflow file. The default triggers are:

* a scheduled run every night
* pull requests involving `.md` files
* manual workflow dispatch execution

The workflow triggers on pull requests involving Markdown files, because that's a situation when it makes sense to test the URLs immediately. However, the default configuration does **not** trigger execution on every push. That's because running tests at every push is rarely a good idea: if you're actively editing a file like the README file and it has an undiscovered URL error, you can easily trigger the creation of many issue reports before you realize what happened. Instead, a once-a-night run is good enough.

For more information about schedule-based execution, please see the GitHub document ["Workflow syntax for GitHub Actions"](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#onschedule). For more information about other triggers you can use, please see the GitHub document ["Triggering a workflow"](https://docs.github.com/en/actions/using-workflows/triggering-a-workflow).


### Workflow configuration parameters

A few parameters control the behavior of Baler. They are described below.

#### `files`

The input parameter `files` sets the file name pattern that identifies the Markdown files Baler examines. The default is `*.md`, which makes Baler examine the Markdown files at the top level of a repository. You can set this to multiple patterns by separating patterns with commas (without spaces).

#### `labels`

When Baler opens a new issue after it finds problems, it can optionally assign a label to the issue. The value of this input parameter should be the name of one or more labels that is already defined in the GitHub repository's issue system. Multiple issue labels can be written, with commas between them.

#### `lookback`

If Baler finds invalid URLs, then before it opens an issue, it checks the previous open issues in the repository and compares the reports. If a previous issue looks like a duplicate of what the new issue would be, Baler does not create a new issue. The number of previous issues to check is determined by the input parameter `lookback`. The maximum number is 100.

#### `timeout`

The time (in seconds) that Baler should wait on a URL that is not responding before giving up and trying to reach the URL more time. Baler will wait 10&nbsp;seconds between attempts. It will try an unresponsive URL a total of two times before reporting a timeout.

#### `ignore`

The value of the input parameter `ignore` should be a plain text file file containing URLs that Baler should ignore. The default value is `.github/workflows/ignored-urls.txt`. The file does not have to exist; if it doesn't exist, this parameter simply has no effect. The parameter can only reference a file in the repository and not an external file. Each URL should be written alone on a separate line of the file. They can be written as regular expresions; e.g., `https://example\.(com|org)`.

Telling Baler to ignore certain URLs is useful if some of your files contain fake URLs used as examples in documentation, or when certain real URLs are repeatedly flagged as unreachable when the workflow runs in GitHub's computing environment (see [next section below](#known-issues-and-limitations)).


### GitHub event handling

The following is an explanation of how different types of GitHub events are handled, and the reasoning behind the choices:

* _`workflow_dispatch` events_: test all `.md` files matched by the pattern defined by `inputs.files`, regardless of whether the files were modified in the latest commit. Rationale: if you're invoking the action manually, you probably intend to test the files as they exist in the repository now, and not relative to a past commit or other past event.
* _`schedule` events_: test all `.md` files matched by `inputs.files`, regardless of whether they have been modified in the latest commit. Rationale: (1) it wouldn't make sense to have periodic runs test only the files modified in the latest commit, because a _previous_ commit (or the n<sup>th</sup> previous) might also have modified some Markdown files, which means the latest commit is not a good reference point; and (2) regularly testing _all_ Markdown files, regardless of whether they were edited recently, is an important way to find links that worked in the past but stopped working due to link rot or other problems.
* _All other event types_: test the `.md` files that will be changed (compared to the versions of those files in the destination branch) as a result of the event. The exact trigger condition is under the control of the invoking workflow. For example, in the [sample workflow](sample-workflow.yml), `pull_request` events result in testing `.md` files that were modified by the pull request, but `push` events do not.


## Known issues and limitations

Baler only tests URLs that use the scheme `https` or `http`.

When Baler runs on GitHub, it will sometimes mysteriously report a link as unreacheable even though you can access it without trouble from your local computer. It's not yet clear what causes this. My current best guess is that it's due to network routing or DNS issues in the environment where the link checker actually runs (i.e., GitHub's computing environment).

Baler may take a long time to run if one or more links in a file are timing out. The reason is that it has to wait for timeouts to occur, and then wait some more before trying one more time (and then wait for another timeout period if the retry fails). It has to do this for every link that times out. The more URLs that do this, the longer the overall process will take. If you encouter links that time out when Baler runs as a GitHub Action but that resolve properly when you visit them from your browser, you can add those URLs to the file defined by the `ignore` parameter (see [above](#ignore)) to skip testing them in the future.

Adding problematic URLs to the `ignore` file is a simple workaround, but there is a downside. If they are never tested, then Baler can't report if they really _do_ go stale in the future. A better solution would be to implement an adaptive algorithm by which Baler remembers timeout failures and stops testing the problematic URLs only for a time, then resumes testing them again automatically in the future. Unfortunately, this can't be implemented until the problem described in the first paragraph (that some URLs time out _only_ when Baler runs on GitHub) is resolved.


## Getting help

If you find an issue, please submit it in [the GitHub issue tracker](https://github.com/caltechlibrary/baler/issues) for this repository.


## Contributing

Your help and participation in enhancing Baler is welcome!  Please visit the [guidelines for contributing](https://github.com/caltechlibrary/baler/blob/main/CONTRIBUTING.md) for some tips on getting started.


## License

Software produced by the Caltech Library is Copyright © 2023 California Institute of Technology.  This software is freely distributed under a BSD-style license.  Please see the [LICENSE](LICENSE) file for more information.


## Acknowledgments

The image of a baler used at the top of this README file was obtained from [Wikimedia Commons](https://commons.wikimedia.org/wiki/File:Baling_Small_Square_Bales_with_Accumulator.jpg) on 2023-12-11. The photo was taken and contributed by [Glendon Kuhns](https://commons.wikimedia.org/wiki/User:Gkuhns) and made available under the [Creative Commons CC0 1.0 license](https://commons.wikimedia.org/wiki/File:Baling_Small_Square_Bales_with_Accumulator.jpg#Licensing).

Numerous other broken link checkers similar to Baler can be found in GitHub. Some of them served as sources of ideas for what to do in Baler, and I want to acknowledge this debt. The following are notable programs that I looked at (and if you are the author of another one not listed here, please don't feel slighted – I probably missed it simply due to limited time, inadequate or incomplete search, or lack of serendipity):

* [My Broken Link Checker](https://github.com/marketplace/actions/my-broken-link-checker)
* [Broken Link Checker Action](https://github.com/marketplace/actions/broken-link-checker-action)
* [Markdown link check](https://github.com/gaurav-nelson/github-action-markdown-link-check)
* [linksnitch](https://github.com/marketplace/actions/linksnitch-action)
* [md-links](https://github.com/raulingg/md-links)

This work was funded by the California Institute of Technology Library.

<div align="center">
  <br>
  <a href="https://www.caltech.edu">
    <img width="100" height="100" alt="Caltech logo" src="https://raw.githubusercontent.com/caltechlibrary/baler/main/.graphics/caltech-round.png">
  </a>
</div>
