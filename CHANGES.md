# Change log for Baler

## Version 2.0.3 (2024-02-06)

Changes in this version:

* Fix a minor bug in the Makefile that prevented `post-release` from being invoked automatically.
* Replace relative links to images in `README.md` with absolute URLs to the files in raw.githubusercontent.com, to solve broken images in the GitHub Pages version.


## Version 2.0.2 (2024-01-31)

Changes in this version:

* The documentation did not adequately explain how to specify more than one path/pattern for the `files` parameter. Now fixed (hopefully), with new examples in `README.md`.


## Version 2.0.1 (2024-01-30)

Changes in this version:

* Use v5 of [peter-evans/create-issue-from-file](https://github.com/peter-evans/create-issue-from-file) to solve warning about Node version deprecation.
* Update more repository workflows to their latest versions.
* Added a `.editorconfig` file for good measure.
* Add more repo metadata fields to `CITATION.cff` and `codemeta.json`.
* In `README.md`, use a better URL that always points to the latest version of the archived copy in our InvenioRDM server.


## Version 2.0.0

Changes in this version:

* Use just-released v20 of [tj-actions/glob](https://github.com/tj-actions/glob) to solve warning about Node version deprecation in v19 of the action.


## Version 1.0.0

First full release.


## Version 0.0.2

This version features overhauled logic, updated sample workflow, and updated documentation.


## Version 0.0.1

First complete version for testing.


## Version 0.0.0

Created this repository.
