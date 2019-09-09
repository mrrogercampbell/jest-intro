# Using Matchers
{{ Put a brief description here }}
## Common Matchers:
    - Simplest way to test a value is with exact equality

```javascript
test('two plus two is four', () => {
  expect(2 + 2).toBe(4);
});
```

- `expect(2 +2)` returns an "expectation" object.
  - You call matcher on expectation objects.
- `.toBe(4)` is the matcher in this example.
- On run, Jest tracks all the failing matches and prints out a nice error message
- `toBe` uses `Object.is` to test exact equality
- To check the value of an object, use `toEqual`:
```javascript
test('object assignment', () => {
    const data = {one: 1}
    data['two'] = 2
    expect(data).toEqual({one: 1, two: 2})
})
```

- `toEqual` recursively check every field of an object or array
- To test for the opposite of a matcher:
```javascript
test('adding positive numbers is not zero', () => {
    for(let a =1; a < 10; a++){
        for(let b = 1; b < 10; b++){
            expect(a +b).not.toBe(0)
        }
    }
})
```

## Truthiness

- You are able to distinguish between `undefinded`, `null`, and `false`; by using:
  - `toBeNull` matches only `null`
  - `toBeUndefined` matches only `undefined`
  - `toBeDefined` is the opposite of `toBeUndefined`
  - `toBeTruthy` matches anything that an `if` statement treats as true
  - `toBeFalsy` matches anything that an `if` statement treats as false
- Example:
```javascript
test('null', () => {
  const n = null;
  expect(n).toBeNull();
  expect(n).toBeDefined();
  expect(n).not.toBeUndefined();
  expect(n).not.toBeTruthy();
  expect(n).toBeFalsy();
});

test('zero', () => {
  const z = 0;
  expect(z).not.toBeNull();
  expect(z).toBeDefined();
  expect(z).not.toBeUndefined();
  expect(z).not.toBeTruthy();
  expect(z).toBeFalsy();
});
```

- Use the matcher that most precisely corresponds to what you want your code to be doing. 

## Numbers
Most ways of comparing numbers have matchers equivalents.
```javascript
tet('two plus two', () =>{
    const value = 2 + 2
    expect(value).toBeGreaterThan(3)
    expect(value).toBeGreaterThanOrEqual(3.5)
    expect(value).toBeLessThan(5)
    expect(value).toBeLessThanOrEqual(4.5)

    //tpBe and ToEqual are equivalent for numbers
    expect(value).toBe(4)
    expect(value).toEqual(4)
})
```

- For floating point equality, use `toBeCloseTo` instead of `toEqual`, if not your test could fail due to rounding errors.
```javascript
test('adding floating point numbers', () => {
    const value = 0.1 + 0.2
    //expect(value).toBe(0.3)         This won't work because of rounding error
    expect(value).toBeCloseTo(0.3)  //This will work
})
```

## Strings
-Check strings against regular expressions with `toMatch`:

```javascript
test('there is no I in team', () => {
    expect('team').not.toMatch(/I/)
})

test('but there is a "stop" in Christoph', () => {
    expect('Christoph').toMatch(/stop/)
})
```

## Arrays and Iterables
- To check if an array or iterable contains a particular item use `toContain`:
```javascript
const shoppingList = [
    'diapers',
    'kleenex',
    'trash bags',
    'paper towels',
    'beer'
]

test('the shoping list has beer on it', () => {
    expect(shoppingList).toContain('beer')
    expect(new Set(shoppingList)).toContain('beer')
})
```

## Exceptions
- To check if a function throws an error when called use `toThrow`:

```javascript
function compileAndroidCode() {
    throw new ConfigError('you are using the wrong JDK')
}

test('compiling android goes as expected', () => {
    expect(compileAndroidCode).toThrow()
    expect(compileAndroidCode).toThrow(ConfigError)

    // You can also use the exact error message or a regexp
    expect(compileAndroidCode).toThrow('you are using the wrong JDK')
    expect(compileAndroidCode).toThrow
})
```