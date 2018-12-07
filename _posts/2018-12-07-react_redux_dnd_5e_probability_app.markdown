---
layout: post
title:      "React/Redux DnD 5e Probability App"
date:       2018-12-07 13:06:14 -0500
permalink:  react_redux_dnd_5e_probability_app
---

## Musings on React/Redux Difficulties

React/Redux is a much different creature from Ruby on Rails, concerning itself mainly w/ the 'V' in MVC. 

It is not a full stack solution. It is a library for building a front-end UI, meaning the developer must do extra work to decide how to persist app data and combine it with a working backend.

As Javascript libraries, React/Redux is also asynchronous. To build a working React/Redux app, a dev must have a good understanding of both the React lifecycle and the Redux lifecycle to make sure] state updates and component rendering happens in the right order.

Despite these difficulties, React/Redux is a good framework to learn, and has key advantages.

## React/Redux Strengths

JSX is a high-performance, high-level language that abstracts low-level HTML code in a way that is far more efficient to maintain.

React/Redux uses *declarative programming*, rather than *imperative* - the framework is structured to tell the app *what* to do instead of *how* to do it. - All parent components render child components in a consistent, relatively simple flow, and splitting your app into *containers* and *presentational components* is not just good for the single responsibility principle - It's also a consistent, a organized way of giving your app a consistent file structure and deciding how you will render each model in your app.

The main challenge of React/Redux is that it's consistent, declarative flow is that it requires more creative  solutions with some higher-level features - like logins, error handling, and redirects.

However, tackling those challenges will help a student programmer become a better programmer, as it will force them to think about the underlying lifecycle of the frameworks they use and build creative solutions on that knowledge.

## DnD App

