# Why HonoJS

---

19th July 2022 (22:32pm Malaysia)

I first noticed Hono from a [tweet](https://twitter.com/threepointone/status/1534501486293569537) by Sunil.

It is a quite new project (initial commit on Dec 15, 2021). So I decide to give it a try.

### <u>Let's Go</u>

> I plan to understand the project by ignoring their TS type first. We will get back to it later

HonoJS is a very simple web framework that make for Cloudfare Worker, Deno deploy and Bun.

Example code on how to run a hono server

```ts
import { Hono } from "hono";

const app = new Hono();

app.get("/", (c) => c.text("Hello World"));

app.fire(); // For Cloudfare Worker only?
```

hono package only export single constructor function. "Hono"

### <u>What happen when we construct it?</u>

1. It will extend the environment default **Request** object. By calling [extendRequestPrototype()](https://github.com/honojs/hono/blob/1d73e3a8ed22d819040b2b2a59f6b24b50ecdd58/src/request.ts#L35).

extendRequestPrototype extends [default Request](https://developer.mozilla.org/en-US/docs/Web/API/Request) prototype by adding few function

- param
- header
- query

We will get back to [this](./extendRequestPrototype.md).

2. initial method function on the hono object.

```ts
["get", "post", "put", "delete", "head", "options", "patch"]["all"];

// All this request method will be added to the Hono object itself
```

Maybe I am new to JS, I saw a very interesting way to initial the method function. What the author do is by going through the method array above.

```ts
allMethod.map((method) => {
  this[method] = // <----- Interesting
    (args1, ...args) => {};
});
```

All method function accept **string / object** as argument.

Everytime when we do

```ts
app.get("/hey", (c) => {});
```

Internally, it will set the current `this.path` to the path we pass in. So by this approach, actually we can do thing below (I assume)

```ts
app.get("/hey", (c) => {});
app.post((c) => {});
```

By doing this, the post handler is follow the `/hey` path.

And the **method function** also return `this`
Means we can chain it. Like below

```ts
app.get("/hey", (c) => {}).post((c) => {});
```

All the handlers are insert to the **Router** and push to **Routes**.

> All route path is converted to UpperCase
