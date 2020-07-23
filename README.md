# wasted
Unexpected things that cost me time.

## RxJS
### Passing async function directly to `swtichMap` causes unsubscription on error.
#### Event
If below code errors once the subscription to response$ gets lost, even with `catchError`.
```ts
const response$ = paramsAndState$.pipe(
  switchMap(async ([param, state]) => {
    const data = await request(param);
    return produce(state, draft => {
      draft.status = 'SUCCESS';
      draft.error = null;
      draft.data = castDraft(data);
    });
  }),
  catchError(err => handleError(err, state$))
);
```
#### Reason
"The problem is that async causes switchMap to return an Observable of whatever you return. So you returned an `Observable<Observable<never>>` which then emitted an `Observable<never>` to your success callback." - https://stackoverflow.com/a/56148440 -

#### Fix
Don't pass `async` function to switchMap directly, always pass a normal function and return promise, iterable, observable, etc.

#### Reference
https://stackoverflow.com/a/56148440


## Components/Elements
#### Event
`<meta charset="utf-8" />` causes `TS2322: Type 'string' is not assignable to type 'T'` error in React

#### Fix
use `<meta httpEquiv="Content-Type" content="text/html; charset=utf-8" />`



## Config
#### Event
`Parsing error: "parserOptions.project" has been set for @typescript-eslint/parser.`

#### Fix
```
# tsconfig.json
"include": [
    ".eslintrc.js",
 ]
```

#### Reference
https://stackoverflow.com/a/61993196
