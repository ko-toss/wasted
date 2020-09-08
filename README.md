# wasted
Unexpected things that cost me time.

## RxJS
### Passing async function directly to `swtichMap` causes unsubscription on error.
##### NoobLevel: 1
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


### TypesScript checks node_modules 
##### NoobLevel: 9
#### Event
`tsc --noEmit` spits some crazy errors, something like below;
```
node_modules/rxjs/src/internal/Subscription.ts:67:5 - error TS2322: Type 'null' is not assignable to type 'SubscriptionLike[]'.

67     this._subscriptions = null;
       ~~~~~~~~~~~~~~~~~~~

node_modules/rxjs/src/internal/Subscription.ts:108:22 - error TS2454: Variable 'errors' is used before being assigned.

108             errors = errors || [];
                         ~~~~~~

.... LOT MORE LOGS OMITTED ....

node_modules/rxjs/src/internal/util/toSubscriber.ts:17:14 - error TS7053: Element implicitly has an 'any' type because expression of type 'string | symbol' can't be used to index type 'NextObserver<T> | ErrorObserver<T> | CompletionObserver<T> | ((value: T) => void)'.
  No index signature with a parameter of type 'string' was found on type 'NextObserver<T> | ErrorObserver<T> | CompletionObserver<T> | ((value: T) => void)'.

17       return nextOrObserver[rxSubscriberSymbol]();
                ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
```
#### Reason
Imported node_modules uncompiled src directory. eg `import { ObservableInput } from 'rxjs/src/internal/types';`
Or rather relied on IDE's auto import (probably a bug).

#### Fix
Import from compiled source. eg `import {ObservableInput} from 'rxjs'`

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
