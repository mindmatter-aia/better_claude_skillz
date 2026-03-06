---
name: e2e
description: Generate and run end-to-end tests with Playwright. Creates test journeys, runs tests, captures screenshots/videos/traces, and uploads artifacts.
---

# E2E Command

This command invokes the **e2e-runner** agent to generate, maintain, and execute end-to-end tests using Playwright.

## What This Command Does

1. **Generate Test Journeys** - Create Playwright tests for user flows
2. **Run E2E Tests** - Execute tests across browsers
3. **Capture Artifacts** - Screenshots, videos, traces on failures
4. **Upload Results** - HTML reports and JUnit XML
5. **Identify Flaky Tests** - Quarantine unstable tests

## When to Use

Use `/e2e` when:
- Testing critical user journeys (login, checkout, payments)
- Verifying multi-step flows work end-to-end
- Testing UI interactions and navigation
- Validating integration between frontend and backend
- Preparing for production deployment

## How It Works

The e2e-runner agent will:

1. **Analyze user flow** and identify test scenarios
2. **Generate Playwright test** using Page Object Model pattern
3. **Run tests** across multiple browsers (Chrome, Firefox, Safari)
4. **Capture failures** with screenshots, videos, and traces
5. **Generate report** with results and artifacts
6. **Identify flaky tests** and recommend fixes

## Example Usage

```
User: /e2e Test the product search and view flow

Agent (e2e-runner):
# E2E Test Generation: Product Search and View Flow

## Test Scenario Identified

**User Journey:** Product Search -> View Results -> Click Product -> View Details

**Test Coverage:**
1. Navigate to products page
2. Perform search
3. Verify search results
4. Click on first result
5. Verify product details page loads
6. Verify content renders

## Generated Test Code

```typescript
// tests/e2e/products/search-and-view.spec.ts
import { test, expect } from '@playwright/test'
import { ProductsPage } from '../../pages/ProductsPage'
import { ProductDetailsPage } from '../../pages/ProductDetailsPage'

test.describe('Product Search and View Flow', () => {
  test('user can search products and view details', async ({ page }) => {
    const productsPage = new ProductsPage(page)
    await productsPage.goto()
    await expect(page).toHaveTitle(/Products/)
    await productsPage.searchProducts('electronics')
    await page.waitForResponse(resp =>
      resp.url().includes('/api/products/search') && resp.status() === 200
    )
    const productCards = productsPage.productCards
    await expect(productCards.first()).toBeVisible()
    const firstProductTitle = await productCards.first().textContent()
    await productCards.first().click()
    await expect(page).toHaveURL(/\/products\/[a-z0-9-]+/)
    const detailsPage = new ProductDetailsPage(page)
    await expect(detailsPage.productName).toBeVisible()
    await expect(detailsPage.contentSection).toBeVisible()
  })
})
```
```

## Test Artifacts

When tests run, the following artifacts are captured:

**On All Tests:**
- HTML Report with timeline and results
- JUnit XML for CI integration

**On Failure Only:**
- Screenshot of the failing state
- Video recording of the test
- Trace file for debugging (step-by-step replay)
- Network logs
- Console logs

## Viewing Artifacts

```bash
# View HTML report in browser
npx playwright show-report

# View specific trace file
npx playwright show-trace artifacts/trace-abc123.zip

# Screenshots are saved in artifacts/ directory
open artifacts/search-results.png
```

## Flaky Test Detection

If a test fails intermittently:

```
FLAKY TEST DETECTED: tests/e2e/products/checkout.spec.ts

Test passed 7/10 runs (70% pass rate)

Common failure:
"Timeout waiting for element '[data-testid="confirm-btn"]'"

Recommended fixes:
1. Add explicit wait: await page.waitForSelector('[data-testid="confirm-btn"]')
2. Increase timeout: { timeout: 10000 }
3. Check for race conditions in component
4. Verify element is not hidden by animation

Quarantine recommendation: Mark as test.fixme() until fixed
```

## Browser Configuration

Tests run on multiple browsers by default:
- Chromium (Desktop Chrome)
- Firefox (Desktop)
- WebKit (Desktop Safari)
- Mobile Chrome (optional)

Configure in `playwright.config.ts` to adjust browsers.

## CI/CD Integration

Add to your CI pipeline:

```yaml
# .github/workflows/e2e.yml
- name: Install Playwright
  run: npx playwright install --with-deps

- name: Run E2E tests
  run: npx playwright test

- name: Upload artifacts
  if: always()
  uses: actions/upload-artifact@v3
  with:
    name: playwright-report
    path: playwright-report/
```

## Best Practices

**DO:**
- Use Page Object Model for maintainability
- Use data-testid attributes for selectors
- Wait for API responses, not arbitrary timeouts
- Test critical user journeys end-to-end
- Run tests before merging to main
- Review artifacts when tests fail

**DON'T:**
- Use brittle selectors (CSS classes can change)
- Test implementation details
- Run tests against production
- Ignore flaky tests
- Skip artifact review on failures
- Test every edge case with E2E (use unit tests)

## Integration with Other Commands

- Use `/plan` to identify critical journeys to test
- Use `/tdd` for unit tests (faster, more granular)
- Use `/e2e` for integration and user journey tests
- Use `/code-review` to verify test quality

## Related Agents

This command invokes the `e2e-runner` agent located at:
`~/.claude/agents/e2e-runner.md`

## Quick Commands

```bash
# Run all E2E tests
npx playwright test

# Run specific test file
npx playwright test tests/e2e/products/search.spec.ts

# Run in headed mode (see browser)
npx playwright test --headed

# Debug test
npx playwright test --debug

# Generate test code
npx playwright codegen http://localhost:3000

# View report
npx playwright show-report
```
