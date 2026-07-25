```markdown
# WEB-Sekolah Development Patterns

> Auto-generated skill from repository analysis

## Overview
This skill introduces the core development patterns and conventions used in the WEB-Sekolah TypeScript codebase. It covers file naming, import/export styles, commit message patterns, and testing structure. By following these guidelines, contributors can ensure consistency and maintainability across the project.

## Coding Conventions

### File Naming
- Use **snake_case** for all file names.
  - Example:  
    ```
    user_profile.ts
    dashboard_controller.ts
    ```

### Import Style
- Use **relative imports** for referencing modules within the project.
  - Example:
    ```typescript
    import { getUser } from './user_service';
    ```

### Export Style
- Use **named exports** for all modules.
  - Example:
    ```typescript
    // In user_service.ts
    export function getUser(id: string) { /* ... */ }
    export const USER_ROLE = 'admin';
    ```

### Commit Messages
- Commit messages are **freeform** (no enforced prefix or format).
- Typical length: ~26 characters.
  - Example:
    ```
    add user authentication logic
    ```

## Workflows

### Adding a New Module
**Trigger:** When creating a new feature or utility module  
**Command:** `/add-module`

1. Create a new file using snake_case naming (e.g., `feature_name.ts`).
2. Use named exports for all functions, constants, or classes.
3. Use relative imports to include dependencies from other modules.
4. Write corresponding tests in a file named `feature_name.test.ts`.

### Writing Tests
**Trigger:** When adding or updating code that requires testing  
**Command:** `/write-test`

1. Create a test file with the pattern `*.test.ts` (e.g., `user_service.test.ts`).
2. Write tests using the project's chosen (unknown) testing framework.
3. Use relative imports to bring in the module under test.
4. Ensure all named exports are covered by tests.

## Testing Patterns

- Test files follow the pattern: `*.test.ts`
- The specific testing framework is not detected, but tests should be written in TypeScript and placed alongside or near the modules they test.
- Example test file structure:
  ```typescript
  // user_service.test.ts
  import { getUser } from './user_service';

  describe('getUser', () => {
    it('returns user data for valid id', () => {
      // test implementation
    });
  });
  ```

## Commands
| Command        | Purpose                                    |
|----------------|--------------------------------------------|
| /add-module    | Scaffold a new module with conventions     |
| /write-test    | Create a new test file for a module        |
```
