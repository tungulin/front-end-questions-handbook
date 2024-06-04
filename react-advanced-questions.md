## React advanced questions:

### 1. Why does React need keys?

 > Imagine that files on your desktop didn’t have names. Instead, you’d refer to them by their order — the first file, the second file, and so on. You could get used to it, but once you delete a file, it would get confusing. The second file would become the first file, the third file would be the second file, and so on.



Simply put, in order for react to determine which component from the list it needs to re-render. Keys are required for this. **Without the keys, react will not re-render only the changed component in the list, but will simply re-render the entire list**

For a clearer explanation of how and when react decides to re-render a component, I recommend reading  [react-under-the-hood](https://add-link-react-under-the-hood)


### 2. What is HOC in react?

<blockquote>
  HOC is a function that takes one component as input and returns a new one with more advanced functionality as output. In other words, hoc is a function that expects as parameters a component (let’s call it input), which is wrapped in another component declared in the body of the function, which acts as the value returned from the function (let’s call it output).
</blockquote>

**Example Hign-order-component(HOC):**

```jsx
export const WithAuthorize = (isAuthorized,
  {ComponentForAuthorized, ComponentForUnauthorized}) => {
      
    const WrappedComponentWithAuth = (props) => {
      if(isAuthorized) {
        return <ComponentForAuthorized {...props} />
      }
      
      return <ComponentForUnauthorized {...props} />
    }

    return WrappedComponentWithAuth
}
```

**Example of using Hign-order-component(HOC):**

```jsx
const isAuthorize = true // Checking the user is authorized
const ProfileWithAuthorize = WithAuthorize(isAuthorize, ProfileFullInfo, ProfilePreview)
```

What's going on here? It's simple. An arrow function (very similar to a functional component) **WithAuthorize** is created. Which takes two parameters: 
1. **isAuthorized** - this is a flag whether the user is authorized or not.

2. **{ComponentForAuthorized, ComponentForUnauthorized}**  is an object consisting of two components.
    1. ComponentForAuthorized - this is a component that will be displayed if the user is authorized. 
    2. ComponentForUnauthorized - this is a component that will be displayed if the user is not authorized.

Depending on whether the user is authorized or not, **WrappedComponentWithAuth** will return one or another object. **WithAuthorize** returns WrappedComponentWithAuth in its own cases.


**The main advantage of HOCs** is that they allow us to extend the functionality of multiple components without repeating the same code in each of them. This promotes code reuse and improves maintainability of your React applications. In the above example it is very convenient 

Another not obvious, but **important advantage** - unnecessary hooks of the ProfileFullInfo and ProfilePreview components are not called

You may ask me why you can't just conditionally render these components in JSX:

```jsx
const Component = () => {
  const [isAuthorized, setIsAuthorized] = useState(false)

  useEffect(()=>{
    fetchData() // Change isAuthorized = true
  },[])

  return isAuthorized ? ProfileFullInfo : ProfilePreview
}
```

The first state is **isAuthorized = false**, since we have not yet received the data and changed it to true. Because of this, the ProfilePreview component will be rendered first. This component may contain a lot of logic built on react hooks. We must take into account that we cannot conditionally execute hooks. They must be fulfilled. After **isAuthorized = true**, the ProfileFullInfo component will be rendered. So why did we implement react hooks in the ProfilePreview component? That's right, there's nothing for it :) This is where HOC solves this problem. High order component encapsulates the call to hooks (logic). And unnecessary logic will not be implemented.