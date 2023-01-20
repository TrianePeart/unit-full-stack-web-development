## Getting Started

- Go to your bookmarks2 app and get it started with `nodemon`.
- Fork the `bookmarks2-frontend-starter` in your class GitHub Organization
- `git clone` the forked repository
- Rename the folder **bookmarks2-frontend**
- You should now have two separate folders, **bookmarks2** and **bookmarks2-frontend**
- In your bookmarks2-frontend, `npm install` to install dependencies already included in the `package.json`.
- `touch .env`

**.env**

```
REACT_APP_API_URL=http://localhost:3003
```

- `git add -A`
- `git commit -m 'add front end starter code'`.

- `npm start`

## Index

<details><summary>Landing Page</summary>

![](../assets/landing-page.png)

</details>

Let's click on `Bookmarks` in the navigation to go to `/bookmarks`

<details><summary>Index Page Empty</summary>

![](../assets/index-empty.png)

</details>

We can see that we have a view for our index page. But we need to connect this app to our back-end. We can do so using `axios` much like we did use third-party APIs.

## Setting Up the App to Make Requests

### Loading the Bookmarks Index on Page Load

#### A Small Note

Since the initial build, the team has reviewed the Bookmarks app. They had made several suggestions to improve the code. Here you'll note some code reorganization and updates to make the code easier to read and more maintainable.

Can you spot the differences? Can you talk about why you think the changes have improved the app?

### Building the Index Page Functionality

With React, we sometimes have to think about _when_ things happen. For us, we want to make an API call to get our data AFTER the component has been fully loaded (mounted in the DOM) and then update state.

If we made the API call first, the DOM was not fully loaded, and the state was updated first, we would risk not seeing our data, even though the API call was successful.

React has a built-in function called `useEffect` that will control _when_ the functions inside are called.

This is where we will add our initial API call to get all the bookmarks.

The `useEffect` function is already in **app.js**, but it is empty. Let's add some code.

- Make a `get` request using Axios. `Then` when there is a `response`, do something. In our case, we want to update state using hooks by using the `setBookmarks` that was declared at the beginning of the `App` component. The state will be updated to be our array of bookmark objects.

**src/Components/Bookmarks.js**

```js
useEffect(() => {
  axios.get(`${API}/bookmarks/${id}`);
}, []);
```

Now we can make our API call, but `then`, what should happen?

We'll chain two functions:

`then`, where we'll put our code for what should happen after the Axios call occurs.

Three things can happen:

- the response is resolved (first callback parameter)
- the response is rejected (second callback parameter)
- something has failed (catch function)

The code will still work without the second `then` parameter and the `catch`, but when you are coding, it's a good idea to include at least one of these, as the error messages can be beneficial in debugging your code.

Work from the outside in. Build the outer functions first.

```js
useEffect(() => {
  axios
    .get(`${API}/bookmarks/${id}`)
    .then(() => {})
    .catch(() => {});
}, []);
```

Add the functionality in the right places:

```js
useEffect(() => {
  axios
    .get(`${API}/bookmarks`)
    .then((response) => setBookmarks(response.data))
    .catch((c) => console.warn("catch", c));
}, []);
```

## Show

Let's build out the show page.

![](assets/show-loaded.png)

