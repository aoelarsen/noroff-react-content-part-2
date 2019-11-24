# Lesson 4 - Forms

The code for this lesson can be found on [this branch](https://github.com/cnnrbrn/noroff-react-content-part-2-code/tree/lesson-4).

---

We are going to use [React Hook Form](https://react-hook-form.com/) to create a contact form. This is one of the simplest form solutions in React. [Formik](https://jaredpalmer.com/formik/).

```js
npm install react-hook-form
```

Add a menu item called `Contact` into the nav:

```js
<Link href="/contact">
    <a>
        <Nav.Link as="span">Contact</Nav.Link>
    </a>
</Link>
```

Add a new page `pages/contact.js`:

```js
import React from "react";
import useForm from "react-hook-form";
import Button from "react-bootstrap/Button";
import Form from "react-bootstrap/Form";
import Layout from "../components/layout/Layout";

export default function App() {
    const { register, handleSubmit, watch, errors } = useForm();

    function onSubmit(data) {
        console.log("data", data);
    }

    console.log(watch("firstName"));

    return (
        <Layout>
            <form onSubmit={handleSubmit(onSubmit)}>
                <input name="firstName" placeholder="First name" ref={register} />

                <input type="submit" />
            </form>
        </Layout>
    );
}
```

Our form only has one input, `firstName`. We need to register each input using a [ref](https://reactjs.org/docs/refs-and-the-dom.html) and calling the `register` function. A ref is a way to access DOM nodes.

```js
<input name="firstName" placeholder="First name" ref={register} />
```

Each input must have a unique `name` value.

The following line is watching for changes in the `firstName` input. Watch your console as you type inside the input.

```js
console.log(watch("firstName"));
```

`handleSubmit` will run validation on the inputs before invoking the function passed in to it, in this case `onSubmit`. Our `onSubmit` function is simply logging the input data sent from the form. Check your console to see the submitted data. This is where we could make an API call and send the data to the server.

```js
function onSubmit(data) {
    console.log("data", data);
}
```

We don't have any validation on the input so the `onSubmit` function runs every time we submit. Let's make the input required:

```js
<input name="firstName" placeholder="First name" ref={register({ required: true })} />
{errors.firstName && <span>This field is required</span>}
<input type="submit" />;
```

We add the validation in an object passed to the `register` function. Now the `onSubmit` will only run if `firstName` has a value.

The line:

```js
{errors.firstName && <span>This field is required</span>}
```

checks if there is a `firstName` property on the `errors` object and if so, displays a validation error message.

Our form is looking very ugly, lets add some `react-bootstrap` components:

```js
<Layout>
    <Form onSubmit={handleSubmit(onSubmit)}>
        <Form.Group>
            <Form.Label>Name</Form.Label>
            <Form.Control name="firstName" placeholder="Enter your name" ref={register({ required: true })} />
            {errors.firstName && <span>First name is required</span>}
        </Form.Group>

        <Button type="submit">Submit</Button>
    </Form>
</Layout>
```

No functionality has changed here, we are just using the `react-bootstrap` components to style the form. `Form.Control` is just an `input`.