My app is a **[Dungeons and Dragons Probability Calculator.](https://github.com/VictoriaMeng/dnd-probability-calculator)** 

To those who don't know, Dungeons and Dragons is a game where you roll dice, and that dice determines whether or not someone gets hurt.

This app calculates AC hit-chances, saving throw chances, and average damage.

* **An AC hit chance** is the chance that a player character's attack will hit an enemy target. The player rolls a d20 (twenty-sided-die) and adds the number to an attack modifier. If the total value is equal to or greater than the target's Armor Class (AC), the attack hits the target. Rolling a 1 is an automatic failure. Rolling a 20 is an automatic hit.

* **A saving throw chance** is the chance that a player character's spell will effect an enemy whe the player targets one of its stats. With a saving throw, the target rolls a d20 and adds the targeted stat modifier to the roll. Possible targeted stats are **Strength, Dexterity, Constitution, Intelligence, Wisdom, and Charisma**. If the roll is lower than the player's DC (Defense Class), the spell works and the target is effected by the spell.

* **Damage** is the health point loss an enemy takes when hit by an attack or on a failed saving throw. Damage is calculated by rolling dice based on the type of attack.

## App Structure - Rails Backend 
The Rails backend of this app contains three class models: ToHitChances, DamageCalculations, and User. 

There was originally a fourth model for SavingThrowChances, but there was no difference between the saving throws and AC hit chance models other than the formula used to calculate success chance. 

To be DRY, the Saving ThrowChance model was deleted, and the ToHitChances model has an instance method which calculates the chance result conditionally based on whether the target stat is 'AC' or one of the saving throw stats.

Each of the three reminaining models has a corresponding backend controller and serializer, but not a view subdirectory, since the app views are handled in the React/Redux frontend instead.

In addition, there is a fourth SessionsController to handle backend authentication when the user logs in.

## App Structure - React/Redux Frontend

Each of the three models are rendered in the frontend as a collection of container and presentational components. 

Users login/signup through an AuthInput component and enter calculation data through a ToHitInput and DamageInput component, all rendered as child components in their respective containers. 

Using thunk middleware, various actions invoke `fetch()` to send the input data to the Ruby backend, where the input is validated, the probabilities calculated, and the final result returned to the frontend via Activerecord serializers.

The UserContainer renders the user page which displays saved hit chance and damage calculations. It retrieves this data by invoking `fetch()` in `componentDidMount` if there are no existing calculations in the state.

## Additional Frontend Concerns

### Redirects

How on earth can we implement a redirect in a framework of parent/child JSX components? - By turning redirects themselves into a `<Redirect>` component.

The `react-router-dom` library includes a `<Redirect>` component that can be conditionally invoked in `render()`. It has a `to` prop that can take either a string or object as a value, based on whether the new destination link is static or dynamic.

So a redirect to a static link would look like :

```<Redirect to="/login"/>```

While a redirect to a dynamic link would contain an object value for the `to` prop instead of a string. That object will contain a `pathname` key which can take an interpolated Javascript string as a value.

```<Redirect to={ { pathname: `/users/${id}` } } />```

This is because the JSX syntax does not recognize interpolated Javascript strings when passed directly into a component.

The redirect control flow can be determined in two ways. If the redirect depends on a value in the app store, one can write a boolean expression - an action - that checks the store.

For example, the following code conditionally renders public or private NavItems based on whether the user is logged in:

```
class NavContainer extends Component {
  componentDidMount() {
    this.props.checkLogin()
  }

  componentDidUpdate() {
    this.props.checkLogin()
  }

  renderAuthLinks = () => {
    if (!this.props.isLoggedIn) {
      return (
        <PublicNav />
      )
    }
  }

  renderLogout = () => {
    if (this.props.isLoggedIn) {
      return (
        <PrivateNav logout={this.props.logout} />
      )
    }
  }

  render() {
    return (
      <Container className='main-nav'>
        <Navbar>
          {this.renderAuthLinks()}
          {this.renderLogout()}
        </Navbar>
      </Container>
    )
  }
}

const mapStateToProps = state => {
  return {
    isLoggedIn: state.session.isLoggedIn
  }
}

const mapDispatchToProps = dispatch => {
  return {
    logout: () => dispatch(logout()),
    checkLogin: () => dispatch(checkLogin())
  }
}

export default connect(mapStateToProps, mapDispatchToProps)(NavContainer)```

If instead the redirect depends on a value local to the component, not in the store, one can define a local state value `redirect` which returns `true` or `false`.

For example, the following code redirects the user from the Damage form page to the user page, where they can view their new damage calculations.

```export default class DamageInput extends Component {
  componentDidUpdate() {
    if (this.props.newPost) {
      this.setRedirect()
    }
  }
 
  state = {
    dice_count: "",
    die_value: 4,
    user_id: sessionStorage.getItem('id'),
    redirect: false
  }

  handleChange = (event) => {
    this.setState({
      [event.target.name]: event.target.value
    })
  }

  setRedirect = () => {
    this.setState({
      redirect: true
    })
  }

  renderRedirect = () => {
    if (this.state.redirect) {
      return <Redirect to={ { pathname: `/users/${this.state.user_id}` } } />
    }
  }

  handleSubmit = (event) => {
    event.preventDefault()
    this.props.calculateDamage(this.state)
  }
  
  render() {
    return (
      <Fragment>
        {this.renderRedirect()}
        <h2>Damage Calculator</h2>
        <Form inline className='form mt-xl-5' onSubmit={this.handleSubmit}>
          <Input onChange={this.handleChange} type="number" name="dice_count" id="dice-count" />
          <Input onChange={this.handleChange} type="select" name="die_value" id="die-value">
            <option value="4">d4</option>
            <option value="6">d6</option>
            <option value="8">d8</option>
            <option value="10">d10</option>
            <option value="12">d12</option>
          </Input>
          <Input className='input-btn ml-sm-2' type="submit" value="Calculate!"></Input>
        </Form>
      </Fragment>
    )
  }
} ```

There are Redux devs who dislike controlled components - components with local state - but this component already had local state to handle form submission, so it did not greatly change the component structure to add one more `redirect` attribute to the state.

## Login Flow

Login can be difficult for new React/Redux devs because Redux store data does not automatically persist, meaning a user can log out just by refreshing the page.

One solution to this is to use `sessionStorage`, which can store a token or user id upon login that will be erased when the user ends the session by closeing the browser. However, `sessionStorage` is a React feature not in sync with the Redux lifecycle, which can lead to issues where the user needs multiple refreshes before the correct data can be conditionally rendered. It is not always enough to check login status by invoking `sessionStorage.hasOwnProperty('id')`.

The solution I used is to save the user ID in `sessionStorage` upon login AND write a `sessionReducer` that saves the user's login state as a boolean value. Then, I wrote an action `checkLogin()` which checks the sessionStorage and updates the sessionReducer based on whether a user ID exists in `sessionStorage`. By invoking `checkLogin()` in `componentDidMount`, the app can update `sessionReducer` between refreshes so a page refresh will not log out the user.

This is my app's session reducer, which only saves one boolean value:

```
export default function sessionReducer(state = {isLoggedIn: false}, action) {
  switch (action.type) {
    case 'LOGGED_IN':
      return {isLoggedIn: true}
    default:
      return {isLoggedIn: false}
  }
}```

Below are the `login()` and `checkLogin()` actions in the ```sessionActions``` file:

```export const checkLogin = () => {
  if (userIdSaved()) {
    return {type: 'LOGGED_IN'}
  } else {
    return {type: 'LOGGED_OUT'}
  }
}

export const saveUserId = (respJSON) => {
  sessionStorage.setItem('id', respJSON.id)
}

export const login = (formData) => {
  return function(dispatch) {
    dispatch({type: 'LOGIN_REQUEST'})
    return fetch('http://localhost:3000/login', {
      headers: {
        'Content-Type': 'application/json'
      },
      method: 'POST',
      body: JSON.stringify({user: formData})
    })
    .then(handleResponse)
    .then(respJSON => {
      saveUserId(respJSON)
      dispatch({type: 'LOGGED_IN', payload: respJSON})
    })
    .catch(error => dispatch({type: 'HANDLE_ERROR', payload: error}))
  }
}```

All login authentication is done in the backend. Based on the above code, when a user successfully logs in, the app saves the user id in `sessionStorage` AND updates the store. 

Then, `checkLogin()` can be invoked in components that conditionally render based on the login state - Take a look again at the `NavContainer` code I posted in the previous section:

```
class NavContainer extends Component {
  componentDidMount() {
    this.props.checkLogin()
  }

  componentDidUpdate() {
    this.props.checkLogin()
  }

  renderAuthLinks = () => {
    if (!this.props.isLoggedIn) {
      return (
        <PublicNav />
      )
    }
  }

  renderLogout = () => {
    if (this.props.isLoggedIn) {
      return (
        <PrivateNav logout={this.props.logout} />
      )
    }
  }

  render() {
    return (
      <Container className='main-nav'>
        <Navbar>
          {this.renderAuthLinks()}
          {this.renderLogout()}
        </Navbar>
      </Container>
    )
  }
}

const mapStateToProps = state => {
  return {
    isLoggedIn: state.session.isLoggedIn
  }
}

const mapDispatchToProps = dispatch => {
  return {
    logout: () => dispatch(logout()),
    checkLogin: () => dispatch(checkLogin())
  }
}

export default connect(mapStateToProps, mapDispatchToProps)(NavContainer)```

A market-ready app would have futher authentication methods, including tokens, but that is not needed for the scope of this smaller app.
	
There are other concerns I would like to discuss, including error handling and logout, but due to length I will write about them in future blog posts.








