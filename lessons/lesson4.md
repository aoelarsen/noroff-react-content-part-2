# Lesson 4 - Forms

The code for this lesson can be found on [this branch](https://github.com/cnnrbrn/noroff-react-content-part-2-code/tree/lesson-4).

---

We are going to use [React Hook Form](https://react-hook-form.com/) to create a contact form.

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
import Head from "../components/head";
import Layout from "../components/layout/Layout";
import CounterButton from "../components/ui/CounterButton";
import CounterLabel from "../components/ui/CounterLabel";

export default function ReduxTest() {
    return (
        <Layout>
            <Head title="Contact | Noroff Next" />

            <Heading background="#ebebeb">About this app</Heading>
        </Layout>
    );
}
```
