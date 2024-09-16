# Static Analysis Security Testing CI/CD Integration

The repository host Github template ".sast-scan.yml" to run SAST scans with Semgrep CI.

## What is Semgrep?
Semgrep is a product security SAST (Static application security testing) tool which runs on the raw source code to detect vulnerability patterns. Apart from security check, Semgrep can be used to write static checks to identify violations of best practices, correctness, performance or maintainability.

Semgrep.dev is the web-portal which hosts the semgrep app. It is an easy way to deploy, manage and monitor Semgrep at scale. We would be using semgrep.dev for scanning and triaging all the projects which are in Github. 

## How to use Semgrep-App?

To get access to Semgrep App, you can login to [semgrep.dev](https://semgrep.dev/orgs/splunk/) through OKTA, using SSO of Splunk. You will automatically get access to Semgrep app with Member level privileges. Please note that you can run scans and get results without logging into semgrep app.

## Usage

### Importing the Templates

On the Github job uses the template `sast-scan.yml` of sast-scanning repository.

```yaml
name: SAST Scan
jobs:
  sast-scan:
    uses: splunk/sast-scanning/.github/workflows/sast-scan.yml@main
    secrets: inherit #Ensures that this project is able to access Organization level secrets which are necessary to run Semgrep Scans.
on:
  push:
    branches:
      - "main" #Add the main branch of your project
  pull_request:
    branches: [main]
```

#### Block/Fail the SAST job if there are vulnerabilities found ?
Currently, there are 3 modes to operate on open vulnerabilities using the variable "block_mode"

Value | Description |
---------:|:------|
`off` | (Default). Using this flag will not fail the job
`policy` | Any High severity vulnerability, i.e., Semgrep High Findings. > 0 will fail the job
`on` | Any High, Medium, Low severity will fail the pipeline.

##### Examples

```yaml
name: SAST Scan
jobs:
  sast-scan:
    uses: splunk/sast-scanning/.github/workflows/sast-scan.yml@main
    secrets: inherit #Ensures that this project is able to access Organization level secrets which are necessary to run Semgrep Scans.
    with:
      block_mode: "off" # Block mode turned off
on:
  push:
    branches:
      - "main" #Add the main branch of your project
  pull_request:
    branches: [main]
```

```yaml
name: SAST Scan
jobs:
  sast-scan:
    uses: splunk/sast-scanning/.github/workflows/sast-scan.yml@main
    secrets: inherit #Ensures that this project is able to access Organization level secrets which are necessary to run Semgrep Scans.
    with:
      block_mode: "on" # Block mode turned on to fail on any high,medium,low vulnerabilities
on:
  push:
    branches:
      - "main" #Add the main branch of your project
  pull_request:
    branches: [main]
```

```yaml
name: SAST Scan
jobs:
  sast-scan:
    uses: splunk/sast-scanning/.github/workflows/sast-scan.yml@main
    secrets: inherit #Ensures that this project is able to access Organization level secrets which are necessary to run Semgrep Scans.
    with:
      block_mode: "policy" # Alert mode turned to fail on policy set by prodsec team
on:
  push:
    branches:
      - "main" #Add the main branch of your project
  pull_request:
    branches: [main]
```

#### Advantages of Semgrep

1. Very lightweight and runs independently without the need for a centralized server.
2. No user or team onboarding is required for developer teams. Teams need to integrate the CICD block/template.
3. Scalable and transparent to developers what vulnerability patterns are scanned in the code
4. Open to contribute rules, i.e., and we encourage developers to contribute and improve rule set which will enhance the tool's capability and improve the overall posture of their product security.
5. Many open-source rulesets detect bad patterns other than application code, such as identifying Dockerfile best practices, Kubernetes, terraform, secrets detection, etc.

### Semgrep CI

* Semgrep can be run within CI (continuous integration) environments. By integrating Semgrep into your CI environment, your development cycle benefits from the automated scanning of repositories.
* A Semgrep CI job can connect to Semgrep Cloud Platform, a centralized location for managing findings, sending notifications, and configuring Semgrep rules for scanning.
* Semgrep CI will use rules configured on Semgrep Cloud Platform by the ProdSec team.

### Additional features of Semgrep Cloud Platform
In addition to the features mentioned previously, Semgrep Cloud Platform has the following features:

1. Diff-aware scanning: Semgrep Cloud Platform can scan only changes in files when running on a pull or merge request (PR or MR). This keeps the scan fast, tracks when a finding is fixed, and reduces the number of duplicated findings.
2. Receiving results (findings) as PR or MR comments: This feature enables you to receive PR or MR comments from Semgrep Cloud Platform on the lines of code that generated a finding.
3. Hyperlinks to code: Semgrep Cloud Platform collects findings in a Findings page. In this page, you can click on a finding to return to your Github repository to view the lines of code in your repository that generated the finding.
4. GitHub Advanced Security Dashboard: Send Semgrep findings to GitHub Advanced Security Dashboard.

### Help

For more information go the [Confluece Page of SAST for Github](https://splunk.atlassian.net/wiki/spaces/PROD/pages/1078166210996/Semgrep-App+Overview+For+Github+Projects+End-End+Workflow)

If you have any questions, drop by `#sast-community` on slack!
