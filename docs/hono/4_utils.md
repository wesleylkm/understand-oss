# Util function

### getPathFromURL

1. find index of symbol `?`.
2. get url substring(8, url.length or queryIndex)

> 8 can get thing after the first 2 slash (**/** for both http & https case).

### splitPath

1. Split path using `split(/\//)`
2. If the first element is a empty string `""`, shift() it.

> for root route "/", split will become ["",""], so after shift once, will remain [""], which make [This tree](./images/slash_route.png).

### node.getHandlerSets

This method accept a node, and method name.

Every node contain a handler cache.

It get handler from the cache. If the method name is not in the cache. It run a **IIFE** and store the result in the cache. The author is using **||=** ([Logical OR assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_OR_assignment)).

```ts
return (node.handlerSetCache["MethodName"] ||= (() => {
  const handlers = [];

  // I have a question here: Why use maps instead of forEach?
  node.method.maps((m) => {
    const handlerSet = m[method] || m["ALL"];
    if (handlerSet !== undefined) {
      handlers.push({ ...handlerSet });
      return;
    }
  });

  return handlers;
})());
```
