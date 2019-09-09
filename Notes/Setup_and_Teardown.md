# Setup and Teardown
- Jest has built in functions that allow you to streamline any setup work that might need to be preformed being running your actually test.

## Repeating Setup For Many Tests
- When you have work that needs to be done repeatedly for many test you can use `beforeEach` and `afterEach`,
-  Say you have several test that interact with a database of cities.
   -  The method `initializeCityDatabase()` must be called before each test, and a method `clearCityDatabase()` must be called after each test. 
```javascript
beforeEach(() => {
    initializeCityDatabase()
})

afterEach(() => {
    clearCityDatabase()
})

test('city database has Vienna', () => {
    expect(isCity('Vienna')).toBeTruthy()
})

test('city database has San Juan', () => {
    expect(isCity('San Juan')).toBeTruthy()
})
```

`beforeEach` and `afterEach` can handle asynchronous code in the same ways that test can; they can either take a `done` parameter or return a promise that resolved when the database was initialized, we would want to return that promise:

```javascript
beforeEach(() => {
    return initializeCityDatabase()
})
```

## One-Time Setup

- In cases where you only need to setup once and you cant handle this inline; You can use `beforeAll` and `afterAll`.
- Example:
```javascript
beforeAll(() => {
    return initializeCityDatabase()
})

afterAll(() => {
    return clearCityDatabase()
})

test('city database has Vienna', () => {
    expect(isCity('Vienna')).toBeTruthy()
})

test('city database has San Juan', () => {
    expect(isCity('San Juan')).toBeTruthy()
})
```

## Scoping
- `before` and `after` blocks apply to every test in the file by default.
- Instead to keep code clean, group your test together in a `describe`.
  - When utilizing a `describe` block, `before` and `after` blocks only apply to test within the `describe`.
- Example; Say you have a city database and a food database:

```javascript
// Applies to all test in this file
beforeEach(() => {
    return initializeCityDatabase()
})

test('city database has Vienna', () => {
    expect(isCity('Vienna')).toBeTruthy()
})

test('city database has San Juan', () => {
    expect(isCity('San Juan')).toBeTruthy()
})

describe('matching cities to foods', () => {
  // Applies only to tests in this describe block
  beforeEach(() => {
    return initializeFoodDatabase()
  })

    test('Vienna <3 sausage', () => {
        expect(isValidCityFoodPair('Vienna', 'Wiener Schnitzel')).toBe(true)
    })

    test('San Juan <3 plantains', () => {
        expect(isValidCityFoodPair('San Juan', 'Mofongo')).toBe(true)
    })
})
```
- The top-level `beforeEach` is executed before the `describe` block.
- Example of order of execution of all hooks:
```javascript
beforeAll(() => console.log('1 - beforeAll'))

afterAll(() => console.log('1 - afterAll'))

beforeEach(() => console.log('1 - beforeEach'))

afterEach(() => console.log('1 - afterEach'))

test('', () => console.log('1 - test'))

describe('Scoped / Nested block', () => {
  beforeAll(() => console.log('2 - beforeAll'))
  afterAll(() => console.log('2 - afterAll'))
  beforeEach(() => console.log('2 - beforeEach'))
  afterEach(() => console.log('2 - afterEach'))
  test('', () => console.log('2 - test'))
});

// 1 - beforeAll
// 1 - beforeEach
// 1 - test
// 1 - afterEach
// 2 - beforeAll
// 1 - beforeEach
// 2 - beforeEach
// 2 - test
// 2 - afterEach
// 1 - afterEach
// 2 - afterAll
// 1 - afterAll
```

## Order of Execution of Describe and Test Blocks
- *Before* Jest executes any actual test, it executes all describe handlers first.
- Because of this you should do setup and teardown inside `before*` and `after*` rather than inside the describe block.
- By default, Jest runs all test serially in the order they were encountered, **only after** it has completed executing the describe blocks.
- It waits for each collection phase to finish and be cleared up before moving on.
- Example:
```javascript
describe('outer', () => {
  console.log('describe outer-a');

  describe('describe inner 1', () => {
    console.log('describe inner 1');
    test('test 1', () => {
      console.log('test for describe inner 1');
      expect(true).toEqual(true);
    });
  });

  console.log('describe outer-b');

  test('test 1', () => {
    console.log('test for describe outer');
    expect(true).toEqual(true);
  });

  describe('describe inner 2', () => {
    console.log('describe inner 2');
    test('test for describe inner 2', () => {
      console.log('test for describe inner 2');
      expect(false).toEqual(false);
    });
  });

  console.log('describe outer-c');
});

// describe outer-a
// describe inner 1
// describe outer-b
// describe inner 2
// describe outer-c
// test for describe inner 1
// test for describe outer
// test for describe inner 2
```

## General Advice
- When a test is failing, first thing to check is whether the test is failing when it's the only test being ran.
- To run only one test temporarily change `test` command to `test.only`:

```javascript
test.only('this will be the only test that runs', () => {
    expect(true).toBe(false)
})

test('this test will not run', () => {
    expect('A').toBe('A')
})
```

-When a test is only failing when part of a larger suite of test, but doesn't fail on its own, its most likely something from one of the other test that is causing the issue.
- One fix is to clear some shared state with `beforeEach`.
- Not sure what shared state is being modified?
  - Try `beforeEach` that only logs data.