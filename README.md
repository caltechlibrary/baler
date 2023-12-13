# Baler<img alt="A baler making bales of hay on a farm" title="A baler making bales of hay on a farm. Photo by Glendon Kuhns." width="33%" align="right" src=".graphics/baler.jpg">

Baler (<em><ins><b>ba</b></ins>d <ins><b>l</b></ins>ink report<ins><b>er</b></ins></em>) is a [GitHub Action](https://docs.github.com/actions) that tests the URLs inside Markdown files of your GitHub repository. If any of them are invalid, Baler automatically files an issue to report the problem(s).

[![License](https://img.shields.io/badge/License-BSD--like-lightgrey)](https://choosealicense.com/licenses/bsd-3-clause)


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

The URLs of hyperlinks inside Markdown files may be invalid for any number of reasons: the author might make typographical errors, or the link destinations might disappear over time, or other reasons. Manually testing the validity of links on a regular basis is laborious and error-prone – this is clearly a case where automation is best. That's where Baler comes in.

Baler (<em><ins><b>ba</b></ins>d <ins><b>l</b></ins>ink report<ins><b>er</b></ins></em>) is a [GitHub Action](https://docs.github.com/actions) for automatically testing the links inside Markdown files in your repository, then filing issue reports when problems are found. It's by no means the first or only GitHub Action for this purpose. Baler aims to be different from the others through its simplicity, the use of a different link checker approach, and its informative issue reports.


## Installation

This action is available from the [GitHub Marketplace](https://github.com/marketplace?type=&verification=&query=baler). Once you find the page in the GitHub Marketplace, do the following:

1. In the main branch of your repository, create a `.github/workflows` directory if this directory does not already exist.
2. In the `.github/workflows` directory, create a file named `bad-link-reporter.yml`.
3. Copy and paste the following content into the file:

    ```yaml
    # This is available as the file "sample-workflow.yml" from the source
    # code repository for Baler: https://github.com/caltechlibrary/baler/.

    name: "Bad Link Reporter"

    # Configure this section ─────────────────────────────────────────────

    env:
      # Files examined by the workflow:
      files: '*.md'

      # Optional file containing a list of URLs to ignore, one per line:
      ignore: '.github/workflows/ignored-urls.txt'

      # Label assigned to issues created by this workflow:
      labels: 'bug'

    on:
      schedule:
        - cron: "00 11 * * *"
      pull_request:
        paths: ['**.md']
      push:
        paths:
          - .github/workflows/bad-link-reporter.yml
          - .github/workflows/ignored-urls.txt
      workflow_dispatch:
        inputs:
          debug:
            description: "Run unconditionally in debug mode"
            type: boolean

    # The rest of this file should be left as-is ─────────────────────────

    run-name: Test links in files
    jobs:
      run-baler:
        name: Run Bad Link Reporter
        runs-on: ubuntu-latest
        steps:
          - uses: mhucka/baler@main
            with:
              files:  ${{github.event.inputs.files  || env.files}}
              labels: ${{github.event.inputs.labels || env.labels}}
              ignore: ${{github.event.inputs.ignore || env.ignore}}
              debug:  ${{github.event.inputs.debug  || env.debug}}
    ```

4. Save the file, add it to your git repository, and commit the changes.
5. (If you did the steps above outside of GitHub) Push your repository changes to GitHub.

Refer to the next section for more information.


## Usage

The trigger condition that causes Baler to run is determined by the `on` statement in your `bad-link-reporter.yml` workflow file. The default triggers are:

* a scheduled run every night
* pull requests involving `.md` files
* push requests involving the workflow file itself or the optional list of ignored URLs
* manual workflow dispatch execution

Notice that the default configuration does **not** trigger execution on every push. That's because running tests at every push is rarely a good idea: if you're actively editing a file like the README file and pushing it to GitHub while it has an undiscovered URL error, you can easily generate many identical issue reports before you realize what happened. Instead, the author has found that a once-a-night run is good enough. The workflow does, however, also trigger on pull requests involving Markdown files, because that's a situation when it makes sense to test the URLs immediately.

You can use [other trigger events defined by GitHub](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows) if you wish.

### Workflow configuration parameters

A few parameters control the behavior of Baler. They are described below.

#### `files`

The input parameter `files` sets the file name pattern that identifies the files Baler examines. The default is `*.md`, which makes Baler examine the Markdown files at the top level of a repository. You can set this to multiple patterns by separating patterns with commas (without spaces).

#### `ignore_list`

The value of the input parameter `ignore_list` should be a plain text file file containing URLs that Baler should ignore. Each URL should be written alone on a separate line of the file. The default value is `.github/workflows/ignored-urls.txt`. The file does not have to exist; if it doesn't exist, this parameter simply has no effect. The parameter can only reference a file in the repository and not an external file.

#### `labels`

When Baler files a new issue, it can optionally assign a label to the issue. The value of this input parameter should be the name of one or more labels that is already defined in the GitHub repository's issue system. Separate multiple issue labels with commas.


## Known issues and limitations

When Baler runs on GitHub, it will sometimes mysteriously report a link as unreacheable even though you can access it without trouble from your local computer. It's not yet clear what causes this. My current best guess is that it's due to network routing or DNS issues in the environment where the link checker actually runs (i.e., GitHub's computing environment).


## Getting help

If you find an issue, please submit it in [the GitHub issue tracker](https://github.com/caltechlibrary/baler/issues) for this repository.


## Contributing

Your help and participation in enhancing Baler is welcome!  Please visit the [guidelines for contributing](https://github.com/caltechlibrary/baler/blob/main/CONTRIBUTING.md) for some tips on getting started.


## License

Software produced by the Caltech Library is Copyright © 2023 California Institute of Technology.  This software is freely distributed under a BSD-style license.  Please see the [LICENSE](LICENSE) file for more information.

## Acknowledgments

This work was funded by the California Institute of Technology Library.

The image of a baler used at the top of this README file was obtained from [Wikimedia Commons](https://commons.wikimedia.org/wiki/File:Baling_Small_Square_Bales_with_Accumulator.jpg) on 2023-12-11. The photo was taken and contributed by [Glendon Kuhns](https://commons.wikimedia.org/wiki/User:Gkuhns) and made available under the [Creative Commons CC0 1.0 license](https://commons.wikimedia.org/wiki/File:Baling_Small_Square_Bales_with_Accumulator.jpg#Licensing).

<div align="center">
  <br>
  <a href="https://www.caltech.edu">
    <img width="100" height="100" alt="Caltech logo" src="https://raw.githubusercontent.com/caltechlibrary/baler/main/.graphics/caltech-round.png">
  </a>
</div>
