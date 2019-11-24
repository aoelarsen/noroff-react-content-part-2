# Lesson 2 - Styled Components

The code for this lesson can be found on [this branch](https://github.com/cnnrbrn/noroff-react-content-part-2-code/tree/lesson-2).

---

Next provides its own css-in-js solution, but we are going to try [Styled Components](https://www.styled-components.com/).

With Styled Components your styles only apply to a single component. You use plain CSS and don't have to worry about clashing class names or naming systems like `BEM`.

This is in contrast to pre-processors like `Sass` which apply global styles to your app.

You can still use Sass-like conventions like nesting and we can create a theme to use in all components.

## Getting Styled Components to work with Next.

Next often requires extra configuration to get a library working due to server-side requirements. This is true of Styled Components.

The Next [examples repo](https://github.com/zeit/next.js/tree/canary/examples) has many configuration examples for a wide variety of libraries and implementations.

The slightly modified code below comes [this example](https://github.com/zeit/next.js/tree/canary/examples/with-styled-components).

Install `styled-components`:

```js
npm install styled-components
```

Install `babel-plugin-styled-components` as a dev dependency.

```js
npm install -D babel-plugin-styled-components
```

Add a `.babelrc` file to the root of your project with this content:

```js
{
  "presets": ["next/babel"],
  "plugins": [["styled-components", { "ssr": true }]]
}
```

In `pages`, add `_document.js` with this content:

```js
import Document from "next/document";
import { ServerStyleSheet } from "styled-components";

export default class MyDocument extends Document {
    static async getInitialProps(ctx) {
        const sheet = new ServerStyleSheet();
        const originalRenderPage = ctx.renderPage;

        try {
            ctx.renderPage = () =>
                originalRenderPage({
                    enhanceApp: App => props => sheet.collectStyles(<App {...props} />),
                });

            const initialProps = await Document.getInitialProps(ctx);
            return {
                ...initialProps,
                styles: (
                    <>
                        {initialProps.styles}
                        {sheet.getStyleElement()}
                    </>
                ),
            };
        } finally {
            sheet.seal();
        }
    }
}
```

`_document.js` is a special file used by Next to add elements to the `html` and `body` tags. The above code will insert our stylesheet into the document every time the server loads the page.

In `pages`, add `_add.js` with this content:

```js
import App from "next/app";
import React from "react";
import { ThemeProvider } from "styled-components";
import theme from "../constants/theme";

export default class MyApp extends App {
    render() {
        const { Component, pageProps } = this.props;
        return (
            <ThemeProvider theme={theme}>
                <Component {...pageProps} />
            </ThemeProvider>
        );
    }
}
```

`_app.js` is a special file we can use to extend page initialisation. In the above code we are using it to wrap our page components in a `ThemeProvider` component which will pass a `theme` prop that we can use in all our styled components.

Create `constants/theme.js` and add the following:

```js
export default {
    colors: {
        primary: "#bf083a",
    },
};
```

This file exports an object that we will use to house our theme variables. You can add any properties you want here and use them in your styled components.

## Using Styled Components

Create `components/layout/Heading.js`:

```js
import styled from "styled-components";

const Heading = styled.h1`
    color: red;
    margin-top: 20px;
`;

export default Heading;
```

In these lines:

```js
const Heading = styled.h1`
    color: red;
    margin-top: 20px;
`;
```

We are creating a `Heading` component of an h1 element. Inside the `` tags we can write normal CSS to style this component.

We can create a styled component of most element types, e.g:

```js
const Button = styled.button`
    background: white;
    color: black;
`;

const Paragraph = styled.p`
    font-size: 18px;
`;
```

We can now use these components just like any other component and they will be styled by the CSS we have added to them.

In `pages/about.js`, import the `Heading` component and add it to the return:

```js
import Layout from "../components/layout/Layout";
import Heading from "../components/layout/Heading";

export default function About() {
    return (
        <Layout>
            <Head title="About | Noroff Next" />

            <Heading>About this app</Heading>
        </Layout>
    );
}
```

Styled components can receive props and you can use them within the styles.

Add another CSS property to the `Heading` component:

```js
const Heading = styled.h1`
    color: red;
    margin-top: 20px;
    background: ${props => props.background};
`;
```

The **styled.h1``** notation is called a `Tagged template literal`. [Here](https://mxstbr.blog/2016/11/styled-components-magic-explained/) is a good explanation of how they work and are used in styled components.

Basically, we can execute functions within the tags and the functions will receive the `props` we pass into the component.

So in the above code, if we pass in a `prop` called `background` we can set the background CSS property to that `prop` using the fat arrow syntax.

```js
    background: ${props => props.background};
```

Back in `pages/about.js`, add a `background` `prop` to the `Heading` component:

```js
<Heading background="#ebebeb">About this app</Heading>
```

Watch your browser for changes by passing in different colours to the `background` prop.

We can also use `ternary operators` (shorthand `if` statements) to decide how to style the components based on the prop passed in.

Add another CSS property to the `Heading` component:

```js
const Heading = styled.h1`
    color: red;
    margin-top: 20px;
    background: ${props => props.background};
    font-size: ${props => (props.small ? "13px" : "32px")};
`;
```

When deciding the `font-size`, the function will check if a `small` prop has been passed in, and if so set the size to `13px`; if it hasn't been passed in the size will be `32px`.

Test it out by adding/removing a `small` prop in the `Heading` component in `pages/about.js`

```js
<Heading background="#ebebeb" small>
    About this app
</Heading>
```

Because we added a `ThemeProvider` in `pages/_app.js`, we have access to the `theme` object in each styled component.

Let's change the color property to use the `primary` colour we set in `constants/theme.js`:

```js
const Heading = styled.h1`
    color: ${props => props.theme.colors.primary};
    // rest of the properties...
`;
```

## Building the character cards with styled components

Create `components/characters/list/CharacterList.js`:

```js
import React from "react";
import PropTypes from "prop-types";
import Row from "react-bootstrap/Row";
import Col from "react-bootstrap/Col";
import CharacterItem from "./CharacterItem";

export default function CharacterList({ characters }) {
    return (
        <Row>
            {characters.map(character => {
                return (
                    <Col sm={6} md={3} key={character.id}>
                        <CharacterItem character={character} />
                    </Col>
                );
            })}
        </Row>
    );
}

CharacterList.propTypes = {
    characters: PropTypes.arrayOf(PropTypes.object).isRequired,
};
```

This maps over the `characters` prop and sends each `character` as a prop to a `CharacterItem` component we'll create next.

In `components/characters/list/CharacterItem.js` add the following:

```js
import React from "react";
import PropTypes from "prop-types";
import Card from "./Card";
import Name from "./Name";
import Image from "./Image";

export default function CharacterItem({ character }) {
    const { name, image, species, gender } = character;

    return (
        <Card species={species}>
            <Name>{name}</Name>
            <Image src={image} gender={gender} />
        </Card>
    );
}

CharacterItem.propTypes = {
    character: PropTypes.object.isRequired,
};
```

Here we receive a `character` object prop, destructure the `name`, `image`, `species` and `gender` properties from the object, and pass them into what will be our three styled components, `Card`, `Name` and `Image`.

Above we are passing a `species` prop into the `Card` component. Let's build that styled component.

In `components/characters/list/Card.js`:

```js
import styled from "styled-components";

const Card = styled.div`
    padding: 1em;
    margin-bottom: 1em;
    box-shadow: 0 3px 4px #ebebeb;
    background: ${props => (props.species === "Human" ? "lightgray" : "white")};
    border-width: 2px;
    border-style: solid;
    border-color: ${props => (props.species === "Human" ? "transparent" : "darkgray")};
`;

export default Card;
```

This is a `div` component.

To set our `background` property, we are checking the value of the `species` prop passed in. If it's equal to "Human" we set the background to `lightgray`. If not, we set it to `white`.

To set our `border-color` property, we are checking the value of the `species` prop again. If it's equal to "Human" we set the background to `transparent`. If not, we set it to `darkgray`.

Create `components/characters/list/Image.js`:

```js
import styled from "styled-components";

const Image = styled.img`
    border: 3px white solid;
    max-width: 100%;
    border-radius: ${props => (props.gender === "Female" ? "50%" : 0)};
`;

export default Image;
```

This is an `img` component.

Here we're checking the `gender` prop's value. If it's "Female" we set the `border-radius` to `50%`, making it a circle. If not, the image will have no `border-radius` and be a square.

Create `components/characters/list/Name.js`:

```js
import styled from "styled-components";

const Name = styled.h5`
    font-size: 0.9em;
    text-transform: uppercase;
    color: ${props => props.theme.colors.characterName};
`;

export default Name;
```

This is an `h5` component.

We aren't checking or using props we pass in manually here, but rather accessing properties from our `theme` object to set the `color`.

We need to add the `characterName` property to our `theme` object in `constants/theme.js`:

```js
export default {
    colors: {
        primary: "#bf083a",
        characterName: "black",
    },
};
```

Finally in `pages/index.js`, we need to:

```js
import CharacterList from "../components/characters/list/CharacterList";
```

and replace

```js
{
    props.characters.map(character => {
        return <div key={character.id}>{character.name}</div>;
    });
}
```

with

```js
<CharacterList characters={props.characters} />
```

Change the values in the `theme` object and the styled components to see how the components are updated.

## Styling any component

So far we've only styled native HTML elements like `h1` and `div`. It's possible to style any component using the `styled` method:

```js
const StyledComponent = styled(SomeComponent)`
    color: red;
`;
```

Say we wanted to add a top margin to the Bootstrap `Container` component in our `Layout` component, we could add the following in `components\layout\Layout.js`:

```js
// ... other immports
import Container from "react-bootstrap/Container";
import styled from "styled-components";

// here we create a new custom styled component from react-bootstrap's Container
const StyledContainer = styled(Container)`
    margin-top: 30px;
`;

export default function Layout(props) {
// ... rest of function definition
```

Then we replace

```js
<Container>{props.children}</Container>
```

with

```js
<StyledContainer>{props.children}</StyledContainer>
```

## Practice

-   Add a new property in the `theme` object. Use this property to set the `border-width` of the `Card` component.
-   Send a `species` prop into `Card`. Change the `border-colour` of the image based on whether the `species` is "Alien" or not.
-   Create an `EpisodeCount` component. This must display the episodes count below the `Image`. Style the `font-size` of this component with a property from the `theme`. If the episode count is greater than 2, its colour must be `red`, otherwise it must be `black`.
