# FOSSA SCAN PE Instructions #

fossa-scan-pe contains two parts. The first is an daily automated scanner that runs on Jenkins.
The second is a semi-manual process needed to generate a license report.

## Jenkins' Daily PE Repository Scanning ##

The [`fossa_scan_puppet-enterprise-components`](https://cinext-jenkinsmaster-pipeline-prod-1.delivery.puppetlabs.net/view/fossa/job/fossa_scan_puppet-enterprise-components/) Jenkins job runs daily.

It is controlled by the `Jenkinsfile` in this repository.

It scans the git repositories listed in `ci/repo_list` in this repository.


### Repo-specific customizations  ###

The `ci/hooks` directory contains any hooks needed for special handling of repos.

Most repos use the `default` hook file. Each hook file can contain any one of three hooks:

  * `pre_build_hook`
  * `build_hook`
  * `fossa_hook`

These are written as bash shell functions. They are expected to send output to the standard
output.

Two helper functions are available:

  * `configure_lein` This helper function will set up [leiningen](https://leiningen.org) in the
  current directory. It takes an optional argument of the version of leiningen to install.

  * `bundle_install_everything` attempts to recursively descend the target respository and
  run `bundle install` in each of the directories. It aggregates the result in the current
  directory. I found this to be useful because I often had trouble getting FOSSA
  to detect multiple `Gemfile` files.

## Create an HTML License Report ##

## Generate a FOSSA bill of materials report ##

From https://app.fossa.com/components, select "Generate Report" from "Global Package Report Bundle".

In the dialog, click `Start New Report`

  * Scope: `PE Components`
  * Format: `CSV`
  * Project Data: `Package`, `Package Homepage`
  * License Detail: `License Template`, `Discovered License(s)`
  * Email: Your Email

Not that you must use exactly the above selections, otherwise the HTML will be incorrectly
formatted.

I hope to fix that in the future.

Click `Submit Report Job`.

After a few minutes, you'll receive a CSV file in email. Download it somewhere on a local machine.

## Generate License HTML ##

Run:

    $ ./generate-html-page <fossa-csv-file> > licenses.html
