# React 3
## [React router](https://reactrouter.com/web/guides/quick-start)
To get started with React Router in a web app, you’ll need a React web app. If you need to create one, we recommend you try Create React App. It’s a popular tool that works really well with React Router.First, install create-react-app and make a new project with it.

npx create-react-app demo-app
cd demo-app

npm install react-router-dom

1st Example: Basic Routing
In this example we have 3 “pages” handled by the router: a home page, an about page, and a users page. As you click around on the different <Link> s, the router renders the matching <Route>.Note: Behind the scenes a <Link> renders an <a> with a real href, so people using the keyboard for navigation or screen readers will still be able to use this app.

2nd Example: Nested Routing
This example shows how nested routing works. The route /topics loads the Topics component, which renders any further <Route>'s conditionally on the paths :id value.

## [Styling and CSS](https://reactjs.org/docs/faq-styling.html)

## [Next.js](https://nextjs.org/learn/basics/create-nextjs-app)
Enter Next.js, the React Framework. Next.js provides a solution to all of the above problems. But more importantly, it puts you and your team in the pit of success when building React applications.

Next.js has the best-in-class "Developer Experience" and many built-in features; a sample of them are:

An intuitive page-based routing system (with support for dynamic routes)
Pre-rendering, both static generation (SSG) and server-side rendering (SSR) are supported on a per-page basis
Automatic code splitting for faster page loads
Client-side routing with optimized prefetching
Built-in CSS and Sass support, and support for any CSS-in-JS library
Development environment with Fast Refresh support
API routes to build API endpoints with Serverless Functions
Fully extendable

## [Sass](https://sass-lang.com/guide)
npm install -g sass
You can also watch individual files or directories with the --watch flag. The watch flag tells Sass to watch your source files for changes, and re-compile CSS each time you save your Sass. If you wanted to watch (instead of manually build) your input.scss file, you'd just add the watch flag to your command, like so:

sass --watch input.scss output.css

### Variables
SCSS\
$font-stack:    Helvetica, sans-serif;
$primary-color: #333;

body {
  font: 100% $font-stack;
  color: $primary-color;
}

CSS\
body {
  font: 100% Helvetica, sans-serif;
  color: #333;
}

### Nesting
nav {
  ul {
    margin: 0;
    padding: 0;
    list-style: none;
  }

  li { display: inline-block; }

  a {
    display: block;
    padding: 6px 12px;
    text-decoration: none;
  }
}

CSS\
nav ul {
  margin: 0;
  padding: 0;
  list-style: none;
}
nav li {
  display: inline-block;
}
nav a {
  display: block;
  padding: 6px 12px;
  text-decoration: none;
}

### Mixins
Some things in CSS are a bit tedious to write, especially with CSS3 and the many vendor prefixes that exist. A mixin lets you make groups of CSS declarations that you want to reuse throughout your site. You can even pass in values to make your mixin more flexible. A good use of a mixin is for vendor prefixes. Here's an example for transform.

@mixin transform($property) {
  -webkit-transform: $property;
  -ms-transform: $property;
  transform: $property;
}
.box { @include transform(rotate(30deg)); }

.box {
  -webkit-transform: rotate(30deg);
  -ms-transform: rotate(30deg);
  transform: rotate(30deg);
}

## [Sass cheatsheet](https://devhints.io/sass)