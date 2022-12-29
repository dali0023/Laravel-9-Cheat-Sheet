# React Router Dom-6

Install React:

- `npx create-react-app my-app`

Install React Router Dom-6+

- `npm i react-router-dom`

3 Core Parts:

- Setup your router
- Define your routes
- Handle navigation

###### Step -1: Setup your router: `index.js`

```js
import { BrowserRouter } from "react-router-dom";
render(
  <React.StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </React.StrictMode>
);
```

###### Step -2: Defining Routes

- We can do in it `<App/>` component, but can be done anywhere you want.

```js
import { Route, Routes } from "react-router-dom";
import Home from "./pages/Home";
import Posts from "./pages/Posts";
function App() {
  return (
    <>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/posts" element={<Posts />} />
      </Routes>
    </>
  );
}
```

###### Handling Navigation

```js
import { Route, Routes, Link } from "react-router-dom";
function App() {
  return (
    <>
      <nav>
        <ul>
          <li>
            <Link to="/">Home</Link>
          </li>
          <li>
            <Link to="/posts">Posts</Link>
          </li>
        </ul>
      </nav>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/posts" element={<Posts />} />
      </Routes>
    </>
  );
}
```

###### Advanced Route Definitions:
```diff
- 5 main techniques:
```
- Dynamic Routing
- Routing Priority
- Nested Routes
- Multiple Routes
- useRoutes Hook

###### Dynamic Routing:

- Working with parameter, `/books/1`, `/books/bookName` , and `/books/literally-anything`
$\color{red}{test}$
```js
import { Route, Routes, Link } from "react-router-dom";
function App() {
  return (
    <>
     <nav>
        <ul>
          <li><Link to="/">Home</Link></li>
          <li><Link to="/posts">Posts</Link></li>
        </ul>
      </nav>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/posts" element={<Posts />} />
        <Route path="/posts/:id" element={<Post />} />
      </Routes>
    </>
  );
}
```
