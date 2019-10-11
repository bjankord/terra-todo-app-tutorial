# terra-todo-app

*This todo tutorial is based on https://scotch.io/tutorials/build-a-react-to-do-app-with-react-hooks-no-class-components*

## Getting Started
Navigate to the place you would like your new application to be located and type:

```sh
npx create-terra-react-app react-to-do
```

**Note:** *Running npx before the command allows for it to be installed if it is not already installed globally on your machine.*

Once it is done, you will need to navigate into the project:

```
cd react-to-do
```

Before we start up the dev server, we are going to install some terra components we'll use for this Todo app.

If you are using yarn, run the following command:

```
yarn add terra-button terra-spacer terra-form-input terra-arrange
```

If you are using npm, run the following command:

```
npm install terra-button terra-spacer terra-form-input terra-arrange
```

Then, inside the project, add the following code to import these packages in `src/App.js`

```
import Button from "terra-button";
import Spacer from "terra-spacer";
import InputField from "terra-form-input/lib/InputField";
import Arrange from "terra-arrange";
```

Now let's run the following command to start up the dev server:

```
npm run start
```

and navigate to http://localhost:3000/ to see the spinning React logo in your browser.

## Reading a List of Items.
Let’s start on the Read part of CRUD. We’ll want to make a list of things, just so we can Read/View the list.

## Adding in State
Go into your `src/App.js` file and let’s add a state to our component. We are going to be using React Hooks so state will look a little different than what you're used to in classes.

Replace the following line in the `src/App.js` file:

```js
import React from 'react';
```

with 

```js
import React, {useState} from 'react';
```

Then, inside of the App component, add a list of todos:

```jsx
function App() {
  const [todos, setTodos] = useState([
    { text: "Learn about React" },
    { text: "Meet friend for lunch" },
    { text: "Build really cool todo app" }
  ]);

}
```

The component, as we can see, is a functional component. In past versions of React, function components were unable to handle state, but now, by using Hooks, they can.

The first parameter, todos is what we are going to name our state.
The second parameter, setTodos is what we are going to use to set the state.

The hook of `useState` is what React uses to "hook" into the state of the component. We then create an array of objects and we have the beginnings of our state.

## Comparing to a Class Component

Let's take a quick detour and see how this would've done with classes:
```jsx
class App extends Component {
  state = {
    todos: [
      { text: "Learn about React" },
      { text: "Meet friend for lunch" },
      { text: "Build really cool todo app" }
    ]
  }

  setTodos = todos => this.setState({ todos });

  render() {
    return <div></div>
  }
}
```

A lot more typing. React Hooks let's us make that really clean! We'll continue with our functional component version from above.

## Our "Todo" Component
In `src/App.js` we will want to create a component that we can use later on in the return of the main App component. We will call that Todo and it will pass in the (todo) and show the “text” part of the todo (todo.text):

Add the following line in `src/App.js` right before the opening of our App function component.

```jsx
const Todo = ({ todo }) => <div>{todo.text}</div>;
```

## Using Our Todo Variable to Get a List Returned
Go down to the return part of the App component in `src/App.js` and remove everything nested within the `<Base />` component. We want to empty out that return part especially so that the spinning logo we saw earlier when we navigated to http://localhost:3000, goes away and we can start work to have our list being displayed on the page.

By using the JavaScript method, `map()`, we are able to create a new array of items by mapping over the todo items from state and displaying them by index.

Let’s create a list of items:

```jsx
return (
  <div>
    {todos.map((todo, index) => (
      <Todo
        key={index}
        index={index}
        todo={todo}
      />
    ))}
  </div>
);
```

You App function component should look like this:

```jsx
function App() {
  const [todos, setTodos] = useState([
    { text: "Learn about React" },
    { text: "Meet friend for lunch" },
    { text: "Build really cool todo app" }
  ]);

  return (
    <Base locale="en">
      <div>
        {todos.map((todo, index) => (
          <Todo
            key={index}
            index={index}
            todo={todo}
          />
        ))}
      </div>
    </Base>
  );
}
```

## Creating New Items to the To-Do List
Want to create a new item to the list? What if we forgot to add something to our to-do list and don’t want to forget that thing? Let’s give our application the power to Create a new item for our to-do app.

It is time to create our `TodoForm` component. In this component we want it to:

* Start with an empty state for an input field.
* Be able to update the form by setting the state.
* Handle the submit.
* Setting our Empty State for the Form Input

Remember, we are using React Hooks so state is going to be a little different. To set our state we are going to want to write it like so:

```js
const [value, setValue] = useState("");
```

The first is the "value" and the second is how we are going to be setting the state. The state starts off empty and as we add things to our state, it will add it to our list of to-do items.

We will want to add in a `handleSubmit` variable that can handle our `addTodo` function (we will make that function soon) and add the item to the list. If nothing is in the input box and the user presses “enter”, we want it to not do anything (i.e., not add in an empty tile to the list).

