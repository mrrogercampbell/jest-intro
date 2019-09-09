# Testing Asynchronous Code

- When testing asynchronous JS code,  Jest has to know when the code it is testing has completed it functionality before it moves on to another test. 
  -  Jest has several ways to handle this.

## Callbacks
- This is the most common asynchronous pattern are `callbacks`.
- You have a function `fetchData(callback)` that fetches data and calls `callback(data)` when its complete. You want to test that this returned data is just the string `'peanut butter'`
- By default, Jest test complete they reach the end of their executions.
- Dont do the following:
```javascript
//Don't do this
test('the data is peanut butter', () => {
    function callback(data) {
        expect(data).toBe('peanut butter')
    }

    fetchData(callback)
})
```
- This test will complete as soon as `fetchData` completes, which meas you Jest never waited for the callback.
- Instead of putting the test in a function with an empty argument, use a single argument call `done`.
- When used Jest waits until the `done` callback is called and then finishes the test.

```javascript
test('the data is peanut butter', done => {
    function callback(data) {
        expect(data).toBe('peanut butter')
        done()
    }

    fetchData(callback)
})
```
- If done is never called the test will fail. This is a good thing.

## Promises
- Using promises, the simplest way to handle this is to return a promise from your test, and Jest will wait for the promise to resolve.
- If the promise is rejected the test will fail.
- Example:
  - Say `fetchData` returns a promise that is supposed to resolve to the string `peanut butter`.
  - Possible test: 
```javascript
test('the data is peanut butter', () => {
    expect.assertion(1)
    return fetchData().then(data => {
        expect(data).toBe('peanut butter')
    })
})
```
- Be you return the promise: if do not have the `return` statement your test will complete before `fetchData` completes.
- If the expected result is for the promise to be rejected use `.catch` method.
- You also have to add `expect.assertions` to verify that a certain number of assertions are called. If not, the fulfilled promise would not fair the test.
```javascript
test('the fetch fails with an error', () => {
    expect.assertions(1)
    return fetchData().catch(e => expect(e).toMatch('error'))
})
```

## `.resolve` | `.reject`
#### Available in Jest 20.00+

- If you use the `.resolve` matcher in your expect statement, Jest will wait for that promise to be resolved.
- If the promise is rejected, the test will fail.
```javascript
test('the data is peanut butter', () => {
    expect.assertions(1)
    return expect(fetchData()).resolves.toBe('peanut butter')
})
```

- Be sure to return the assertion.