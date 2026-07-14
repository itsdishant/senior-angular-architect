# ESLint Configuration Standards

Use this when an agent needs to recommend or explain baseline Angular linting rules. Keep the JSON template in `templates/eslint-config.json` as the machine-usable copy.

## Recommended Rules

```json
{
  "rules": {
    "@angular-eslint/component-selector": [
      "error",
      {
        "type": "element",
        "prefix": "app"
      }
    ],
    "@angular-eslint/no-empty-lifecycle-method": "error",
    "@angular-eslint/use-component-selector": "error",
    "@angular-eslint/use-lifecycle-interface": "error",
    "@typescript-eslint/explicit-function-return-type": [
      "warn",
      {
        "allowExpressions": true
      }
    ],
    "@typescript-eslint/no-explicit-any": "error",
    "@typescript-eslint/no-unused-vars": [
      "error",
      {
        "argsIgnorePattern": "^_"
      }
    ],
    "rxjs/no-ignored-subscription": "error",
    "rxjs/no-subclass": "error",
    "rxjs/no-unused": "error"
  }
}
```

## Purpose

- Enforce Angular selector and lifecycle consistency.
- Prevent ignored RxJS subscriptions and unsafe RxJS inheritance patterns.
- Discourage `any` and unused code before it reaches review.
- Encourage explicit return types on exported or non-trivial functions.
