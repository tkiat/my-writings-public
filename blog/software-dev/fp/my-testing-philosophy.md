# My Testing Philosophy

(Many) developers are attracted to testing because not only it is an automated activity but also quality assurance. There are different kinds of tests like static type testing, unit testing, integration testing, and end-to-end testing. I am no expert in testing but after learning some Haskell testing libraries I have formed the testing philosophy that makes sense to me.

## Motivation

I once tried to aim for 100% coverage because that is what somewhere on the internet told me to do but I later found out that it is not very useful. Someone can game the system by simulating all lines of code in the component and simply do a trivial check so 100% coverage is always not proof. Plus, testing for side effects can be painful especially in the functional programming languages that are very picky with the type (good luck mocking!).

Furthermore, achieving a strict 100% test coverage can break your tests very often when you change the code. And you have to understand the test to correct the test. This problem is even severe in a larger codebase. I can imagine achieving 100% test coverage is fine for the corporate because a manager probably likes that number.

## Shift All Complexities from Side Effects to Pure Functions

This is the best advice I have found. Compared with the side-effect tests, it is trivial to test pure functions as I don't have to use any mock. This also makes side-effect functions as simple as possible. Ideally, we should strive to make the side-effect functions as obvious as possible so there is no need to test at all.

Let's say I have this Javascript function.

```javascript
export const myFunc = p => {
  const [p1, p2] = window.location.pathname.split('/').filter(x => x !== '')
  p.whatever = p1
  return p
}
```

The side-effects are `window` object and the line where we mutate `p`. Let's shift it outside.

```typescript
export const myFunc = (p: Path,
                       urlPath: string = window.location.pathname):
                       Path => {
  const [p1, p2] = urlPath.split('/').filter(x => x !== '')

  const temp = JSON.parse(JSON.stringify(p)) as Path
  temp.whatever = p1
  return temp
}
```

The new function is more complex because it has to deep copy the object `p`. What I get in return is a nice pure function. I can then reason about code more easily and also have an easier time writing tests as there is no need to mock the `window` object.

### My Opinions About TDD

There is no need to use TDD if we don't test side effects. I usually write the code first, then make side-effect functions as simple as possible, and finally test only pure functions.

If 100% coverage is the requirement then TDD seems nice to have in the imperative language. A functional language forces you to design code well anyway so there is much less need for TDD.

### I Always Favor Property-Based Over Unit Test

For the obvious reason that more samples are better.
