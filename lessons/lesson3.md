# Lesson 3 - Redux

The code for this lesson can be found on [this branch](https://github.com/cnnrbrn/noroff-react-content-part-2-code/tree/lesson-3).

---

So far we have only passed props directly into child components, apart from when we used the `theme` object in Styled Components. Styled Components allowed us to access the `theme` properties from any component because it used a form of application state management.

State management allows us to access props and data from a `store` no matter where the component might be in the component tree. The `store` is an object with properties.

![The store](/images/redux-1.png)

Redux is a state management library with a lot of legacy code weight.

It too will take a fair bit of configuration to get working.

## Configuration

We need to install `redux`, `react-redux` and `next-redux-wrapper`:

```js
npm install redux react-redux next-redux-wrapper
```

We also want to install `redux-devtools-extension` so that we can use the Redux Devtools browser extension: [Chrome version](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd), [Firefox version](https://addons.mozilla.org/en-US/firefox/addon/reduxdevtools/).

```js
npm install -D redux-devtools-extension
```

We're going to keep all our Redux code in a folder called `redux`.

Add a menu item called `redux-test` into the nav:

```js
<Link href="/redux-test">
    <a>
        <Nav.Link as="span">Redux Test</Nav.Link>
    </a>
</Link>
```

and a new page: `pages/redux-test.js`
