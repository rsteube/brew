# Deprecating, Disabling and Removing Casks

There are many reasons why casks may be deprecated, disabled or removed. This document explains the differences between each method as well as explaining when one method should be used over another.

## Overview

These general rules of thumb can be followed:

- `deprecate!` should be used for casks that _should_ no longer be used.
- `disable!` should be used for casks that _cannot_ be used.
- Casks that are no longer acceptable in `homebrew/cask` or have been disabled for over a year _should_ be removed.

## Deprecation

If a user attempts to install a deprecated cask, they will be shown a warning message but the install will proceed.

A cask should be deprecated to indicate to users that the cask should not be used and will be disabled in the future. Deprecated casks should continue to be maintained by the Homebrew maintainers if they continue to be installable. If this is not possible, they should be immediately disabled.

The most common reasons for deprecation are when the upstream project is deprecated, unmaintained, or archived.

Casks should only be be deprecated if at least one of the following are true:

- the cask does not install on any of our supported macOS versions
- the cask has outstanding CVEs

To deprecate a cask, add a `deprecate!` call. This call should include a deprecation date (in the ISO 8601 format) and a deprecation reason:

```ruby
deprecate! date: "YYYY-MM-DD", because: :reason
```

The `date` parameter should be set to the date that the project or version became (or will become) deprecated. If there is no clear date but the cask needs to be deprecated, use today's date. If the `date` parameter is set to a date in the future, the cask will not become deprecated until that date. This can be useful if the upstream developers have indicated a date when the project or version will stop being supported.

The `because` parameter can be a preset reason (using a symbol) or a custom reason. See the [Deprecate and Disable Reasons](#deprecate-and-disable-reasons) section below for more details about the `because` parameter.

## Disabling

If a user attempts to install a disabled cask, they will be shown an error message and the install will fail.

A cask should be disabled to indicate to users that the cask cannot be used and will be removed in the future. Disabled cask are those that could not be installed successfully any longer.

The most common reasons for disabling a cask are:

- it cannot be installed
- it has been deprecated for a long time
- the upstream URL has been removed

Popular casks (i.e. have more than 999 [analytics installs in the last 30 days](https://formulae.brew.sh/analytics/cask-install/30d/)) should not be disabled without a deprecation period of at least six months even if e.g. they do not install on all macOS versions.

Unpopular casks (i.e. have fewer than 1000 [analytics installs in the last 30 days](https://formulae.brew.sh/analytics/cask-install/30d/)) can be deprecated immediately for any of the reasons above e.g. they cannot be installed.

**Note: disabled casks in `homebrew/cask` will be automatically removed one year after their disable date.**

Unpopular casks (i.e. have fewer than 1000 [analytics installs in the last 30 days](https://formulae.brew.sh/analytics/cask-install/30d/)) can be manually removed three months after their disable date.

To disable a cask, add a `disable!` call. This call should include a deprecation date (in the ISO 8601 format) and a deprecation reason:

```ruby
disable! date: "YYYY-MM-DD", because: :reason
```

The `date` parameter should be set to the date that the reason for disabling came into effect. If there is no clear date but the cask needs to be disabled, use today's date. If the `date` parameter is set to a date in the future, the cask will be deprecated until that date (on which the cask will become disabled).

The `because` parameter can be a preset reason (using a symbol) or a custom reason. See the [Deprecate and Disable Reasons](#deprecate-and-disable-reasons) section below for more details about the `because` parameter.

## Removal

A cask should be removed if it does not meet our criteria for [acceptable casks](Acceptable-Casks.md), does not install, or has been disabled for over a year.

## Deprecate and Disable Reasons

When a cask is deprecated or disabled, a reason explaining the action must be provided.

There are two ways to indicate the reason. The preferred way is to use a pre-existing symbol to indicate the reason. The available symbols are listed below and can be found in the [`DeprecateDisable` module](https://github.com/Homebrew/brew/blob/master/Library/Homebrew/deprecate_disable.rb):

- `:does_not_build`: the cask cannot be installed on any supported macOS version.
- `:repo_archived`: the upstream repository has been archived and no replacement is pointed to that we can use
- `:repo_removed`: the upstream repository has been removed and no replacement is mentioned on the homepage that we can use
- `:unmaintained`: the project appears to be abandoned i.e. it has had no commits for at least a year and has critical bugs or CVE that have been reported and gone resolved longer. Note: some software is "done"; a lack of activity does not imply a need for removal.
- `:unsupported`: Homebrew's compilation of the software is not supported by the upstream developers (e.g. upstream only supports macOS versions older than 10.15)
- `:deprecated_upstream`: the project is deprecated upstream and no replacement is pointed to that we can use
- `:checksum_mismatch`: the checksum of the source for the casks's current version has changed since bottles were built and we cannot find a reputable source or statement justifying this

These reasons can be specified by their symbols (the comments show the message that will be displayed to users):

```ruby
# Warning: <cask> has been deprecated because it is deprecated upstream!
deprecate! date: "2020-01-01", because: :deprecated_upstream
```

```ruby
# Error: <cask> has been disabled because it does not build!
disable! date: "2020-01-01", because: :does_not_build
```

If these pre-existing reasons do not fit, a custom reason can be specified. Such reasons should be written to fit into the sentence `<cask> has been deprecated/disabled because it <reason>!`.

A well-worded example of a custom reason would be:

```ruby
# Warning: <cask> has been deprecated because it fetches unversioned dependencies at runtime!
deprecate! date: "2020-01-01", because: "fetches unversioned dependencies at runtime"
```

A poorly-worded example of a custom reason would be:

```ruby
# Error: <cask> has been disabled because it invalid license!
disable! date: "2020-01-01", because: "invalid license"
```
