# react-fetch-hook

[![Build Status](https://travis-ci.org/ilyalesik/react-fetch-hook.svg?branch=master)](https://travis-ci.org/ilyalesik/react-fetch-hook)
[![npm version](https://img.shields.io/npm/v/react-fetch-hook.svg)](https://www.npmjs.com/package/react-fetch-hook)
[![npm downloads](https://img.shields.io/npm/dt/react-fetch-hook.svg)](https://www.npmjs.com/package/react-fetch-hook)

React hook for conveniently use Fetch API.

* **Tiny** (391 B). Calculated by [size-limit](https://github.com/ai/size-limit)
* Both **Flow** and **TypeScript** types included

```javascript
import React from "react";
import useFetch from "react-fetch-hook";

const Component = () => {
  const { isLoading, data } = useFetch("https://swapi.co/api/people/1");

  return isLoading ? (
    <div>Loading...</div>
  ) : (
    <UserProfile {...data} />
  );
};

```

*useFetch* accepts the same arguments as *fetch* function.

## Installation

Install it with yarn:

```
yarn add react-fetch-hook
```

Or with npm:

```
npm i react-fetch-hook --save
```

## Features

### Custom formatter

Default is `response => response.json()` formatter. You can pass custom formatter:

```javascript
const { isLoading, data } = useFetch("https://swapi.co/api/people/1", {
    formatter: (response) => response.text()
});

```

### Depends
The request will not be called until all elements of `depends` array be truthy. Example:

```javascript
const {authToken} = useContext(authTokenContext);
const [someState, setSomeState] = useState(false);
const { isLoading, data } = useFetch("https://swapi.co/api/people/1", {
    depends: [!!authToken, someState] //don't call request, if haven't authToken and someState: false
});

```

### Re-call requests
If any element of `depends` changed, request will be re-call. For example, you can use [react-use-trigger](https://github.com/ilyalesik/react-use-trigger) for re-call the request:
```javascript
import createTrigger from "react-use-trigger";
import useTrigger from "react-use-trigger/useTrigger";

const requestTrigger = createTrigger();

export const Subscriber = () => {  
    const requestTriggerValue = useTrigger(requestTrigger);
    
    const { isLoading, data } = useFetch("https://swapi.co/api/people/1", {
        depends: [requestTriggerValue]
    });
  
    return <div />;
}

export const Sender = () => { 
    return <button onClick={() => {
        requestTrigger() // re-call request
    }}>Send</button>
}
```

### usePromise
For custom promised function.

```javascript
import React from "react";
import usePromise from "react-fetch-hook/usePromise";
import callPromise from "..."

const Component = () => {
  const { isLoading, data } = usePromise(() => callPromise(...params), [...params]);

  return isLoading ? (
    <div>Loading...</div>
  ) : (
    <UserProfile {...data} />
  );
};
```

### Paginated requests

```javascript
import React from "react";
import usePaginatedRequest from "react-fetch-hook/usePaginatedRequest";
import fetchSomeData from "..."

const Component = () => {
    const results = usePaginatedRequest(
        ({limit, offset}) => fetchSomeData(limit, offset),
        20
    );

    return (
        <InfiniteScroll
            pageStart={0}
            loadMore={results.loadMore}
            hasMore={results.hasMore}
            loader={<div />}>
            {results.data && results.data.map((item) => {
                ...
            })}
        </InfiniteScroll>)
}      
```



## API

### `useFetch`
Create a hook wrapper for `fetch` call. 
```javascript
useFetch(
    path: RequestInfo,
    options?: {
        ...RequestOptions,
        formatter?: Response => Promise
        depends?: Array<boolean>
    },
    specialOptions?: {
        depends?: Array<boolean>
    }
): TUseFetchResult
```
where `TUseFetchResult` is:
```javascript
{
    data: any,
    isLoading: boolean,
    error: any
}
```

 `RequestInfo`, `RequestOptions` is [fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) args.


### `usePromise`
```javascript
usePromise<T, I: $ReadOnlyArray<mixed>>(
    callFunction: ?(...args: I) => Promise<T>,
    ...inputs: I
): TUsePromiseResult<T>
```
where `TUsePromiseResult<T>` is
```javascript
type TUsePromiseResult<T> = {
    data: ?T,
    isLoading: boolean,
    error: mixed
}
```

### `usePaginatedRequest`
Create a paginated request. 
```javascript
usePaginatedRequest = <T>(
    request: (params: { limit: number, offset: number }) => Promise<Array<T>>,
    limit: number
): {
    data: Array<T>,
    loadMore?: () => mixed,
    hasMore: boolean
};
```

## Sponsored
<a href="https://lessmess.agency/?utm_source=react-fetch-hook">
  <img src="https://lessmess.agency/badges/sponsored_by_lessmess.svg"
       alt="Sponsored by Lessmess" width="236" height="54">
</a>
