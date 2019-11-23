# Lesson 4 - Filtering results

Let's filter the characters in the `CharacterList` component.

First we need a search component: `src/components/characters/list/Search.js`.

```js
import PropTypes from "prop-types";
import InputGroup from "react-bootstrap/InputGroup";
import FormControl from "react-bootstrap/FormControl";

export default function Search({ handleSearch }) {
    return (
        <InputGroup className="search">
            <FormControl
                placeholder="Search by name..."
                onChange={event => handleSearch(event)}
            />
        </InputGroup>
    );
}

Search.propTypes = {
    handleSearch: PropTypes.func.isRequired,
};
```

The component receives one prop of type function. The `FormControl`'s onChange event will call that function and pass the elements `event` object in to the function as an argument.

We need to add that function to `CharacterList`, import `Search` and pass the function in as the `handleSearch` prop.

```js
import Search from "./Search";
```

The function just logs the event for now:

```js
// add the function above the loading check
const filterCards = function(e) {
    console.log(e);
};

if (loading) {
    return <Spinner animation="border" className="spinner" />;
}
```

We'll add the `Search` above the row of cards:

```js
return (
    <>
        <Search handleSearch={filterCards} />
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
    </>
);
```

Typing in the search input will now log the input's event.

We need to add more state variables to our component, this time to manage filtered characters:

```js
const [characters, setCharacters] = useState([]);
const [filteredCharacters, setFilteredCharacters] = useState([]);
const [loading, setLoading] = useState(true);
```

When the API call returns we will set both `characters` and `filteredCharacters` to the results:

```js
.then(json => {
    setCharacters(json.results);
    setFilteredCharacters(json.results);
})
```

Both variables will hold all the results initially. We'll now map over `filteredCharacters` in the return instead of `characters`:

```js
{
    filteredCharacters.map(character => {
        // ...
    });
}
```

Now our `filterCards` function must filter the `characters` array and then set the value of `filteredCards` to the new filtered array:

```js
const filterCards = function(e) {
    // Let's get the value the user typed in and make it lower case:
    const searchValue = e.target.value.toLowerCase();

    // create a new array from the characters array
    const filteredArray = characters.filter(function(char) {
        // make each name lowercase so we can check it properly with the search value
        const lowerCaseName = char.name.toLowerCase();

        // check if the character name begins with the search value
        if (lowerCaseName.startsWith(searchValue)) {
            // if it does, return true
            // this will add it to the new filtered array
            return true;
        }
        return false;
    });

    // set filtered characters to the new array
    setFilteredCharacters(filteredArray);
};
```

The function inside the `filter` method runs against every item in the `characters` array and checks if it should be added to the new array. Don't put things like alerts in there as you'll get an alert for every item in the `characters` array.

---

# Practice

-   Create a new component called `Breadcrumb`. Use the `withRouter` HOC from `react-router-dom` to create a link back to the `CharacterList` component from the detail page/component.
-   Add a `ClearSearch` component. Render this next to the `Search` input. This component should receive one function as a prop that fires on the click event of a button. The function should set the `filteredCharacters` in `CharacterList` equal to `characters`. The button should only be enabled if the search's input value is not empty.

---
