# Testing Reference

Complete testing guidelines. Use this as your single source of truth for writing tests.

## Test Structure: Given-When-Then Pattern

All tests MUST follow the Given-When-Then pattern to improve readability and maintainability. This pattern structures tests into three explicit sections separated by blank lines:

### Pattern Structure

1. **Given** (Setup): Initial context, preconditions, test data
2. **When** (Action): The specific behavior being tested
3. **Then** (Assertions): Expected outcomes and verifications

### Important: No Comments

Do NOT add `// Given`, `// When`, `// Then` comments in the code. The structure should be clear from blank lines and code organization alone.

### Good: Clear Given-When-Then Structure

```javascript
describe('ShoppingCart', () => {
  it('should add item to cart when addItem is called', () => {
    const cart = new ShoppingCart();
    const item = { id: '1', name: 'Product', price: 29.99 };

    cart.addItem(item);

    expect(cart.items).toHaveLength(1);
    expect(cart.total).toBe(29.99);
  });
});
```

```python
def test_add_item_to_cart():
    cart = ShoppingCart()
    item = Product(id='1', name='Product', price=29.99)

    cart.add_item(item)

    assert len(cart.items) == 1
    assert cart.total == 29.99
```

### Bad: No Clear Structure

```javascript
it('should add item to cart when addItem is called', () => {
  const cart = new ShoppingCart();
  const item = { id: '1', name: 'Product', price: 29.99 };
  cart.addItem(item);
  expect(cart.items).toHaveLength(1);
  expect(cart.total).toBe(29.99);
});
```

### Bad: Unnecessary Comments

```javascript
it('should add item to cart when addItem is called', () => {
  // Given
  const cart = new ShoppingCart();
  const item = { id: '1', name: 'Product', price: 29.99 };

  // When
  cart.addItem(item);

  // Then
  expect(cart.items).toHaveLength(1);
});
```

## Test Naming Conventions

### Format

Test names should be clear, descriptive sentences that explain the expected behavior:

```
it('should [expected behavior] when [condition]', () => {
  // Test implementation
});
```

### Good Examples

```javascript
it('should calculate total price when multiple items are added', () => { ... });
it('should throw error when adding item with negative price', () => { ... });
it('should return empty list when no results match filter', () => { ... });
it('should redirect to login page when user is not authenticated', () => { ... });
```

### Bad Examples

```javascript
it('test addItem', () => { ... });
it('works correctly', () => { ... });
it('should work', () => { ... });
it('adds things', () => { ... });
```

## Test Organization

### Nested Describe Blocks

Use nested `describe` blocks to organize related tests:

```javascript
describe('ShoppingCart', () => {
  describe('adding items', () => {
    it('should add item to empty cart', () => { ... });
    it('should increment quantity when adding duplicate item', () => { ... });
  });

  describe('removing items', () => {
    it('should remove item completely', () => { ... });
    it('should decrement quantity when removing partial amount', () => { ... });
  });

  describe('calculating totals', () => {
    it('should sum all item prices', () => { ... });
    it('should apply discount when applicable', () => { ... });
  });
});
```

```python
class TestShoppingCart:
    class TestAddingItems:
        def test_add_item_to_empty_cart(self):
            ...

        def test_increment_quantity_when_adding_duplicate(self):
            ...

    class TestRemovingItems:
        def test_remove_item_completely(self):
            ...
```

## Test Data Management

### Create Meaningful Test Data

Test data should be:
- **Realistic**: Represent actual use cases
- **Minimal**: Only include fields relevant to the test
- **Descriptive**: Use clear names that indicate purpose

```javascript
describe('User filtering', () => {
  it('should filter users by role', () => {
    const users = [
      { id: '1', name: 'Admin User', role: 'admin' },
      { id: '2', name: 'Regular User', role: 'user' },
      { id: '3', name: 'Guest User', role: 'guest' },
    ];

    const admins = filterByRole(users, 'admin');

    expect(admins).toHaveLength(1);
    expect(admins[0].name).toBe('Admin User');
  });
});
```

### Builder Pattern for Test Data

For complex or repetitive test data, use the Builder pattern with sensible defaults:

```typescript
class User {
  constructor(
    public readonly id: string,
    public readonly name: string,
    public readonly email: string,
    public readonly preferences: Record<string, unknown>
  ) {}
}

class UserBuilder {
  private id: string = '1';
  private name: string = 'Test User';
  private email: string = 'test@example.com';
  private preferences: Record<string, unknown> = {};

  withId(id: string): UserBuilder {
    this.id = id;
    return this;
  }

  withName(name: string): UserBuilder {
    this.name = name;
    return this;
  }

  withEmail(email: string): UserBuilder {
    this.email = email;
    return this;
  }

  withPreferences(preferences: Record<string, unknown>): UserBuilder {
    this.preferences = preferences;
    return this;
  }

  build(): User {
    return new User(
      this.id,
      this.name,
      this.email,
      this.preferences
    );
  }
}

it('should enable dark mode when user preference is set', () => {
  const user = new UserBuilder()
    .withPreferences({ theme: 'dark', notifications: true })
    .build();

  const isDarkMode = checkThemePreference(user);

  expect(isDarkMode).toBe(true);
});
```

```python
class User:
    def __init__(self, id: str, name: str, email: str, preferences: dict):
        self.id = id
        self.name = name
        self.email = email
        self.preferences = preferences

class UserBuilder:
    def __init__(self):
        self.id = '1'
        self.name = 'Test User'
        self.email = 'test@example.com'
        self.preferences = {}

    def with_id(self, id: str):
        self.id = id
        return self

    def with_name(self, name: str):
        self.name = name
        return self

    def with_email(self, email: str):
        self.email = email
        return self

    def with_preferences(self, preferences: dict):
        self.preferences = preferences
        return self

    def build(self) -> User:
        return User(
            id=self.id,
            name=self.name,
            email=self.email,
            preferences=self.preferences
        )

def test_enable_dark_mode_when_preference_set():
    user = (UserBuilder()
        .with_preferences({'theme': 'dark', 'notifications': True})
        .build())

    is_dark_mode = check_theme_preference(user)

    assert is_dark_mode
```

### Check for Existing Builders

**Before creating test data, ALWAYS check if builders already exist:**
- Look for builder classes in test utilities or helpers (often in `tests/builders/` or similar)
- Check existing tests for reusable builder patterns
- Review test setup files for available test data builders
- When builders exist, use them - they provide consistency and maintainability across all tests


## Summary

- **Always use Given-When-Then structure** with blank line separation
- **Never add Given/When/Then comments** in test code
- **Write descriptive test names** that explain expected behavior
- **Keep tests focused** on single behaviors
- **Use test data builders** for complex objects
- **Test both positive and negative cases**
- **Make tests independent** and runnable in any order
