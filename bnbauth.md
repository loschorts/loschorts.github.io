## Phase 7: Front End User Authentication
In this phase, we are going to implement front-end user sign-up and login. Goodbye Rails views; hello, single-page app!

### 1. Build Your Backend.

You should already know how to do this. Create a User Model, Users Controller, and Sessions Controller.

Follow the pattern you used during the [Rails curriculum](#), keeping in mind the following: 
  * Your controllers should live under an `Api` namespace and return JSON formatted responses.
  * We only care about one user (the current user), so adjust your routes and controllers accordingly (i.e. you have a single user `resource`, not `resources`).
  * `Users#show` should return the `current_user` if she or he exists.
  * If any auth errors arise (e.g. 'invalid credentials' or 'username already exists'), return those errors in your response with a corresponding error status. 

### 2. Build Your Frontend.
Set up the following flux architecture components.
####UserStore
  * Keep track of the `_currentUser`, if any. 
  * Keep track of any `_authErrors`, if they arise.

####UserActions 
  * `fetchCurrentUser`
  * `login` a user
  * `logout` a user if one is logged in
  * `create` a user
  * `destroy` a user account

These actions should all rely on the UserApiUtil to make the actual request.

####UserApiUtil
  * `fetchCurrentUser`
  * `login` a user
  * `logout` a user if one is logged in
  * `create` a user
  * `destroy` a user account

Make sure to provide `success` and `error` callbacks to your queries. What do the error callbacks do?

#### CurrentUserStateMixin
Say you have multiple components that will want to update themselves based on who is logged in. Those components have a *cross-cutting concern*, i.e. a need for a shared set of functionalities. 

The way it works in React is with [**Mixins**](https://facebook.github.io/react/docs/reusable-components.html#mixins). A mixin is simply an object full of functions that can be added to a component. It is analogous to a Ruby `module`; generically, we could call it a *namespace*. 

We are going to create a `CurrentUserStateMixin` that can be added to any component that wants to keep track of the current user.

So let's start by creating a new file `frontend/mixins/current_user_state.js`. This file is going to export an object called `CurrentUserStateMixin`, which will have three functions:
  * ``getInitialState`: get the currentUser and authErrors from the UserStore and add them to `this.state`.
  * `componentDidMount`: 
    * Add a UserStore listener that will `updateUser`
    * `fetchCurrentUser` if the `UserStore.currentUser` is undefined.
  * `updateUser`: update the state of currentUser and authErrors.

We can add this mixin to any component by requiring it and adding it under the property `mixins` like so: 

```
var Component = React.createClass({
		mixins: [CurrentUserStateMixin],
		//other methods go here
})
```

Now our `Component` has all the methods we wrote in `CurrentUserStateMixin` and we can easily sprinkle that functionality wherever else it's needed.

Don't worry if you want to do other things at those life-cycle events (e.g., have more initial state). Write your component hooks as normal, and React will run both the mixin hook and your hook when the event triggers.

### 3. Create a NavBar
Use your sprinkly-shiny mixin to create a NavBar component in your App that shows a login/signup form if nobody is logged in, and greets the user and gives them a logout button if they are logged in. Make sure to show errors if they screw up the forms!
