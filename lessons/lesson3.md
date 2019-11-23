# Lesson 3 - Class components

Time to add a card for each item.

Add the following file and CSS to it: `src/components/characters/list/CharacterList.css`

```css
.card {
    margin-bottom: 30px;
    box-shadow: 0px 3px 16px #ccc;
    transition: all 0.4s;
}

.card:hover {
    box-shadow: 0 0 0 #ccc;
}

.card-title.h5 {
    font-size: 1em;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
}

.search {
    margin: 2em 0;
}

.clear-search {
    margin-bottom: 30px;
}

.clear-search__button {
    margin-left: 10px;
}
```

And import it in the `CharacterList` component.

```js
import "./CharacterList.css";
```

We are going to create a new component for our card code. Add the following file and code to it: `src/components/characters/list/CharacterItem.js`

```js
import PropTypes from "prop-types";
import Card from "react-bootstrap/Card";
import Button from "react-bootstrap/Button";
import "./CharacterList.css";

function CharacterItem({ id, name, image }) {
    return (
        <Card>
            <Card.Img variant="top" src={image} />
            <Card.Body>
                <Card.Title>{name}</Card.Title>
                <Button variant="secondary" block>
                    View
                </Button>
            </Card.Body>
        </Card>
    );
}

CharacterItem.propTypes = {
    id: PropTypes.number.isRequired,
    name: PropTypes.string.isRequired,
    image: PropTypes.string.isRequired,
};

export default CharacterItem;
```

We are destructuring each property from the `props` object so that we can use them directly. We also added a `propTypes` object for design-time prop type checking. We aren't using the `id` prop just yet.

Finally, we are making use of the `Card` and `Button` components from `react-bootstrap`.

Back in the `CharacterList` component, add the following imports:

```js
import Row from "react-bootstrap/Row";
import Col from "react-bootstrap/Col";
import CharacterItem from "./CharacterItem";
```

And change the return code to:

```js
return (
    <Row>
        {characters.map(character => {
            const { id, name, image } = character;

            return (
                <Col sm={6} md={3} key={id}>
                    <CharacterItem id={id} name={name} image={image} />
                </Col>
            );
        })}
    </Row>
);
```

We're destructuring the properties from each `character` object, then sending them as props in to the `CharacterItem` component.

We've wrapped all the components in a `Row` and each `CharacterItem` in a `Col`. You could move the `Col` in to the `CharacterItem` if that made more sense to you.

## Linking to the detail page

When we click the "View" button we want to go to the detail page. We'll use `react-router-dom` for this.

Import `withRouter` from the libary:

```js
import { withRouter } from "react-router-dom";
```

