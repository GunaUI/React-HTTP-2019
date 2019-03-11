This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).

### Nested Route
* Sometimes you also have a set up where you want to create a nested route. So where you want to load a certain component or where you want to render certain content inside of another component which is also loaded via routing.

```jsx
return (
            <div>
                <section className="Posts">
                    {posts}
                </section>
                <Route path="/posts/:id" exact component={FullPost} />
            </div>
        );
```
* Here we are trying to load individual full post inside the list of posts component.
* We can improve this with adding dynamic route path as below

```jsx
<Route path={this.props.match.url + '/:id'} exact component={FullPost} />
```
### Receive the URL param

* Receive the URL params inside the component...

```jsx
this.props.match.params.id
```

### Redirecting request

* here if we are in base URL slash("/) just redirect to the route "/posts"

```jsx
import { Route, NavLink, Switch, Redirect } from 'react-router-dom';
....
....
<Redirect from="/" to="/posts" />
```
* For example once after submit if we want to redirect to the base or some specific page use the below code

```jsx
this.props.history.replace('/posts');
```
### Guards (only for authenticated Roles)
* Just redirect in componentDidMount or conditionally apply the component . by this we can restrict the unauth visit.
### 404 Request Handling
* Redirect or render some 404 template component.

### Basename in sever deployment
* it's important to add basename in server deployement, basename is noting if you are running from sub-directory my-app then you need to add that as basename.

```jsx
<BrowserRouter basename="/my-app">
    <div>
        <Blog />
    </div>
</BrowserRouter>
```




