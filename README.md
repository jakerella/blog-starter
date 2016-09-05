# Blog Starter Project

These are some starter files for project building the front end of a blog system. Further down you can read the details of the API to use for data storage and retrieval.

## Goals

The goal of this project is to build the front end for a custom blog site. The back end API for data storage and retrieval has already been built and provides a simple REST API using JSON data in and out. The API has three types of objects: `Author`, `Category`, and `Post`. Your task is build this front end using _Angular_, including making use of all the concepts covered in your course: modules, controllers, expressions, services, directives, filters, etc. How you implement it is up to you!

### Where do I start?

A good place to start any project is with a set of mockups or wireframes developed alongside your client or users. That said, you can also use the mockups designed for you in the `/mockups` directory! You do not have to use them, but if you do you should take note of some requirements:

* Don't copy and paste... that defeats the purpose of this exercise!
* Don't forget to include the stylesheet in your own HTML.
* Make sure to use the _exact_ class names you see in the mockups or you will have to update the stylesheet as well.
* You should be making a Single Page Application (SPA), so you should not be using these HTML files directly! (Break things up into templates!)
* Not all pages are created for you here, so be sure to extrapolate from these mockups.

### What views do I need to create?

Good question! This would be something you would sit down with your client to determine based on their workflow (and that of their users). For us, try to create the following views/pages:

* _Home page_: list most recent 5 posts with buttons to go back and forth in time
  * Don't forget the header, footer, and sidebar!
* _Account registration_: for creating a new blog user
* _Account login_: for logging in with an email and password
* _Post creation_: for creating new blog posts
* _Single post view_: for viewing an individual blog post
* _Error page_: this will be shown any time there is an error
* _Account Logout_: not technically a view, but this should be possible... how?

Here are some other views you can create if you have more time:

* A page with all posts for a given Author
* A page to view all posts in a given Category
* A page listing all post titles within a given month
* A search page for posts
* A way to edit a blog post
* A way to edit a user profile (email, name, password)
* A way to manage categories (add, remove, edit)
* A way to delete a blog post with a confirmation dialog box (from what view?)

## Blog API Details

The blog's back end is already written and cannot be changed easily (read: won't happen unless it's a serious bug). **You should get the base URL of blog API from your instructor.**

> NOTE: Any endpoint denoted with an asterisk ( * ) after the description is secure (requires authentication)! After logging in (see below), the front end must send the authorization token in the `Authorization` header: `Authorization: thetokenvalue...`

### Authors

```js
{
  "id": String,
  "name": String,
  "email": String,
  "password": String,  // Note: this is NEVER returned, only accepted as input on Author creation and login
  "posts": Array       // NOTE: only present when "include" filter used (see below)
}
```

* `GET /Authors` - retrieve a list of all authors *
* `GET /Authors/:id` - retrieve a single author *
* `POST /Authors` - create a new author (do NOT provide the `id`, it will be assigned by the back end)
* `PUT /Authors/:id` - update the given author *
* `DELETE Author/:id` - delete the given author *
* `POST /Authors/login` - Authenticate a user. Required data: `{ "email": "...", "password": "..." }`

> Note: The current user can only update or delete their _own record_.

The `/Authors/login` endpoint returns the following data:

```js
{
  "id": "8ec71100d...6fc271ba...",        // the token to use in the `Authorization` header
  "ttl": 1209600,                         // how long the token is valid for
  "created": "2016-04-25T19:08:00.145Z",  // when the token was created
  "userId": "571ba0271a8ec71100d46fc2"    // the Author `id` associated with this token
}
```

(For testing, you can use me to log in: `jordan@theironyard.com` / `p4sswerd`)

#### Including Related Records

For either of the `GET` endpoints, you can add a "filter" to the query string to include all posts in the returned author(s). For example:

`/Authors?filter={"include":"posts"}`

### Categories

```js
{
  "id": String,
  "name": String,
  "posts": Array   // NOTE: only present when "include" filter used (see below)
}
```

* `GET /Categories` - retrieve all Categories
* `GET /Categories/:id` - retrieve a single Category
* `POST /Categories` - create a new category (do NOT provide the `id`, it will be assigned by the back end) *
* `DELETE /Categories/:id` - delete a single category *

#### Including Related Records

For either of the `GET` endpoints, you can add a "filter" to the query string to include all posts in the returned categories. For example:

`/Categories?filter={"include":"posts"}`

### Posts

```js
{
  "id": String,
  "title": String,
  "content": String,
  "date": Date,
  "categoryId": String,
  "category": Object,   // NOTE: only present when "include" filter used (see below)
  "authorId": String,
  "author": Object      // NOTE: only present when "include" filter used (see below)
}
```

* `GET /Posts` - retrieve a list of all posts
* `GET /Posts/:id` - retrieve a single post
* `POST /Posts` - create a new post (do NOT provide the `id` or `date`, they will be assigned by the back end) *
* `PUT /Posts/:id` - update the given post *
* `DELETE Posts/:id` - delete the given post *

#### Filters (includes, limits and ordering)

For either of the `GET` endpoints, you can add a "filter" to the query string to include the `category` and `author` objects in the returned data. For example:

`/Posts?filter={"include":["author"]}`

`/Posts?filter={"include":["author","category"]}`

You can restrict the list to some number of results with the `limit` filter and skip a specific number of entries using the `offset` filter. For example, if you want to include the `author` and `category` data, but want to only return 10 results and skip to the fourth result, you can use this endpoint:

`/Posts?filter={"limit":10,"offset":4,"include":["author","category"]}`

You can also order posts by any field using the `order` filter. Note that the order filter needs a string with the property name to sort by and either `ASC` or `DESC` depending on what direction you want the ordering to go. Also, note that the `order` filter can be combined with any of the filters above!

`/Posts?filter={"order":"title ASC","limit":5,"include":["author"]}`

The endpoint above would show 5 posts sorted by their `title` in ascending order and include the author (but not category)

#### Searching Posts

The last filter you can use is to search blog posts: the `where` filter. This filter takes a JSON object to restrict the result set by. Here is an example which gets all posts by a given Author (id) with the word "foobar" in the title:

`/Posts?filter={"where":{"title":{"like":"foobar"},"authorId":"57ab76b4a94db51100f67293"}}`

Notice that the "where" filter accepts an object with the properties you want to search on. Here's what that would like like spread out over multiple lines:

```
{
  "where": {
    "title": {
      "like": "foobar"
    },
    "authorId": "57ab76b4a94db51100f67293"
  }
}
```
