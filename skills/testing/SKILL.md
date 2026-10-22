---
name: testing
description: Testing strategy and patterns with Vitest/Jest, Testing Library, Supertest, and Playwright. Use when writing or reviewing tests, setting coverage, or debugging flaky tests.
---

# Testing

## Pyramid
- **Unit (70%)** — pure logic, utils, services, hooks. Fast, isolated.
- **Integration (20%)** — API + DB, component + hooks. Use real DB (test container / in-memory) where practical.
- **E2E (10%)** — critical user journeys only (signup, login, checkout). Playwright.

## What must be tested
Auth, permissions, payments, data writes, validation, error paths, and any bug that was ever fixed (regression).

## Unit example (Vitest)
```ts
import { describe, it, expect, vi } from 'vitest';

describe('calculateDiscount', () => {
  it('returns 0 for orders below the threshold', () => {
    expect(calculateDiscount({ total: 49 })).toBe(0);
  });
  it('applies 10% at or above the threshold', () => {
    expect(calculateDiscount({ total: 100 })).toBe(10);
  });
  it('throws ValidationError for negative totals', () => {
    expect(() => calculateDiscount({ total: -1 })).toThrow(ValidationError);
  });
});
```

## Component example (Testing Library)
```tsx
it('shows an error and keeps input when save fails', async () => {
  server.use(http.post('/api/profile', () => HttpResponse.json({}, { status: 500 })));
  const user = userEvent.setup();
  render(<ProfileForm />);

  await user.type(screen.getByLabelText(/name/i), 'Akash');
  await user.click(screen.getByRole('button', { name: /save/i }));

  expect(await screen.findByRole('alert')).toHaveTextContent(/couldn't save/i);
  expect(screen.getByLabelText(/name/i)).toHaveValue('Akash');
});
```

## API example (Supertest)
```ts
it('returns 401 without a token', async () => {
  const res = await request(app).get('/api/me');
  expect(res.status).toBe(401);
  expect(res.body).toMatchObject({ success: false, error: { code: 'UNAUTHORIZED' } });
});
```

## E2E (Playwright)
```ts
test('user can sign in and see dashboard', async ({ page }) => {
  await page.goto('/login');
  await page.getByLabel('Email').fill(process.env.E2E_USER!);
  await page.getByLabel('Password').fill(process.env.E2E_PASS!);
  await page.getByRole('button', { name: 'Sign in' }).click();
  await expect(page.getByRole('heading', { name: 'Dashboard' })).toBeVisible();
});
```

## Rules
- Deterministic: mock time (`vi.useFakeTimers`), network (MSW), randomness.
- Isolated: reset mocks and DB state between tests.
- No arbitrary waits; use `findBy*`, `waitFor`, Playwright auto-wait.
- Coverage targets: 80% lines overall, 90%+ on critical modules. Coverage is a floor, not the goal.
- Flaky test = bug. Fix or quarantine with a ticket the same day.
- Test names describe behavior, not implementation.
- Accessibility: add `axe` checks to key component/e2e tests.

## Checklist
- [ ] Happy path, edge, error, permission cases
- [ ] Regression test for every bug fix
- [ ] No `.only` / `.skip` left behind
- [ ] Runs headless in CI under 5 minutes
