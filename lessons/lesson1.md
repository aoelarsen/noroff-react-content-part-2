# Lesson 1 - Next.js

The code for this lesson can be found on [this branch](https://github.com/cnnrbrn/noroff-react-content-part-2-code/tree/lesson-1).

---

[Next.js](https://nextjs.org/) is a React framework that provides, amongst other things, server-side rendering (which will improve your site's SEO) and easy routing.

We are going to rebuild part of last week's project in a Next app.

First we'll use Create Next App to create a new Next app. Run the following command:

```js
    npx create-next-app noroff-next
```

Change into that directory and start the app:

```js
    cd noroff-next
    npm run dev
```

Then open [http://localhost:3000](http://localhost:3000) in your browser.

## App structure

`create-next-app` creates three folders for us:

-   `components` - all our components apart from our page components will live here.
-   `pages` - this will hold the components that will serve as the pages in our app.
-   `static` or `public` - if your version of `create-next-app` calls this folder `static`, please rename it to `public`. This is where things like images and favicons will go.

You can delete `components/nav.js`.

## Routing

Every file in the `pages` folder, such as `about.js` or `contact.js`, will be become a route we can navigate to without having to use a library like `react-router`.

The file names must match the browser url you want. If you want your About Us page to have the path `/about-us`, the file in pages must be called `about-us.js`.

Open `pages/index.js`, delete everything inside and replace it with the following:

```js
import React from "react";
import Link from "next/link";
import Head from "../components/head";

export default function Index() {
    return (
        <>
            <Head title="Noroff Next App" />

            <Link href="/about">
                <a>About</a>
            </Link>
        </>
    );
}
```

Page components are normal React components.

`Head` is a component provided by `create-next-app` that we can use on each page to create the contents of the HTML `<head>` tags, such as the `title` tag and `description` meta tag. Above we are using the `title` prop to set the title of the index page to "Noroff Next App".

It utilises a component imported from `next/head` to append elements to the `head` tag. We'll use this same component shortly.

`Link` is a component provided by Next we can use to navigate around our app. The child of a link component must be an `a` tag. We are setting the `href` prop to `/about` which means clicking the link will navigate to `http://localhost:3000/about`. If you vist that page now you'll be greeted by Next's built-in 404 component.

### Adding a page

Create the file `pages/about.js` and add the following:

```js
import React from "react";
import Link from "next/link";
import Head from "../components/head";

export default function About() {
    return (
        <>
            <Head title="About | Noroff Next" />

            <Link href="/">
                <a>Home</a>
            </Link>
        </>
    );
}
```

We've changed the title in the `Head` component and the `Link` component will take us back to the `index` page. (You may have to reload to see the changes when adding a new page).

## Adding a layout component

We're going to use `React Bootstrap` again so install that:

```js
npm install react-bootstrap
```

Create `components/layout/Layout.js` and add the following:

```js
import React from "react";
import PropTypes from "prop-types";
import Link from "next/link";
import NextHead from "next/head";
import Navbar from "react-bootstrap/Navbar";
import Nav from "react-bootstrap/Nav";
import Container from "react-bootstrap/Container";

export default function Layout(props) {
    return (
        <>
            <NextHead>
                <link
                    rel="stylesheet"
                    href="https://maxcdn.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css"
                    integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T"
                    crossOrigin="anonymous"
                />
            </NextHead>
        </>
    );
}
```

We're using the `NextHead` component from `next/head` to append a link to the Bootstrap CSS CDN file.

Next we'll add the navbar below the `NextHead`:

```js
<Navbar bg="dark" variant="dark" expand="lg">
    <Link href="/">
        <a>
            <Navbar.Brand>Rick and Morty</Navbar.Brand>
        </a>
    </Link>
    <Navbar.Toggle aria-controls="basic-navbar-nav" />
    <Navbar.Collapse id="basic-navbar-nav">
        <Nav className="mr-auto">
            <Link href="/">
                <a>
                    <Nav.Link as="span">Home</Nav.Link>
                </a>
            </Link>
            <Link href="/about">
                <a>
                    <Nav.Link as="span">About</Nav.Link>
                </a>
            </Link>
        </Nav>
    </Navbar.Collapse>
</Navbar>
```

This is similar to what we did last week, but configured to use Next's `Link` component.

We need to render what we want wrapped in this layout. Add the following below the `Navbar`:

```js
<Container>{props.children}</Container>
```

`props.children` is used to display whatever you include between the opening and closing tags of a component. You will see how this works when we add this `Layout` component to our pages.

Finally, add the prop type check below the function:

```js
Layout.propTypes = {
    children: PropTypes.node.isRequired,
};
```

We want `children` to be a `node`, not another type like string or number. The list of prop types can be found [here](https://reactjs.org/docs/typechecking-with-proptypes.html).

Full code for `Layout.js`:

```js
import React from "react";
import PropTypes from "prop-types";
import Link from "next/link";
import NextHead from "next/head";
import Navbar from "react-bootstrap/Navbar";
import Nav from "react-bootstrap/Nav";
import Container from "react-bootstrap/Container";

export default function Layout(props) {
    return (
        <>
            <NextHead>
                <link
                    rel="stylesheet"
                    href="https://maxcdn.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css"
                    integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T"
                    crossOrigin="anonymous"
                />
            </NextHead>
            <Navbar bg="dark" variant="dark" expand="lg">
                <Link href="/">
                    <a>
                        <Navbar.Brand>Rick and Morty</Navbar.Brand>
                    </a>
                </Link>
                <Navbar.Toggle aria-controls="basic-navbar-nav" />
                <Navbar.Collapse id="basic-navbar-nav">
                    <Nav className="mr-auto">
                        <Link href="/">
                            <a>
                                <Nav.Link as="span">Home</Nav.Link>
                            </a>
                        </Link>
                        <Link href="/about">
                            <a>
                                <Nav.Link as="span">About</Nav.Link>
                            </a>
                        </Link>
                    </Nav>
                </Navbar.Collapse>
            </Navbar>
            <Container>{props.children}</Container>
        </>
    );
}

Layout.propTypes = {
    children: PropTypes.node.isRequired,
};
```

### Including the layout in pages

In both `pages/index.js` and `pages/about.js`, import the `Layout` component and replace the fragment tags `<></>` with this component.

How `pages/index.js` should look now:

```js
import React from "react";
import Link from "next/link";
import Head from "../components/head";
import Layout from "../components/layout/Layout";

export default function Index() {
    return (
        <Layout>
            <Head title="Noroff Next" />

            <Link href="/about">
                <a>About</a>
            </Link>
        </Layout>
    );
}
```

### A note on server-side rendering

Add the following to the `index` page:

```html
<p>Lorem ipsum dolor</p>
```

If you view the source of the page (not inspect), you will find that text as well as the `head` content and nav HTML. This is because Next generates the initial content on the server and then sends it to the browser. Updates will still happen in the browser.

This is not the case with apps generated with `create-react-app`. With that, only a very small amount of HTML is send to the browser and the rest is filled in by JavaScript.

Server-side rendered apps will improve the ability of search engines to index your content.

---

## Practice

-   Add description props to the `Head` component in each page. Remember, descriptions should be unique and relevant to each page, and be between 50 and 160 characters long.
-   Create a contact page and add it to the nav.

---

## API calls in Next

We want to store our API URL as a constant again. In `constants/API.js`:

```js
export const BASE_URL = "https://rickandmortyapi.com/api/character/";
```

Because Next is server-side rendered, we need an HTTP library we can use in both the server and client (browser) environments. The built-in `fetch` method in JavaScript only works in the browser.

Install [axios](https://github.com/axios/axios):

```js
npm install axios
```

In Next, an asynchronous function called `getInitialProps` is where API calls are made. With this function, we can fetch data using an API call and pass it as props to the page. `getInitialProps` only works in page components.

In `pages/index.js`, import `axios` and `BASE_URL`:

```js
import axios from "axios";
import { BASE_URL } from "../constants/API";
```

After the function, add the following:

```js
Index.getInitialProps = async function() {
    const response = await axios.get(BASE_URL);
    const data = response.data;
    const results = data.results;
    console.log(results);

    return {
        characters: results,
    };
};
```

Inside this async function we are using `axios` to make the API call. Once we've retrieved the property we want (in this case `results`), the `console.log` method will display the results in your terminal, not the browser. This is because this call is happening on the server.

We return an object from the function that will become the `props` that get passed into our `Index` component.

Change your component to accept `props` as an argument and this time when we `console.log` the data will be displayed in the browser console.

This is how `pages/index.js` should look now with an added prop type check, removal of the unecessary elements and a `map` over the `characters` prop:

```js
import React from "react";
import PropTypes from "prop-types";
import Head from "../components/head";
import Layout from "../components/layout/Layout";
import axios from "axios";
import { BASE_URL } from "../constants/API";

export default function Index(props) {
    return (
        <Layout>
            <Head title="Noroff Next" />

            {props.characters.map(character => {
                return <div key={character.id}>{character.name}</div>;
            })}
        </Layout>
    );
}

Index.propTypes = {
    characters: PropTypes.arrayOf(PropTypes.object),
};

Index.getInitialProps = async function() {
    // in case there is an error in the API call
    // we'll send an empty array in as the prop
    let characters = [];

    try {
        const response = await axios.get(BASE_URL);
        const data = response.data;
        // data.results is the array of characters
        characters = data.results;
        console.log(characters);
    } catch (error) {
        console.log(error);
    }

    // the object we return here will become the props in the page component
    return {
        characters: characters,
    };
};
```