Adding that functionality into a form that has an input box, we should have our code look like this:

```jsx
function TodoForm({ addTodo }) {
  const [value, setValue] = useState("");

  const handleSubmit = e => {
    e.preventDefault();
    if (!value) return;
    addTodo(value);
    setValue("");
  };

  return (
    <form onSubmit={handleSubmit}>
      <InputField
        inputId="todoInput"
        name="todo"
        value={value}
        label="Enter A To Do"
        onChange={e => setValue(e.target.value)}
      />
    </form>
  );
}
```
Note, we are using the [Terra `<InputField />` component](https://engineering.cerner.com/terra-ui/components/terra-form-input/form-input/input-field) in this code sample.

Next, we are going to build out the `addTodo` function in our `App` function component within `src/App.js`.

```jsx
const addTodo = text => {
  const newTodos = [...todos, { text }];
  setTodos(newTodos);
};
```

Notice the lack of `this.state`.? With the new React Hooks, we have no more need to use that. Can you use it? Sure, of course. But the new Hooks allow for less typing, more efficiency, and understand that `this.stat`e. is going to be implied in certain places.

See that spread operator? The three dots before the todos, that is essentially "copying" the list for us so that we are able to add on the new to-do item. Then using our keyword that we set earlier, we will set the state with setTodos.

Next, we are going to add `<TodoForm addTodo={addTodo} />` to our App function component. The entire src/App.js file should look like this so far:

```jsx
import React, { useState } from 'react';
import Base from 'terra-base';
import Button from "terra-button";
import Spacer from "terra-spacer";
import InputField from "terra-form-input/lib/InputField";
import Arrange from "terra-arrange";
import logo from './logo.svg';
import './App.css';

const Todo = ({ todo }) => <div>{todo.text}</div>;

function TodoForm({ addTodo }) {
  const [value, setValue] = useState("");

  const handleSubmit = e => {
    e.preventDefault();
    if (!value) return;
    addTodo(value);
    setValue("");
  };

  return (
    <form onSubmit={handleSubmit}>
      <InputField
        inputId="todoInput"
        name="todo"
        value={value}
        label="Enter A To Do"
        onChange={e => setValue(e.target.value)}
      />
    </form>
  );
}

function App() {
  const [todos, setTodos] = useState([
    { text: "Learn about React" },
    { text: "Meet friend for lunch" },
    { text: "Build really cool todo app" }
  ]);

  const addTodo = text => {
    const newTodos = [...todos, { text }];
    setTodos(newTodos);
  };

  return (
    <Base locale="en">
      <TodoForm addTodo={addTodo} />
      <div>
        {todos.map((todo, index) => (
          <Todo
            key={index}
            index={index}
            todo={todo}
          />
        ))}
      </div>
    </Base>
  );
}

export default App;


```
Go to your browser and play around. You can now add in a to-do item to your list!

## Updating Items in the To-Do List to be Completed
How would we want to update our to-do application? Maybe let’s have the functionality of being able to cross off an item. Let’s get started on that code!

## Updating our State
Our App component needs a little extra to it for the "Completed" status to be able to change. We will be adding in another key/value pair to our list of objects. By adding in an `isCompleted: false` value, we set that to false to begin with and will, when prompted, change that to true.

Let's update our initial app state to the following:

```
const [todos, setTodos] = useState([
     {
       text: "Learn about React",
       isCompleted: false
     },
     {
       text: "Meet friend for lunch",
       isCompleted: false
     },
     {
       text: "Build really cool todo app",
       isCompleted: false
     }
   ]);
```

We will need a function like the `addTodo` function but this one will be able to “complete” an item. We will want to do some similar things that we did in the `addTodo` function like using the spread operator to grab the current list of items. In this function, we will be changing the isCompleted status to true so that it knows it has been completed. It will then update the state and set the state to the newTodos.

Let's add this function in `src/App.js` right after our `addTodo` function within our `<App />` function component.

```
const completeTodo = index => {
  const newTodos = [...todos];
  newTodos[index].isCompleted = !newTodos[index].isCompleted;
  setTodos(newTodos);
};
```

Now we are going to update our `Todo` function component to accept the `completeTodo` function.

By using `completeTodo` in the Todo function, we are going to be able to fire off that functionality when the “Complete” button is clicked, it will add in the textDecoration styling and cross-out the item. This is our way of completing an item on the list and “updating” the list. The code should look as follows:

```
function Todo({ todo, index, completeTodo }) {
  return (
    <Arrange
      fill={<span style={{ textDecoration: todo.isCompleted ? "line-through" : "" }}>{todo.text}</span>}
      fitEnd={
        <Button
          onClick={() => completeTodo(index)}
          variant="neutral"
          text="Done"
        />
      }
    />
  );
}
```

Dive down to the return of the App component and we’ll add in the following line to our `Todo` component:

```jsx
completeTodo={completeTodo}
```

to look like this in the code:

```jsx
<div>
  {todos.map((todo, index) => (
    <Todo
      key={index}
      index={index}
      todo={todo}
      completeTodo={completeTodo}
    />
  ))}
</div>
```

Now we can read our list, add to our list, and update the completed status of each item. Time to work on deleting an item.

## Deleting a To-Do Item
So you’ve completed an item on your to-do list, the day is over and you want to delete it from your list to be able to start over tomorrow. We are going to want to delete that item completely. Let’s see how we can get that going.

By adding just a couple lines, we will be able to add in the functionality of deleting an item.

We will go ahead and build the `removeTodo` function so that when we click on the Remove button to delete an item, that will be fired off. That function will be located by the others underneath the state of the App component.

In this `removeTodo` function, we will again be using the spread operator but once we grab that current list, we will be "splicing" the chosen index off of the array of items. Once that is removed, we will return the new state by setting it with setTodos to be newTodos.

```jsx
const removeTodo = index => {
  const newTodos = [...todos];
  newTodos.splice(index, 1);
  setTodos(newTodos);
};
```

In the `Todo` function component, we are going to add the remove button and some spacing. Update the `Todo` component so it looks like the following:

```jsx
function Todo({ todo, index, completeTodo, removeTodo }) {
  return (
    <Arrange
      fill={<span style={{ textDecoration: todo.isCompleted ? "line-through" : "" }}>{todo.text}</span>}
      fitEnd={
        <React.Fragment>
          <Spacer isInlineBlock marginLeft="small">
            <Button
              onClick={() => completeTodo(index)}
              variant="neutral"
              text="Done"
            />
          </Spacer>
          <Spacer isInlineBlock marginLeft="small">
            <Button
              onClick={() => removeTodo(index)}
              variant="neutral"
              text="Remove"
            />
          </Spacer>
        </React.Fragment>
      }
    />
  );
}
```

You’ll see as well that we are bringing in removeTodo at the top and then using it in the onClick of the “Remove”.

Adding in the `removeTodo={removeTodo}` in the Todo part of the returning the App component, our “delete” will be fully functional. Add it in here:

```jsx
src/App.js
<Todo
    key={index}
    index={index}
    todo={todo}
    completeTodo={completeTodo}
    removeTodo={removeTodo}
/>
```

With that added in, go to your browser and you’ll see a button with an "Remove" that when clicked, deletes the item completely.


## The Final Terra UI Todo App
The entire `src/App.js` file should look like this in the end:

```jsx
import React, { useState } from 'react';
import Base from 'terra-base';
import Button from "terra-button";
import Spacer from "terra-spacer";
import InputField from "terra-form-input/lib/InputField";
import Arrange from "terra-arrange";
import logo from './logo.svg';
import './App.css';

function Todo({ todo, index, completeTodo, removeTodo }) {
  return (
    <Arrange
      fill={<span style={{ textDecoration: todo.isCompleted ? "line-through" : "" }}>{todo.text}</span>}
      fitEnd={
        <React.Fragment>
          <Spacer isInlineBlock marginLeft="small">
            <Button
              onClick={() => completeTodo(index)}
              variant="neutral"
              text="Done"
            />
          </Spacer>
          <Spacer isInlineBlock marginLeft="small">
            <Button
              onClick={() => removeTodo(index)}
              variant="neutral"
              text="Remove"
            />
          </Spacer>
        </React.Fragment>
      }
    />
  );
}

function TodoForm({ addTodo }) {
  const [value, setValue] = useState("");

  const handleSubmit = e => {
    e.preventDefault();
    if (!value) return;
    addTodo(value);
    setValue("");
  };

  return (
    <form onSubmit={handleSubmit}>
      <InputField
        inputId="todoInput"
        name="todo"
        value={value}
        label="Enter A To Do"
        onChange={e => setValue(e.target.value)}
      />
    </form>
  );
}

function App() {
  const [todos, setTodos] = useState([
    {
      text: "Learn about React",
      isCompleted: false
    },
    {
      text: "Meet friend for lunch",
      isCompleted: false
    },
    {
      text: "Build really cool todo app",
      isCompleted: false
    }
  ]);

  const addTodo = text => {
    const newTodos = [...todos, { text }];
    setTodos(newTodos);
  };

  const completeTodo = index => {
    const newTodos = [...todos];
    newTodos[index].isCompleted = !newTodos[index].isCompleted;
    setTodos(newTodos);
  };

  const removeTodo = index => {
    const newTodos = [...todos];
    newTodos.splice(index, 1);
    setTodos(newTodos);
  };

  return (
    <Base locale="en">
      <TodoForm addTodo={addTodo} />
      <div>
        {todos.map((todo, index) => (
          <Todo
            key={index}
            index={index}
            todo={todo}
            completeTodo={completeTodo}
            removeTodo={removeTodo}
          />
        ))}
      </div>
    </Base>
  );
}

export default App;

```
