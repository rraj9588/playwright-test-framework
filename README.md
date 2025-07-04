# Playwright-Python Based Test Framework

## Features
- Playwright browser automation (Python)
- Allure reporting
- Parallel test execution
- Modular, reusable login and utility functions
- Pytest fixtures for browser/page management

## Setup
1. Create and activate a virtual environment:
   ```bash
   python3 -m venv .venv
   source .venv/bin/activate
   ```
2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   playwright install
   ```

## Running Tests
- Run all tests in parallel with Allure reporting:
  ```bash
  pytest
  ```
- View Allure report:
  ```bash
  allure serve allure-results
  ```

## Project Structure
- `tests/` - All test cases and utilities
- `tests/utils.py` - Reusable helper functions (e.g., login)
- `conftest.py` - Pytest fixtures for browser/page
- `pytest.ini` - Pytest configuration (parallel, Allure, markers)
- `mcp/` — (Optional) MCP-related scripts, configs, or adapters for Model Context Protocol integration

## Adding Tests
- Place new test files in `tests/`.
- Use fixtures and helpers for consistency.

## Markers
- `@pytest.mark.login` - Login tests
- `@pytest.mark.smoke` - Smoke tests

## Advanced Topics

### Data-Driven and Parametrized Testing
- Use `pytest.mark.parametrize` for running tests with multiple data sets (see BOM and Concurrency examples).
- Combine with Allure steps and attachments for detailed reporting.

### Workflow Simulation
- Simulate business workflows in tests using Allure steps and attachments (see BOM workflow simulation example).

### Concurrency and Parallelism
- Use `pytest-xdist` for parallel execution: `pytest -n auto`.
- Write tests for concurrent users, race conditions, or resource contention (see Concurrency example file).

### UI and API Test Integration
- Combine Playwright UI automation and API validation in a single test for end-to-end coverage.

### Logging and Debugging
- Use the logging utility for detailed logs in both console and file.
- Attach logs or screenshots to Allure reports for failed steps.

### CI/CD Integration (Optional)
- Integrate with CI/CD tools (GitHub Actions, GitLab CI, Jenkins) for automated test runs.
- Publish Allure reports and Slack notifications in your pipeline.

### Test Data Management (Optional)
- Store test data in YAML, JSON, or CSV files and load dynamically in tests.
- Use fixtures for setup/teardown of test data.

### Environment Configuration (Optional)
- Add a `.env` file at the project root (see `.env.example`) and set environment variables for URLs, credentials, and secrets.
- Reference environment variables in your tests and helpers using `os.environ.get()` (see BOM and Concurrency example files).
- Example:
  ```python
  import os
  BASE_URL = os.environ.get('BASE_URL', 'https://example.com')
  ```
- Do not hardcode sensitive or environment-specific data in test files.

## Environment Variables & Secrets

This framework uses environment variables for all sensitive data (such as usernames and passwords). **Never hardcode credentials in code.**

### How to Set Credentials
- Locally: Set environment variables in your shell before running tests:
  ```bash
  export CHAMELEON_USER="your-username"
  export CHAMELEON_PASS="your-password"
  pytest
  ```
- Or, use a `.env` file (see below) and a tool like `python-dotenv`.
- In CI/CD: Set secrets in your pipeline’s environment variable configuration.

### Sample .env.example
```
CHAMELEON_URL=https://demo.chameleon.autodesk.com/
CHAMELEON_USER=your-username
CHAMELEON_PASS=your-password
```

> **Note:** Do not commit your real `.env` file or secrets to version control. Only commit `.env.example` as a template.

## Test Context Directory (`testcontext/`)
The `testcontext/` folder is used to store shared test context, data, or state that may be needed across multiple tests or by different teams. For example, you can place files like `webtestcontext.txt` or other context/configuration files here. This enables tests to:
- Share setup or state between UI, API, and workflow tests
- Store temporary data or artifacts needed for multi-step or cross-team scenarios
- Keep context files out of the main test directories for better organization

> **Best Practice:** Clean up or version-control only essential context files. Avoid storing sensitive data here.

## Model Context Protocol (MCP) Integration
MCP (Model Context Protocol) is a standard for sharing and synchronizing context between different automation tools, test runners, or systems. In this framework, MCP can be leveraged to:
- Enable advanced integrations with other tools or platforms that support MCP
- Share test state, environment, or results in a standardized way
- Facilitate distributed or cross-language automation scenarios

If you plan to use MCP:
- Add your MCP-related scripts, configs, or adapters in a dedicated folder (e.g., `mcp/`)
- Document usage and integration steps in the README or a separate MCP guide
- Ensure any MCP dependencies are listed in `requirements.txt`

> **Note:** MCP integration is optional and only needed for advanced or cross-platform scenarios. The framework works out of the box for most UI/API automation needs.

## Playwright MCP (Model Context Protocol) Usage (Advanced)

Playwright's Model Context Protocol (MCP) is an advanced feature for sharing browser and test context between tools and processes.

- MCP is typically used in large-scale, distributed, or cross-language automation scenarios.
- Python Playwright does not natively expose MCP APIs, but you can use Playwright's CLI or Node.js server to interact with MCP endpoints if needed.
- For most UI/API automation, you do not need to configure MCP.

### How to Use Playwright MCP (Advanced)
1. Refer to the official Playwright documentation: https://playwright.dev/docs/mcp
2. Use the Playwright CLI or Node.js server to start an MCP endpoint.
3. Connect your Python tests to the MCP endpoint using Playwright's remote browser capabilities.

**Example (Node.js MCP server):**
```bash
npx playwright mcp-server --port=9000
```

**Example (Python connect to MCP):**
```python
from playwright.sync_api import sync_playwright
with sync_playwright() as p:
    browser = p.chromium.connect_over_cdp('ws://localhost:9000')
    # ...run tests using the remote browser...
