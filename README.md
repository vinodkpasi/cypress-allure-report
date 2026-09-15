# Allure Report with Cypress & Mocha

[![Cypress](https://img.shields.io/badge/Cypress-12.14.0-17202C?logo=cypress)](https://www.cypress.io/)
[![Allure
Report](https://img.shields.io/badge/Allure-2.24.1-FF6A00)](https://allurereport.org/)
[![Node.js](https://img.shields.io/badge/Node.js-Required-339933?logo=node.js)](https://nodejs.org/)

A Cypress end-to-end automation project demonstrating **Allure Report
integration** with Cypress's Mocha-based test runner.

The project uses `@shelex/cypress-allure-plugin` to collect test results
and automatically generates a **single-file Allure HTML report** after
the Cypress test run.

## Features

-   Cypress 12.14.0 end-to-end testing
-   Mocha-based Cypress test execution
-   Allure reporting integration
-   Automatic cleanup of previous Allure results
-   Automatic generation of a single-file Allure report
-   Automatic opening of the generated report
-   Allure request attachment support
-   Reuse of Allure data across Cypress specs
-   Optional custom Allure report title and name
-   Custom post-processing of the generated Allure HTML

## Technology Stack

  Technology              Version / Details
  ----------------------- ----------------------------------------
  Node.js                 Required
  Cypress                 12.14.0
  Allure Commandline      2.24.1
  Cypress Allure Plugin   `@shelex/cypress-allure-plugin` 2.40.1
  Test Runner             Mocha through Cypress
  Language                JavaScript

## Project Structure

``` text
allurereport_mocha/
├── cypress/
│   ├── e2e/                  # Cypress end-to-end tests
│   └── plugins/
│       ├── script.txt        # Custom HTML/report script
│       └── scriptwriter.js   # Injects custom script into Allure HTML
├── cypress.config.js         # Cypress + Allure configuration
├── package.json              # Project dependencies and npm scripts
└── README.md
```

The repository also creates the following directories during execution:

``` text
allure-results/               # Raw Allure test results
allure-report/                # Generated Allure report
```

## Prerequisites

Install the following before running the project:

-   Node.js
-   npm
-   A supported browser for Cypress

Verify the installation:

``` bash
node --version
npm --version
```

## Installation

Clone the repository:

``` bash
git clone https://github.com/vinodkpasi/cypress-allure-report.git
```

Navigate to the project:

``` bash
cd cypress-allure-report
```

Install dependencies:

``` bash
npm install
```

The project dependencies are defined in `package.json` and include
Cypress, Allure Commandline, and the Cypress Allure plugin.

## Run Cypress in Open Mode

Launch Cypress interactively:

``` bash
npx cypress open
```

Select the required E2E specification from the Cypress UI.

## Run Cypress in Headless Mode

Execute the Cypress tests from the command line:

``` bash
npx cypress run
```

When the run completes, the configured `after:run` hook automatically:

1.  Generates Allure results into `allure-results`.
2.  Generates a single-file Allure report.
3.  Executes the custom `scriptwriter.js` post-processing script.
4.  Opens the generated Allure report.

## Open the Allure Report Manually

If the report has already been generated, run:

``` bash
npm run allure:open:report
```

This executes:

``` bash
npx allure open allure-report
```

## Allure Configuration

The Allure integration is configured in `cypress.config.js`.

Important settings include:

``` javascript
env: {
  allure: true,
  allureReuseAfterSpec: true,
  allureAttachRequests: true,
  allureClearSkippedTests: false,
  allureAddVideoOnPass: false,
  allureReportTitle: "Test Report",
  allureReportName: "Test Report"
}
```

### Configuration Details

  -----------------------------------------------------------------------
  Setting                             Purpose
  ----------------------------------- -----------------------------------
  `allure`                            Enables Allure integration

  `allureReuseAfterSpec`              Reuses Allure data after each
                                      specification

  `allureAttachRequests`              Enables request attachment support

  `allureClearSkippedTests`           Controls whether skipped tests are
                                      cleared

  `allureAddVideoOnPass`              Prevents video attachment for
                                      passing tests

  `allureReportTitle`                 Sets the report title

  `allureReportName`                  Sets the report name
  -----------------------------------------------------------------------

## Automatic Cleanup

Before every Cypress run, the project removes existing reporting
directories:

``` javascript
fs.rmSync("allure-report", { recursive: true, force: true });
fs.rmSync("allure-results", { recursive: true, force: true });
```

This helps ensure that the generated report contains results from the
current execution rather than stale data.

## Report Generation Flow

The configured `after:run` hook executes the following workflow:

``` text
Cypress Tests
     |
     v
Allure Plugin
     |
     v
allure-results/
     |
     v
Allure Generate
     |
     v
allure-report/index.html
     |
     v
scriptwriter.js
     |
     v
Final Allure HTML
     |
     v
Browser
```

The report is generated with:

``` bash
npx allure generate allure-results --single-file --clean -o allure-report
```

The `--single-file` option creates a self-contained HTML report, which
is convenient for sharing as a standalone report artifact.

## Custom Report Post-Processing

After generating the Allure report, the project executes:

``` bash
node cypress/plugins/scriptwriter.js
```

The script:

1.  Reads `cypress/plugins/script.txt`.
2.  Reads the generated `allure-report/index.html`.
3.  Replaces report placeholders such as:
    -   `{{allureReportTitle}}`
    -   `{{allureReportName}}`
4.  Injects the custom script before the closing `</body>` tag.
5.  Writes the updated HTML file back to `allure-report/index.html`.

This allows additional customization of the generated Allure report.

## NPM Scripts

The project currently provides the following npm script:

``` bash
npm run allure:open:report
```

For normal Cypress execution, use:

``` bash
npx cypress run
```

For interactive execution:

``` bash
npx cypress open
```

## Allure Report Output

After a successful headless execution, the main report is available at:

``` text
allure-report/index.html
```

Raw Allure result files are stored under:

``` text
allure-results/
```

The generated report can be opened with:

``` bash
npm run allure:open:report
```

## Troubleshooting

### `allure: command not found`

Use the project-local Allure Commandline:

``` bash
npx allure --version
```

If dependencies are missing, reinstall them:

``` bash
npm install
```

### No Allure Results

Make sure the Allure plugin is registered in `cypress.config.js`:

``` javascript
const allureWriter = require('@shelex/cypress-allure-plugin/writer');

setupNodeEvents(on, config) {
  allureWriter(on, config);
  return config;
}
```

### Old Results Appearing

The project automatically deletes:

``` text
allure-results/
allure-report/
```

at the beginning of a run. If necessary, remove them manually and
execute the tests again.

### Report Does Not Open Automatically

Generate the report and open it manually:

``` bash
npx allure generate allure-results --single-file --clean -o allure-report
npm run allure:open:report
```

## Recommended Workflow

For local development:

``` bash
npm install
npx cypress open
```

For CI/headless execution:

``` bash
npm install
npx cypress run
```

For manually opening the generated report:

``` bash
npm run allure:open:report
```

## CI/CD Integration

This project can be integrated into CI/CD platforms such as:

-   GitHub Actions
-   Jenkins
-   Azure DevOps
-   GitLab CI/CD

A typical pipeline can follow this pattern:

``` text
Checkout
   |
Install Dependencies
   |
Run Cypress Tests
   |
Generate Allure Results
   |
Generate Allure HTML
   |
Publish Allure Report
```

For CI environments, it is recommended to publish `allure-report/` as a
pipeline artifact or deploy the generated HTML to a report hosting
location.

## Why Allure?

Allure provides a rich test-reporting experience with useful information
such as:

-   Test status
-   Test duration
-   Test suites
-   Steps
-   Attachments
-   Request information
-   Execution history
-   Failure details
-   Categorization and trends

The official Allure JavaScript integrations include an `allure-mocha`
integration as well as Cypress support through the JavaScript ecosystem.
For new projects, prefer the currently maintained Allure JavaScript
integrations rather than the deprecated standalone
`mocha-allure-reporter` package.

## Useful Links

-   Repository: https://github.com/vinodkpasi/allurereport_mocha
-   Cypress: https://www.cypress.io/
-   Cypress Documentation: https://docs.cypress.io/
-   Allure Report: https://allurereport.org/
-   Allure JavaScript Integrations:
    https://github.com/allure-framework/allure-js
-   Cypress Allure Plugin:
    https://github.com/Shelex/cypress-allure-plugin

## Author

**Vinod Kumar**

QA / SDET / Test Automation Engineer

GitHub: https://github.com/vinodkpasi

## License

This project currently specifies the `ISC` license in `package.json`.