`withRouter` is a [Higher Order Component](https://reactjs.org/docs/higher-order-components.html). A higher-order component is a function that takes a component and returns a new component.

We'll pass our `CharacterItem` into this function which will return another component with a `history` object on it that we can use to programmatically navigate.

```js
export default withRouter(CharacterItem);
```

Now we can get the `history` object from the props and add an inline function to the click event of the button to navigate to the detail page and add the id to the path.

Full code:

```js
import PropTypes from "prop-types";
import { withRouter } from "react-router-dom";
import Card from "react-bootstrap/Card";
import Button from "react-bootstrap/Button";
import "./CharacterList.css";

function CharacterItem({ id, name, image, history }) {
    return (
        <Card>
            <Card.Img variant="top" src={image} />
            <Card.Body>
                <Card.Title>{name}</Card.Title>
                <Button
                    variant="secondary"
                    block
                    onClick={() => history.push(`/character/${id}`)}
                >
                    View
                </Button>
            </Card.Body>
        </Card>
    );
}

CharacterItem.propTypes = {
    id: PropTypes.number.isRequired,
    name: PropTypes.string.isRequired,
    image: PropTypes.string.isRequired,
    history: PropTypes.object.isRequired,
};

export default withRouter(CharacterItem);
```

## The detail components

We're going to add a `CharacterDetailContainer` component at: `src/components/characters/detail/CharacterDetailContainer`.

This time we will use a `class` component.

```js
import React, { Component } from "react";
import PropTypes from "prop-types";
import Spinner from "react-bootstrap/Spinner";
import { BASE_URL } from "../../../constants/API";
import "./CharacterDetail.css";

export default class CharacterDetailContainer extends Component {
    state = {
        details: null,
        loading: true,
    };

    componentDidMount() {
        // the API call will happen here
    }

    render() {
        const { loading, details } = this.state;

        if (loading || !details) {
            return <Spinner animation="border" className="spinner" />;
        }

        return null;
    }
}
```

Back in `Layout.js` we need to add this component to our routes:

```js
import CharacterDetailContainer from "../characters/detail/CharacterDetailContainer";
```

```js
<Switch>
    <Route path="/" exact component={Home} />
    <Route path="/about" component={About} />
    <Route path="/character/:id" component={CharacterDetailContainer} />
</Switch>
```

We have added the `id` parameter to the route. That is passed in `CharacterItem`'s click event:

```js
onClick={() => history.push(`/character/${id}`)}
```

Now if we click on a card's button we will see the loading spinner return from the `CharacterDetailContainer`.

Back in `CharacterDetailContainer` let's make the API call. We'll use the `params` object on the `match` prop to get the `id` from the url.

First we add the propType checks (this is where we can add them on a class) and then do the API call in componentDidMount. When the call is complete we set the `state.details` property to the return value and set `state.loading` to false:

```js
static propTypes = {
    match: PropTypes.object.isRequired,
}

state = {
    details: null,
    loading: true,
}

componentDidMount() {
    // get the id from the URL
    const { id } = this.props.match.params
    //create the URL string
    const url = `${BASE_URL}/${id}`

    fetch(url)
        .then(response => response.json())
        .then(json => {
            this.setState({
                details: json,
                loading: false
            })
        })
        .catch(error => {
            console.log(error)

            this.setState({
                loading: false
            })
        });
}
```

We could also use an `async-await` call here:

```js
async componentDidMount() {
    const { id } = this.props.match.params
    const url = `${BASE_URL}/${id}`

    try{
        const response = await fetch(url);
        const json = await response.json();

        this.setState({
            details: json,
            loading: false
        })
    }
    catch(error) {
        console.log(error)
        this.setState({
            loading: false
        })
    }
}
```

Now we can display the character's details:

```js
return <div>{details.name}</div>;
```

Full code so far:

```js
import React, { Component } from "react";
import PropTypes from "prop-types";
import Spinner from "react-bootstrap/Spinner";
import { BASE_URL } from "../../../constants/API";
import "./CharacterDetail.css";

export default class CharacterDetail extends Component {
    static propTypes = {
        match: PropTypes.object.isRequired,
    };

    state = {
        details: null,
        loading: true,
    };

    async componentDidMount() {
        const { id } = this.props.match.params;
        const url = `${BASE_URL}/${id}`;

        try {
            const response = await fetch(url);
            const json = await response.json();

            this.setState({
                details: json,
                loading: false,
            });
        } catch (error) {
            this.setState({
                loading: false,
            });
        }
    }

    render() {
        const { loading, details } = this.state;

        if (loading || !details) {
            return <Spinner animation="border" className="spinner" />;
        }

        return <div>{details.name}</div>;
    }
}
```

We'll add a new component to display the image and details.

Add the following file and CSS to it: `src/components/characters/detail/CharacterDetail.css`

```css
.detail-image {
    display: flex;
    justify-content: center;
    margin-bottom: 40px;
}

.character-breadcrumb .breadcrumb {
    background-color: transparent;
    margin-bottom: 40px;
    border-bottom: 1px solid #ddd;
    border-radius: 0;
}

.character-breadcrumb a {
    color: #333333;
    font-weight: bold;
}

.character-breadcrumb a::before {
    content: "<<";
    margin-right: 7px;
}
```

Then create `src/components/characters/detail/CharacterDetail.js` with the following code:

```js
import PropTypes from "prop-types";
import Row from "react-bootstrap/Row";
import Col from "react-bootstrap/Col";
import Image from "react-bootstrap/Image";
import "./CharacterDetail.css";

export default function CharacterDetail({ details }) {
    const { name, image, gender, species, status, episode, location } = details;

    return (
        <Row>
            <Col md={6} className="detail-image">
                <Image src={image} roundedCircle />
            </Col>
            <Col>
                <h1>{name}</h1>
            </Col>
        </Row>
    );
}

CharacterDetail.propTypes = {
    details: PropTypes.object.isRequired,
};
```

We're receiving one object prop called `details` from the calling component and using more `react-bootstrap` components.

Now we can import and call this from `CharacterDetailContainer`:

```js
import CharacterDetail from "./CharacterDetail";
```

```js
return <CharacterDetail details={details} />;
```

Finally in this section we are going add one more component that will render a list of character details.

Create the following: file `src/characters/detail/DetailList.js` and the code:

```js
import PropTypes from "prop-types";
import ListGroup from "react-bootstrap/ListGroup";

export default function DetailList({
    gender,
    species,
    status,
    episodes,
    location,
}) {
    const numberOfEpisodes = episodes.length;

    const { name: locationName } = location;

    return (
        <ListGroup>
            <ListGroup.Item>
                <b>Gender</b>: {gender}
            </ListGroup.Item>
            <ListGroup.Item>
                <b>Species</b>: {species}
            </ListGroup.Item>
            <ListGroup.Item>
                <b>Status</b>: {status}
            </ListGroup.Item>
            <ListGroup.Item>
                <b>Episodes</b>: {numberOfEpisodes}
            </ListGroup.Item>
            <ListGroup.Item>
                <b>Location</b>: {locationName}
            </ListGroup.Item>
        </ListGroup>
    );
}

DetailList.propTypes = {
    gender: PropTypes.string.isRequired,
    species: PropTypes.string.isRequired,
    status: PropTypes.string.isRequired,
    episodes: PropTypes.array.isRequired,
    location: PropTypes.object.isRequired,
};
```

Two things to note here are that `episodes` is an array and we want to only display the number of episodes; and we are destructuring the `name` property from the `location` object and giving it an alias of `locationName`.

We can now import the `DetailList` component in `CharacterDetail` and pass the props in. Final code for `CharacterDetail`:

```js
import React from "react";
import PropTypes from "prop-types";
import Row from "react-bootstrap/Row";
import Col from "react-bootstrap/Col";
import Image from "react-bootstrap/Image";
import DetailList from "./DetailList";
import "./CharacterDetail.css";

export default function CharacterDetail({ details }) {
    const { name, image, gender, species, status, episode, location } = details;

    return (
        <Row>
            <Col md={6} className="detail-image">
                <Image src={image} roundedCircle />
            </Col>
            <Col>
                <h1>{name}</h1>
                <DetailList
                    gender={gender}
                    species={species}
                    status={status}
                    episodes={episode}
                    location={location}
                />
            </Col>
        </Row>
    );
}

CharacterDetail.propTypes = {
    details: PropTypes.object.isRequired,
};
```

---

## Practice

-   Add more details from the API call to the `DetailList` component.
-   Create an `ErrorAlert` component which takes a single `message` prop. In `CharacterDetailContainer` check for the presence of an `error` property on the json result. If it exists display the `ErrorAlert` rather than the `CharacterDetail`. A missing or badly formed `id` in the URL will return an error.

---
