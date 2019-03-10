This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).

### Links and routing
* Now lets go to the blog.js file, and add some links to that.
```html
<header>
    <nav>
        <ul>
            <li><a href="/">Home</a></li>
            <li><a href="/new-post">New Post</a></li>
        </ul>
    </nav>
</header>
```
* Now setup the npm router package

```js
    npm install react-router react-router-dom
```
* We installed both react-router  and react-router-dom . Technically, only react-router-dom  is required for web development. It wraps react-router  and therefore uses it as a dependency. 

* We don't need to install react-router  on our own for it to work. You can omit this installation step, I left it in there for historic reasons and because I like to emphasize that the main package is named react-router.

### Routing Logic

* Now first of all we need to enable routing on our react app
* In App.js (Main container) add router logic
```jsx
import { BrowserRouter } from 'react-router-dom';

class App extends Component {
    render() {
        return (
            <BrowserRouter>
                <div>
                    <Blog />
                </div>
            </BrowserRouter>
        );
    }
}
```
* Then we have to wrap the everying in app.js with BrowserRouter object as above.

### Multiple page as container (restructuring)
* move all the pages as container from component
* created new posts container and move those post related contents from blog.js to posts.js (Refer Posts.js container)
* Now we created mutiple conatiner , let us see how to frame route rules.

### Setting up and Rendering Routes

* Now we need to import route package
```js
import { Route } from 'react-router-dom';
```
* Path defining with render property
```jsx
<Route path="/" exact render={() => <h1>Home</h1>} />
<Route path="/" render={() => <h1>Home 2</h1>} />
```
* here exact property say , if your path exactly matches.

* Path defining with component property.

```jsx
<Route path="/" exact component={Posts} />
<Route path="/new-post" component={NewPost} />
```
* here component property needs component as input to render.
* with this current routing we have a issue that the page will reload everytime when you switch between routers, this is actually not good.
* for this issue we need to a special link component
```jsx
import { Route, Link } from 'react-router-dom';
...
...
<ul>
    <li><Link to="/">Home</Link></li>
    <li><Link to={{
        pathname: '/new-post',
        hash: '#submit',
        search: '?quick-submit=true'
    }}>New Post</Link></li>
</ul>
```
* here we should tel the link , where this should lead to.
* search will be used as query param and then hash will be added at the end.
* React routing will add some extra params to the props.
* routing related the props are not passed down the component tree, we can't access them in components in some cases like if we return child component like below.
```jsx
const post = (props) => {
    return (
        <article className="Post" onClick={props.clicked}>
            <h1>{props.title}</h1>
            <div className="Info">
                <div className="Author">{props.author}</div>
            </div>
        </article>
    )
}
```
* we need to pass this the corresponding child component by spead operator of props or you could pass a particular props.

```jsx
    posts = this.state.posts.map(post => {
        return <Post 
            key={post.id} 
            title={post.title} 
            author={post.author}
            {...this.props}
        />;
    });
```
* or else we could use withRouter HOC in child component.
```jsx
import { withRouter } from 'react-router-dom';
...
...
export default withRouter(post);
```
### Absolute Vs Relative Path
* Absolute path means that it's always appended right after your domain. Therefore, both syntaxes (with and without leading slash) lead to example.com/some-path .

* Sometimes, you might want to create a relative path instead. This is especially useful, if your component is already loaded given a specific path (e.g. posts ) and you then want to append something to that existing path (so that you, for example, get /posts/new ).

If you're on a component loaded via /posts , to="new"  would lead to example.com/new , NOT example.com/posts/new . 

To change this behavior, you have to find out which path you're on and add the new fragment to that existing path. You can do that with the url  property of props.match :

* Example for releative URL can be formed as below
```jsx
<li><Link to={{
    pathname: this.props.match.url + '/new-post',
    hash: '#submit',
    search: '?quick-submit=true'
}}>New Post</Link></li>
```

### Style Active Menu based on route

* For this instead of Link we will use NavLink. relplace Link with NavLink, this NavLink will add more property like active class to the link.
* Important , Make sure your basic slash or nested URL has exact property , or else this active class css will be applied everywhere..
* We can use our user defined class name and style for active class , its not necessary to use the existing class name as "active"
```jsx
<li><NavLink 
        exact 
        to="/"
        activeClassName="my-active"
        activeStyle={{
            color: '#fa923f',
            textDecoration: 'underline'
        }}
        >
        Home
        </NavLink>
</li>
```
### Pass Dynamic Param Id to the URL
* First we have to form a route for your dynamic structure as below

```jsx
<Route path="/:id" exact component={FullPost} />
```
* :id will be replaced with the dynamic id, for that we need to send different id on click the post Link as below

```jsx
<Link key={post.id} to={"/"+ post.id}>
    ...
    ...
</Link>
```
### Retrive the passed dynamic Id
* To retrive the passed dynamic id , we can fetch from match.params.id as below
```jsx
this.props.match.params.id
```
### Query Params:
* But how do you extract search (also referred to as "query") parameters (=> ?something=somevalue  at the end of the URL)? How do you extract the fragment (=> #something  at the end of the URL)?

* You can pass them easily like this:

```jsx
<Link to="/my-path?start=5">Go to Start</Link> 
```
* OR
```jsx
<Link 
    to={‌{
        pathname: '/my-path',
        search: '?start=5'
    }}
    >Go to Start</Link>
```
* React router makes it easy to get access to the search string: 
```jsx
props.location.search 
```
* But that will only give you something like 
```jsx
?start=5 
```
* To extract value from above follow the below method

```jsx

componentDidMount() {
    const query = new URLSearchParams(this.props.location.search);
    for (let param of query.entries()) {
        console.log(param); // yields ['start', '5']
    }
}
```
* URLSearchParams  is a built-in object, shipping with vanilla JavaScript. It returns an object, which exposes the entries()  method. entries()  returns an Iterator - basically a construct which can be used in a for...of...  loop (as shown above).

* When looping through query.entries() , you get arrays where the first element is the key name (e.g. start ) and the second element is the assigned value (e.g. 5 ).

### Fragment:
* You can pass like this.
```jsx
<Link to="/my-path#start-position">Go to Start</Link>
```
* OR

```jsx
<Link 
    to={‌{
        pathname: '/my-path',
        hash: 'start-position'
    }}
    >Go to Start</Link>
```
* React router makes it easy to extract the fragment. You can simply access 
```jsx
props.location.hash
```
### Using Switch to load single route at a time.
* We can use switch to load a single route at time , once the match route reached it will stop analysing this may cause error. here order of the route is very important.

```jsx
import { Route, NavLink, Switch } from 'react-router-dom';
<Switch>
    <Route path="/new-post" component={NewPost} />
    <Route path="/posts" component={Posts} />
</Switch>
<Route path="/:id" exact component={FullPost} />
```
* Or else we could use some unique url to avoid loading multiple route at at time.

* There is some alternative for this navigating instead of using Link we have some other option , please check in next branch.





