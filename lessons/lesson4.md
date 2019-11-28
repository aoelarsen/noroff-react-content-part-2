# Lesson 4 - Forms

The code for this lesson can be found on [this branch](https://github.com/cnnrbrn/noroff-react-content-part-2-code/tree/lesson-4).

---

We are going to use [React Hook Form](https://react-hook-form.com/) to create a contact form. This is one of the simplest form solutions in React. [Formik](https://jaredpalmer.com/formik/) is another good solution.

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

Our form only has one input, `firstName`. We need to register each input by using a [ref](https://reactjs.org/docs/refs-and-the-dom.html) and calling the `register` function. A ref is a way to access DOM nodes.

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
{errors.firstName && <p>This field is required</p>}
<input type="submit" />;
```

We add the validation in an object passed to the `register` function. Now the `onSubmit` will only run if `firstName` has a value.

The line:

```js
{errors.firstName && <p>This field is required</p>}
```

checks if there is a `firstName` property on the `errors` object and if so, displays a validation error message. 

Our form is looking very ugly, lets add some `react-bootstrap` components:

```js
<Layout>
    <Form onSubmit={handleSubmit(onSubmit)}>
        <Form.Group>
            <Form.Label>Name</Form.Label>
            <Form.Control name="firstName" placeholder="Enter your name" ref={register({ required: true })} />
            {errors.firstName && <p>First name is required</p>}
        </Form.Group>

        <Button type="submit">Submit</Button>
    </Form>
</Layout>
```

No functionality has changed here, we are just using the `react-bootstrap` components to style the form. `Form.Control` is just an `input`.

Let's add two more inputs, `email` and `age`:

```js
<Form.Group>
    <Form.Label>Name</Form.Label>
    <Form.Control name="firstName" placeholder="Enter your name" ref={register} />
    {errors.firstName && <p>First name is required</p>}
</Form.Group>

<Form.Group>
    <Form.Label>Email</Form.Label>
    <Form.Control name="email" placeholder="Enter your email" ref={register} />
    {errors.email && <p>Email is required</p>}
</Form.Group>

<Form.Group>
    <Form.Label>Age</Form.Label>
    <Form.Control type="number" name="age" placeholder="Enter your age" ref={register} />
    {errors.age && <p>Age is required</p>}
</Form.Group>
```

Providing a validation object for an input inside the `register` function is convenient for simple validation like `required`, but becomes clumsy when trying to validate things like email addresses.

We are going to use [Yup](https://github.com/jquense/yup) to create a validation schema to validate the inputs.

```js
npm install yup
```

With Yup we build a schema object that we want the data submitted from the form to match. If the data matches the schema, validation passes.

This will be our Yup schema:

```js
const schema = yup.object().shape({
	firstName: yup.string().required("First name is required"),
	email: yup
		.string()
		.email("Please enter a valid email")
		.required("Email is required"),
	age: yup
		.number()
		.required()
		.integer()
		.min(10, "Age must be greater than 10")
		.max(30, "Age must be less than 10")
});
```

Yup provides default messages for each validation criteria but we can also supply our own.

Now we have to pass the schema into `useForm`:

```js
const { register, handleSubmit, watch, errors } = useForm({
    validationSchema: schema
});
```

Previously we hard-coded the error messages, e.g. 

```js
 {errors.email && <p>Email is required</p>}
 ```

 But that's not very user-friendly. The same message would be displayed for a missing email as it would for an invalid one.

Using Yup we can display dynamic error messages (either) depending on which validation criteria failed.

```js
{errors.email && <p>{errors.email.message}</p>}
```

Final code for the form:

```js
import React from "react";
import useForm from "react-hook-form";
import * as yup from "yup";
import Button from "react-bootstrap/Button";
import Form from "react-bootstrap/Form";
import Layout from "../components/layout/Layout";

const schema = yup.object().shape({
	firstName: yup.string().required("First name is required"),
	email: yup
		.string()
		.email("Please enter a valid email")
		.required("Email is required"),
	age: yup
		.number()
		.required()
		.integer()
		.min(10, "Age must be greater than 10")
		.max(30, "Age must be less than 10")
});

export default function App() {
	const { register, handleSubmit, watch, errors } = useForm({
		validationSchema: schema
	});

	function onSubmit(data) {
		console.log("data", data);
	}

	console.log(watch("firstName"));

	return (
		<Layout>
			<Form onSubmit={handleSubmit(onSubmit)}>
				<Form.Group>
					<Form.Label>Name</Form.Label>
					<Form.Control name="firstName" placeholder="Enter your name" ref={register} />
					{errors.firstName && <p>{errors.firstName.message}</p>}
				</Form.Group>

				<Form.Group>
					<Form.Label>Email</Form.Label>
					<Form.Control name="email" placeholder="Enter your email" ref={register} />
					{errors.email && <p>{errors.email.message}</p>}
				</Form.Group>

				<Form.Group>
					<Form.Label>Age</Form.Label>
					<Form.Control type="number" name="age" defaultValue="10" placeholder="Enter your age" ref={register} />
					{errors.age && <p>{errors.age.message}</p>}
				</Form.Group>

				<Button type="submit">Submit</Button>
			</Form>
		</Layout>
	);
}
```


## Practice

- The error message containers are simple `p` tags. Create a Styled Component to display the error messages.

## Form builder

Apart from being arguably the easiest to use React form library, requiring the least amount of code, React Hook Form also offers a [form builder](https://react-hook-form.com/form-builder).

Validation is currently only available through the `register` function. 