First, let's start with the JSX, then we'll go back and add functionality. This is the recommended approach, based on the docs: [Thinking in React](https://reactjs.org/docs/thinking-in-react.html)

We are going to hard-code values to see our components and make sure things are set up and organized as we want. Then, we'll go back and add more functionality.

**src/Components/BookmarkDetails.js**

Let's set up the `article` container and add an `h3`. There will be conditional rendering using a ternary operator.

Next to it, let's hardcode the bookmark name.

```js
import { useState } from "react";

function BookmarkDetails() {
  const [bookmark, setBookmark] = useState([]);
  return (
    <article>
      <h3>{true ? <span>⭐️</span> : null} bookmark.name</h3>
    </article>
  );
}
```

Let's add the link for the actual bookmark page.

```js
function BookmarkDetails() {
  const [bookmark, setBookmark] = useState([]);
  return (
    <article>
      <h3>{true ? <span>⭐️</span> : null} bookmark.name</h3>
      <h5>
        <span>
          <a href={bookmark.url}>bookmark.name</a>
        </span>
        &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; bookmark.url
      </h5>
      <h6>bookmark.category</h6>
      <p>bookmark.description</p>
    </article>
  );
}
```

What is `&nbsp;`?

Let's add the buttons for edit, delete, and back.

```js
import { Link } from "react-router-dom";

// Further down inside the component

return (
  <article>
    <h3>{true ? <span>⭐️</span> : null} bookmark.name</h3>
    <h5>
      <span>
        <a href={bookmark.url}>bookmark.name</a>
      </span>
      &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; bookmark.url
    </h5>
    <h6>bookmark.category</h6>
    <p>bookmark.description</p>
    <div className="showNavigation">
      <div>
        <Link to={`/bookmarks`}>
          <button>Back</button>
        </Link>
      </div>
      <div>
        <Link to={`/bookmarks/id/edit`}>
          <button>Edit</button>
        </Link>
      </div>
      <div>
        <button>Delete</button>
      </div>
    </div>
  </article>
);
```

## Adding Functionality

We want to load the bookmark details on page load. We will shorten the JSX in the code snippets ( but don't delete or change it), so we can focus on the functionality.

We'll need to make an API call on page load.

`useEffect` is a built-in function in React that will make the API call after the component has been loaded on the page.

The second argument is the dependencies. This is what React will be 'watching'. If there are changes, it will call useEffect again. If you forget to add the `[]` as the second argument, you risk having useEffect run on an infinite loop.

```js
import { useState, useEffect } from "react";

// inside the BookmarkDetails function....

 useEffect(() => {
},[]);
 return <article>...</article>;
});

```

We will also require a bit more information from our app to make the API call. We need to know where to make the API call and the `id` of the bookmark. The `id` of the bookmark will come from the URL.

We will add the `id` and the `API` variables as the dependencies to useEffect. If you are unsure what to put, usually, the console in the browser will recommend what variables you should put in there.

```js
import axios from "axios";
import { useState, useEffect } from "react";
import { Link, useParams, useNavigate } from "react-router-dom";
const API = process.env.REACT_APP_API_URL;

// inside the BookmarkDetails function....
function BookmarkDetails() {
 const [bookmark, setBookmark] = useState([]);
 let { id } = useParams();

 useEffect(() => {
 axios.get(`${API}/bookmarks/${id}`)
 },[id, API]);

 // rest of the component...

});
```

```js

function BookmarkDetails() {
 const [bookmark, setBookmark] = useState([]);
 let { id } = useParams();

 useEffect(() => {
 axios.get(`${API}/bookmarks/${id}`)
 .then(()=> {}).catch(() =>{})
 },[id, API]);

 // rest of the component...


});

```

Add the catch statement.

```js
useEffect(() => {
  axios
    .get(`${API}/bookmarks/${id}`)
    .then(() => {})
    .catch((c) => {
      console.error("catch", c);
    });
}, [id, API]);
```

Add the response

```js
useEffect(() => {
  axios
    .get(`${API}/bookmarks/${id}`)
    .then((response) => {
      console.log(response.data);
      setBookmark(response.data);
    })
    .catch((c) => {
      console.warn("catch", c);
    });
}, [id, API]);
```

Now that it works as we expect let's go in and add the actual values where we've put our placeholders.

```js
return (
  <article>
    <h3>
      {bookmark.is_favorite ? <span>⭐️</span> : null} {bookmark.name}
    </h3>
    <h5>
      <span>
        <a href={bookmark.url}>{bookmark.name}</a>
      </span>{" "}
      &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
      {bookmark.url}
    </h5>
    <h6>{bookmark.category}</h6>
    <p>{bookmark.description}</p>
    <div className="showNavigation">
      <div>
        {" "}
        <Link to={`/bookmarks`}>
          <button>Back</button>
        </Link>
      </div>
      <div>
        <Link to={`/bookmarks/${id}/edit`}>
          <button>Edit</button>
        </Link>
      </div>
      <div>
        <button>Delete</button>
      </div>
    </div>
  </article>
);
```

## Delete Functionality

From this view, we want to be able to delete a bookmark. It's pretty common to name a function that handles an event (like a click or hover `handle`)

```js
const handleDelete = () => {
  console.log("button clicked");
};
```

Let's add a click event.

```js
<div>
  <button onClick={handleDelete}>Delete</button>
</div>
```

Click the button to check that your click handler works as expected.

We will use another `Axios` call to request the database to delete the bookmark.

When the deletion is completed on the back-end, we can think about the user experience. A reasonable way to handle it is to take the user back to the Index page, so they can see that their bookmark has been successfully deleted.

We can do this by using `useNavigate` from React Router.

```js
// Top of file
import { Link, useParams, useNavigate } from "react-router-dom";

let navigate = useNavigate();

// Inside BookmarkDetails function
const deleteBookmark = () => {
  axios
    .delete(`${API}/bookmarks/${id}`)
    .then(
      () => {
        navigate(`/bookmarks`);
      },
      (error) => console.error(error)
    )
    .catch((c) => console.warn("catch", c));
};
```

Finally, let's call this function:

```js
const handleDelete = () => {
  deleteBookmark();
};
```

#### Full Code

```js
import axios from "axios";
import { useState, useEffect } from "react";
import { Link, useParams, useNavigate } from "react-router-dom";
import Reviews from "./Reviews";

function BookmarkDetails() {
  const [bookmark, setBookmark] = useState([]);
  let { id } = useParams();
  let navigate = useNavigate();
  const API = process.env.REACT_APP_API_URL;

  useEffect(() => {
    axios.get(`${API}/bookmarks/${id}`).then((response) => {
      setBookmark(response.data);
    });
  }, [id, navigate, API]);
  const deleteBookmark = () => {
    axios
      .delete(`${API}/bookmarks/${id}`)
      .then(() => {
        navigate(`/bookmarks`);
      })
      .catch((c) => console.error("catch", c));
  };
  const handleDelete = () => {
    deleteBookmark();
  };
  return (
    <>
      <article>
        <h3>
          {bookmark.is_favorite ? <span>⭐️</span> : null} {bookmark.name}
        </h3>
        <h5>
          <span>
            <a href={bookmark.url}>{bookmark.name}</a>
          </span>{" "}
          &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
          {bookmark.url}
        </h5>
        <h6>{bookmark.category}</h6>
        <p>{bookmark.description}</p>
        <div className="showNavigation">
          <div>
            {" "}
            <Link to={`/bookmarks`}>
              <button>Back</button>
            </Link>
          </div>
          <div>
            <Link to={`/bookmarks/${id}/edit`}>
              <button>Edit</button>
            </Link>
          </div>
          <div>
            <button onClick={handleDelete}>Delete</button>
          </div>
        </div>
      </article>
      <Reviews />
    </>
  );
}

export default BookmarkDetails;
```
