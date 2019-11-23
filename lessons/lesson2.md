# Lesson 2 - Bootstrap, routing and API calls

The best thing about React components is that they are reusable. Once written, we can simply import them and use them. We can also import components written by other developers.

You used Bootstrap in an earlier course and we are going to use a library with React versions of the components - [React Bootstrap](https://react-bootstrap.github.io/).

From your terminal/command line, run:

```js
npm i react-bootstrap
```

In the `public/index.html` add a link to the latest Boostrap CDN CSS file, just as you would for a non-React site.

```html
<link
    rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css"
/>
```

The first component we want to use from React Bootstrap is the [Navbar](https://react-bootstrap.github.io/components/navbar/).

You'll be used to seeing Bootstrap code examples as HTML tags with classes. In this library all the elements will be React components.

Create a file in `components/layout` called `Layout.js`. We will be using the `Navbar` and `Nav` components, so import both of these in this file. (We always need React imported in our component files, but won't be inluding the import in examples from now on).

```js
import Navbar from "react-bootstrap/Navbar";
import Nav from "react-bootstrap/Nav";
```

Create a function component called `Layout`, copy the example Navbar code from the document linked above and edit it as below:

```js
function Layout() {
    return (
        <Navbar bg="dark" variant="dark" expand="lg">
            <Navbar.Brand href="#home">Rick and Morty</Navbar.Brand>
            <Navbar.Toggle aria-controls="basic-navbar-nav" />
            <Navbar.Collapse id="basic-navbar-nav">
                <Nav className="mr-auto">
                    <Nav.Link href="#home">Home</Nav.Link>
                    <Nav.Link href="#about">About</Nav.Link>
                </Nav>
            </Navbar.Collapse>
        </Navbar>
    );
}

export default Layout;
```

We're using the `Navbar`'s `bg` and `variant` props to change the style of the nav to a dark one. We've changed the brand name to "Rick and Morty" and added an `About` link.

Import this new component in `App.js` and render it above the `<Heading>` component.

```js
return (
    <div className="App">
        <Layout />
        <Heading title={title} />
    </div>
);
```

Our site now has a responsive nav bar.

## Routing

We have a link to an About page in our navigation, but no way to display that content.

First lets add two components, Home and About.

Create the following files: `src/components/home/Home.js` and `src/components/about/About.js`.

Import the `Heading` component in both and export the following:

`Home.js`

```js
export default function Home() {
    return (
        <>
            <Heading title="Rick and Morty" />
        </>
    );
}
```

`About.js`

```js
export default function About() {
    return (
        <>
            <Heading title="About this site" />
            <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit.</p>
        </>
    );
}
```

Now we need something to route us around the site. We are going to use [React Router](https://reacttraining.com/react-router/web/guides/quick-start).

```js
npm i react-router-dom
```

Now we can write code to navigate between these components.

In `Layout.js` import the following:

```js
import {
    BrowserRouter as Router,
    Switch,
    Route,
    NavLink,
} from "react-router-dom";
import Container from "react-bootstrap/Container";
import Home from "../home/Home";
import About from "../about/About";
```

The imports from `react-router-dom` will provide the routing ability and the `Container` component from `react-bootstrap` will hold all of our site's content.

We need to edit what is returned from the `Layout` component again:

```js
return (
    <Router>
        <Navbar bg="dark" variant="dark" expand="lg">
            <NavLink to="/" exact>
                <Navbar.Brand>Rick and Morty</Navbar.Brand>
            </NavLink>
            <Navbar.Toggle aria-controls="basic-navbar-nav" />
            <Navbar.Collapse id="basic-navbar-nav">
                <Nav className="mr-auto">
                    <NavLink to="/" exact className="nav-link">
                        Home
                    </NavLink>
                    <NavLink to="/about/" className="nav-link">
                        About
                    </NavLink>
                </Nav>
            </Navbar.Collapse>
        </Navbar>
        <Container>
            <Switch>
                <Route path="/" exact component={Home} />
                <Route path="/about" component={About} />
            </Switch>
        </Container>
    </Router>
);
```

We swapped out the `Nav.Link` Bootstrap components for `NavLink` components from React Router.

We added a `Switch` to contain our `Routes`. Each `Route` points to a component we created above. The content that the `Routes` point to will be displayed inside the `Container`. We wrapped the `Navbar.Brand` in a `NavLink` that also points to the home page. Finally, everything we return is contained in a `Router` component.

In the browser you will now be able to navigate between the Home and About components.

We can delete the `Heading` component and `title` prop from `App.js`.

---

## Practice

Practise creating routes by building the following (we won't use your code going forward, this is only for practice):

-   Create a `Contact` component at `src/components/contact/Contact.js`
-   Import it in `Layout.js`, create a `NavLink` pointing `to` "/contact" and a `Route` with a path of "/contact" and a component of `Contact`.

---

## Constants

It's always a good idea to store strings that you will reuse as constants.

Create `src/constants/API.js` and add the base URL we will use for the API calls.

```js
export const BASE_URL = "https://rickandmortyapi.com/api/character/";
```

## The first API call

We could make the API call using either a class component or the newer way with hooks. We're going to use hooks.

Create `src/components/characters/list/CharacterList.js`.

`useState` and `useEffect` are the first hooks we'll use, so we need to import them from React. Also import the API URL.

```js
import React, { useState, useEffect } from "react";
import { BASE_URL } from "../../../constants/API";
```

Add the following code:

```js
export default function CharacterList() {
    useEffect(function() {
        fetch(BASE_URL)
            .then(function(response) {
                return response.json();
            })
            .then(function(json) {
                console.log(json);
            })
            .catch(function(error) {
                console.log(error);
            });
    }, []);

    return null;
}
```

We're going to rewrite the hook using fat arrow functions but you can use whichever syntax your prefer:

```js
useEffect(() => {
    fetch(BASE_URL)
        .then(response => response.json())
        .then(json => console.log(json))
        .catch(error => console.log(error));
}, []);
```

The `useEffect` hook allows us to perform "side effects" like API calls in our function component. If you are familiar with class components, useEffect is similar to componentDidMount and componentDidUpdate. `useEffect` runs after every time the component renders, both the first time and after every update. The empty array `[]` as the second argument passed to `useEffect` tell React to run it only after the first render.

In the `home/Home.js` component, import `CharacterList` and render it beneath the heading:

```js
import CharacterList from "../characters/list/CharacterList";

export default function Home() {
    return (
        <>
            <Heading title="Rick and Morty" />
            <CharacterList />
        </>
    );
}
```

If you look in the console, the call returns a json object with an array of character objects on a property called `results`. We need to store that array in a state property on our component so that we can loop through it in our return statement.

Add a `useState` hook above the `useEffect`:

```js
const [characters, setCharacters] = useState([]);
```

This gives us access to a property called `characters` and a method called `setCharacters` we can use to set that property. The empty array `[]` is the initial value for `characters`.

Change the second `then` method to use the `setCharacters` method:

```js
.then(json => setCharacters(json.results))
```

Loop through `characters` in the return:

```js
return (
    <ul>
        {characters.map(c => (
            <li key={c.id}>{c.name}</li>
        ))}
    </ul>
);
```

We are using the `map` method to return an array of `li` tags. The `key` attribute is important; you can remove it to see the warning ESLint will display about it being missing.

Full code:

```js
export default function CharacterList() {
    const [characters, setCharacters] = useState([]);

    useEffect(() => {
        fetch(BASE_URL)
            .then(response => response.json())
            .then(json => setCharacters(json.results))
            .catch(error => console.log(error));
    }, []);

    return (
        <ul>
            {characters.map(c => (
                <li key={c.id}>{c.name}</li>
            ))}
        </ul>
    );
}
```

Our home page now displays the first 20 names of the characters returned from the API. Because this call is paginated, we will only ever retrieve the first 20 items. We won't cover paginated calls in this guide.

### Improving the UI

Let's add a loading spinner while the API call runs. `react-bootstrap` provides one for us:

```js
import Spinner from "react-bootstrap/Spinner";
```

Add a second `useState` hook:

```js
const [loading, setLoading] = useState(true);
```

This gives us a `loading` property and a `setLoading` method to set that property. We're giving `loading` a default property of `true`.

In the `useEffect` hook, add a finally method to set loading to `false`.

```js
.finally(() => setLoading(false));
```

This will run whether the call succeeds or fails - either way, loading is done.

Above the first return statement, add a second that checks if the `loading` property is true and if so returns the `Spinner` component.

```js
if (loading) {
    return <Spinner animation="border" className="spinner" />;
}
```

You can find the props you can use to change the `Spinner`'s appearance in the [libary's docs](https://react-bootstrap.github.io/components/spinners/).

If `loading` is not true, the `Spinner` won't return and our original return will run.

Full code:

```js
import React, { useState, useEffect } from "react";
import Spinner from "react-bootstrap/Spinner";
import { BASE_URL } from "../../../constants/API";

export default function CharacterList() {
    const [characters, setCharacters] = useState([]);
    const [loading, setLoading] = useState(true);

    useEffect(() => {
        fetch(BASE_URL)
            .then(response => response.json())
            .then(json => setCharacters(json.results))
            .catch(error => console.log(error))
            .finally(() => setLoading(false));
    }, []);

    if (loading) {
        return <Spinner animation="border" className="spinner" />;
    }

    return (
        <ul>
            {characters.map(c => (
                <li key={c.id}>{c.name}</li>
            ))}
        </ul>
    );
}
```
