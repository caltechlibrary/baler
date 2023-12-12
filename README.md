# Baler<img width="30%" align="right" src=".graphics/baler.jpg">

Baler (<em><ins><b>ba</b></ins>d <ins><b>l</b></ins>ink report<ins><b>er</b></ins></em>) is a [GitHub Action](https://docs.github.com/actions) that tests the URLs inside Markdown files of your GitHub repository. If any of them are invalid, Baler automatically files a issue to report the problems.

[![License](https://img.shields.io/badge/License-BSD--like-lightgrey)](https://choosealicense.com/licenses/bsd-3-clause)


## Table of contents

* [Introduction](#introduction)
* [Installation](#installation)
* [Usage](#usage)
* [Known issues and limitations](#known-issues-and-limitations)
* [Getting help](#getting-help)
* [Contributing](#contributing)
* [License](#license)
* [Acknowledgments](#authors-and-acknowledgments)


## Introduction

The URLs of hyperlinks inside Markdown files may be invalid for any number of reasons: the author might make typographical errors, or the link destinations might disappear over time, or other reasons. Manually testing links on a regular basis for validity is laborious and error-prone – this is clearly a case where automation is best. That is where Baler comes in.

Baler (<em><ins><b>ba</b></ins>d <ins><b>l</b></ins>ink report<ins><b>er</b></ins></em>) is a [GitHub Action](https://docs.github.com/actions) for automatically testing the links inside Markdown files in your repository, then filing issue reports when problems are found. It is by no means the first or only GitHub Action for this purpose. Baler aims to be different from the others through its simplicity, the use of a different link checker approach, and its informative issue reports.


## Installation

This action is available from the [GitHub Marketplace](https://github.com/marketplace?type=&verification=&query=baler). Once you find the page in the GitHub Marketplace, do the following:

1. In the main branch of your repository, create a `.github/workflows` directory if this directory does not already exist.
2. In the `.github/workflows` directory, create a file named `bad-link-reporter.yml`.
3. Copy and paste the following content into the file:
    ```yaml
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

Notice that the default configuration does **not** trigger execution on every push. That's because running tests at every push is rarely a good idea: if you're actively editing a file like the README file and it has an undiscovered URL error, you can easily generate many identical issue reports before you realize what happened. Instead, the author has found that a once-a-night run is good enough. It does, however, trigger on pull requests involving Markdown files, because that's a situation when it makes sense to test the URLs immediately.

You can use [other trigger events defined by GitHub](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows) if you wish.

A few parameters control the behavior of Baler; they are described below.

### `files`

### `ignore_list`

### `labels`


## Known issues and limitations


## Getting help

If you find an issue, please submit it in [the GitHub issue tracker](https://github.com/caltechlibrary/baler/issues) for this repository.


## Contributing

Your help and participation in enhancing Baler is welcome!  Please visit the [guidelines for contributing](https://github.com/caltechlibrary/baler/blob/main/CONTRIBUTING.md) for some tips on getting started.


## License

Software produced by the Caltech Library is Copyright © 2023 California Institute of Technology.  This software is freely distributed under a BSD-style license.  Please see the [LICENSE](LICENSE) file for more information.

## Acknowledgments

This work was funded by the California Institute of Technology Library.

<div align="center">
  <br>
  <a href="https://www.caltech.edu">
    <img width="100" height="100" src="https://raw.githubusercontent.com/caltechlibrary/baler/main/.graphics/caltech-round.png">
  </a>
</div>
