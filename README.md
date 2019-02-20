This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).

## HTTP
* Create a project with dummy post, NewPost and FullPost component.
* For storing and retriving we are going to use some dummy data. Please refer [jsonplaceholder](https://jsonplaceholder.typicode.com/)
* For sending ajax request we will be using some third party javascript library !!not sepcific to react [axios](https://www.npmjs.com/package/axios)
* First install axios in our project
```js
    npm install axios --save
```
* Now where to use side effect (ie data change when server calls). yes we already see in previous module. the best place is "componentDidMount()".
### GET
* In blog container import axios and do asynchronous server call with transformed data as follows.

```js
import axios from 'axios';

state = {
    posts: [],
    selectedPostId: null
}

componentDidMount () {
    axios.get( 'https://jsonplaceholder.typicode.com/posts' )
        .then( response => {
            const posts = response.data.slice(0, 4);
            const updatedPosts = posts.map(post => {
                return {
                    ...post,
                    author: 'Max'
                }
            });
            this.setState({posts: updatedPosts});
        } );
}
```
* Now we have list of respose with Post Data . we have to render this data with dynamic post component with key, title, author props in blog container.

```js
render () {
        const posts = this.state.posts.map(post => {
            return <Post 
                key={post.id} 
                title={post.title} 
                author={post.author}
            />;
        });

        return (
            <div>
                <section className="Posts">
                    {posts}
                </section>
                <section>
                    <FullPost />
                </section>
                <section>
                    <NewPost />
                </section>
            </div>
        );
    }
```
* Then replace the static dummy data in Post component  with corresponsing props .

### Fetch and view Post based on selected post Id
* Now add a handler (function) to select post onclick.
* In Post.js
```js
const post = (props) => (
    <article className="Post" onClick={props.postClicked}>
        <h1>{props.title}</h1>
        <div className="Info">
            <div className="Author">{props.author}</div>
        </div>
    </article>
);
```
* In Blog container
```js
const postContainer = this.state.posts.map(post => {
    return <Post 
        key={post.id} 
        title={post.title} 
        author={post.author}
        postClicked={() => this.postSelectedHandler(post.id)} 
    />;
});

postSelectedHandler = (id) => {
    this.setState({selectedPostId: id});
}

```
* Once selected Post state ID updated pass selectedPostId to Fullpost component.
```jsx
<FullPost choosedId={this.state.selectedPostId} />
```
* Consume this choosedId post id in FullPost component and fetch single data object to load
* First initialise the state with loaded Post as null. Then get data by id in componentDidUpdate().
```js
state = {
        loadedPost: null
    }

componentDidUpdate () {
        if ( this.props.choosedId ) {
            if ( !this.state.loadedPost || (this.state.loadedPost && this.state.loadedPost.id !== this.props.choosedId) ) {
                axios.get( 'https://jsonplaceholder.typicode.com/posts/' + this.props.choosedId )
                    .then( response => {
                        // console.log(response);
                        this.setState( { loadedPost: response.data } );
                    } );
            }
        }
    }

    render () {
        let post = <p style={{ textAlign: 'center' }}>Please select a Post!</p>;
        if ( this.props.choosedId ) {
            post = <p style={{ textAlign: 'center' }}>Loading...!</p>;
        }
        if ( this.state.loadedPost ) {
            post = (
                <div className="FullPost">
                    <h1>{this.state.loadedPost.title}</h1>
                    <p>{this.state.loadedPost.content}</p>
                    <div className="Edit">
                        <button className="Delete">Delete</button>
                    </div>
                </div>

            );
        }
        return post;
    }
```
* Here used componentDidUpdate() hook why ? why not componentDidMount() ??
* Because the fullpost component there from start , just we are going to update the existing component that is why we used componentDidUpdate to setState.
* Here we check this.state.loadedPost before render because this is asyncronous call ,it might loaded later once got response. But jsx might render berfore we get async repsonse , so it's better to confirm loadedPost response before render JSX.
* When you are setState inside the componentDidUpdate will it cause render the component again and that will cause componentDidUpdate load again. to avoid this check.
```js
componentDidUpdate () {
        if ( this.props.choosedId ) {
            if ( !this.state.loadedPost || (this.state.loadedPost && this.state.loadedPost.id !== this.props.choosedId) ) {
                axios.get( 'https://jsonplaceholder.typicode.com/posts/' + this.props.choosedId )
                    .then( response => {
                        // console.log(response);
                        this.setState( { loadedPost: response.data } );
                    } );
            }
        }
    }
```
### POST data to server.
* Now using axios we are going to create a POST in NewPost Component.
* As ususual create a state and handler to post data to the server.
```js
state = {
    title: '',
    content: '',
    author: 'Max'
}

postDataHandler = () => {
    const data = {
        title: this.state.title,
        body: this.state.content,
        author: this.state.author
    };
    axios.post('https://jsonplaceholder.typicode.com/posts', data)
    .then(response => {
        console.log(response);
    });
}
```
* Add a onlick button to call above handler.
```html
<button onClick={this.postDataHandler}>Add Post</button>
```
### DELETE post
```js
deletePostHandler = () => {
        axios.delete('https://jsonplaceholder.typicode.com/posts/' + this.props.choosedId)
            .then(response => {
                console.log(response);
            });
    }

<button className="Delete" onClick={this.deletePostHandler}>Delete</button>
```
### Handling Errors Locally.
* you could add catch block to your async call 
```js
componentDidMount () {
        axios.get( 'https://jsonplaceholder.typicode.com/posts123' )
            .then( response => {
                const posts = response.data.slice(0, 4);
                const updatedPosts = posts.map(post => {
                    return {
                        ...post,
                        author: 'Max'
                    }
                });
                this.setState({posts: updatedPosts});
                
            } ).catch(error =>{
                console.log(error);
                this.setState({error: true});
            });
    }
```
* based on the error state you could do some changes...

### Interceptors to execute code Globally.
* Now lets go to the index.js file, this is the file which runs App component.
```js
import axios from 'axios';

axios.defaults.baseURL = 'https://jsonplaceholder.typicode.com';
axios.defaults.headers.common['Authorization'] = 'AUTH TOKEN';
axios.defaults.headers.post['Content-Type'] = 'application/json';

axios.interceptors.request.use(request => {
    console.log(request);
    // Edit request config
    return request;
}, error => {
    console.log(error);
    return Promise.reject(error);
});

axios.interceptors.response.use(response => {
    console.log(response);
    // Edit request config
    return response;
}, error => {
    console.log(error);
    return Promise.reject(error);
});


```
* If you may need to remove an interceptor later you can
```js
const myInterceptor = axios.interceptors.request.use(function () {/*...*/});
axios.interceptors.request.eject(myInterceptor);
```