```

> Note: This is an advanced feature and not required for most test automation workflows.

## Moving the Framework to Git

1. **Initialize a Git repository (if not already done):**
   ```bash
   git init
   ```
2. **Add all files to the repository:**
   ```bash
   git add .
   ```
3. **Commit your changes:**
   ```bash
   git commit -m "Initial commit: Playwright Python automation framework"
   ```
4. **Create a new repository on GitHub, GitLab, or your preferred Git hosting service.**
5. **Add the remote origin (replace `<your-repo-url>` with your actual repo URL):**
   ```bash
   git remote add origin <your-repo-url>
   ```
6. **Push your code to the remote repository:**
   ```bash
   git push -u origin main
   ```
   (If your default branch is `master`, use `master` instead of `main`.)

7. **(Optional) Add a `.gitignore` file** to exclude files like `.env`, `__pycache__/`, `allure-results/`, `.venv/`, etc.

Example `.gitignore`:
```
.env
.venv/
__pycache__/
allure-results/
*.pyc
archive/
```

After these steps, your framework will be version-controlled and available on your Git hosting platform.

## CI/CD with Jenkins

A sample `Jenkinsfile` is provided for automated test execution:
- **Setup**: Installs dependencies and Playwright browsers in a virtual environment.
- **Smoke Tests**: Runs all tests marked with `@pytest.mark.smoke`, generates Allure and JUnit XML reports, and archives results.
- **E2E Tests**: Runs all tests marked with `@pytest.mark.e2e`, generates Allure and JUnit XML reports, and archives results.
- **API Tests**: Runs all tests marked with `@pytest.mark.api`, generates Allure and JUnit XML reports, and archives results.
- **Test Results**: Jenkins publishes JUnit XML results for all stages.
- **Allure Reports**: Allure results are published if the Allure Jenkins plugin is installed.
- **Cleanup**: Cleans up virtual environment and result folders after the build.
- **Slack Notifications**: Sends build status notifications to Slack channel `#test-result` (requires Slack Jenkins plugin and configuration).

Example pipeline stages:
```groovy
stage('Smoke Tests') {
    steps {
        sh 'source $VENV_DIR/bin/activate && pytest -m smoke --alluredir=allure-results-smoke --junitxml=allure-results-smoke/junit-smoke.xml'
    }
}
stage('E2E Tests') {
    steps {
        sh 'source $VENV_DIR/bin/activate && pytest -m e2e --alluredir=allure-results-e2e --junitxml=allure-results-e2e/junit-e2e.xml'
    }
}
stage('API Tests') {
    steps {
        sh 'source $VENV_DIR/bin/activate && pytest -m api --alluredir=allure-results-api --junitxml=allure-results-api/junit-api.xml'
    }
}
post {
    always {
        junit 'allure-results-*/junit-*.xml'
        allure([
            includeProperties: false,
            jdk: '',
            results: [[path: 'allure-results-smoke']],
            reportBuildPolicy: 'ALWAYS',
            results: [[path: 'allure-results-e2e']],
            results: [[path: 'allure-results-api']]
        ])
    }
    cleanup {
        sh 'rm -rf $VENV_DIR allure-results-*'
    }
    success {
        slackSend(channel: '#ci-cd', color: 'good', message: "Build SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}")
    }
    failure {
        slackSend(channel: '#ci-cd', color: 'danger', message: "Build FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}")
    }
}
```
