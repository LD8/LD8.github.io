---
layout: post
title: "React Router"
categories: Daily Notes
---

## React Router Notes

### Install

```bash
$ npm install react-router-dom
```

### import `BrowserRouter, Route, Switch`

```js
import { BrowserRouter, Route, Switch } from "react-router-dom";

<BrowserRouter>
  <Navbar />
  <Switch>
    <Route exact path="/" render={() => <Home myFunc={SomeFunction} />} />
    <Route exact path="/about" component={About} />
  </Switch>
</BrowserRouter>;
```

### `<Link>` to

```js
import { Link } from "react-router-dom";

<Link to="/">Home</Link>;
```

### `<Redirect to="/">` from 'react-router'

### `<Prompt>`

```js
<Prompt when={a condition} message={(location)=> {
  // firstly: when the condition above is met: e.g. {loggenIn && !age}
  // check if user wants to go somewhere with url starts with '/user', if yes go ahead, if no prompt the confirm alert/message
  return location.pathname.startsWith('/user') ? true : "are you sure"
}}>
```

### \* `useParams` to access strings from urls

```js
import { useParams } from "react-router";

const { name } = useParams();
// this will get parameter from path="/user/:name"
```

### \* `useLocation` hook to access object passed from `<Link>` tag

```js
<Link
  to={{
    pathname: "/about",
    state: {
      from: "root",
    },
  }}
>
  About
</Link>;

import { useLocation } from "react-router";
function About() {
  const location = useLocation();
  // location is the object passed in by the Link tag
}
```
