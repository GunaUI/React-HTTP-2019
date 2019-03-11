This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).

### Navigating Programatically
* Remove Link  in posts.
* Navigating without link (alternate way).
```jsx
// this.props.history.push({pathname: '/posts/' + id});
this.props.history.push( '/posts/' + id );
```
* push method allow us to push page on top of the stack of pages...

### Adding information regarding active links.

```jsx
<ul>
    <li><NavLink  
    to="/posts"
    activeClassName="my-active"
    activeStyle={{
        color: '#fa923f',
        textDecoration: 'underline'
    }}
    >Home</NavLink></li>
    <li><NavLink to={{
        pathname: '/new-post',
        hash: '#submit',
        search: '?quick-submit=true'
    }}>New Post</NavLink></li>
</ul>

<Switch>
    <Route path="/posts" exact component={Posts} />
    <Route path="/new-post" component={NewPost} />
    <Route path="/posts/:id" exact component={FullPost} />
</Switch>
```




