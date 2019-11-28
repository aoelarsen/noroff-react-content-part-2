# Module Assignment 3: React part 2

You can either create a new Next.js app or use the [lesson-2 branch](https://github.com/cnnrbrn/noroff-react-content-part-2-code/tree/lesson-2) of the module's repo. If you use the branch delete all unecessary files and code.

If you undertake level 2, you can clone the [lesson-3 branch](https://github.com/cnnrbrn/noroff-react-content-part-2-code/tree/lesson-3).

### API

The API endpoint is `https://elephant-api.herokuapp.com/elephants` but it requires a CORS workaround so use: `https://cors-anywhere.herokuapp.com/https://elephant-api.herokuapp.com/elephants`

To get the API call to work on the server using the CORS anywhere proxy, add a X-Requested-With header:

```js
const headers = {
    "X-Requested-With": "XMLHttpRequest"
};

const response = await axios.get(BASE_URL, { headers });
```

### Suggested CSS

- green - #12a260
- red - #920f21
- box-shadow - 0 3px 4px #ccc

## Level 1

#### Question 1
- In the `index` page of the app, call the above API and map over the results.
- Using Styled Components, each elephant's name must be displayed in a card (a rectangle).
- If the elephant is female the card's background colour must be red and the font colour white. If it is male the background must be green and the font colour black.
- Only if the elephant's species is `Asian` must the card have a `box-shadow`. 
- The border radius of the card must be `5px` and set in a Styled Components `theme` object.

Pay special attention to arranging your components in small, reusable units.

Styling, apart from that required in the brief, is not important. The submission needn't be responsive. You can use a UI framework like `react-bootstrap` or leave it out.

#### Question 2
- Add a form to a page called `form` using React Hook Form. There must be two inputs:
    - A required email address
    - A required password with a minimum of 8 characters
- If validation passes, the form must console log the submitted form values.


## Level 2

- Create a page called `redux`.
- Add a button with the text "Toggle". 
- Clicking the button must toggle a boolean value in a Redux store called `buttonIsOn`. 
- Using a Styled Component, if `buttonIsOn`'s value is `true`, the button's background colour must be green. If it's false, the background must be red.
- The default value for `buttonIsOn` must be false.


## Submission

- A GitHub repo called `{your-name}-noroff-react-2`.