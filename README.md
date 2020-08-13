# React-Hooks

> - Released with `React 16.8`.
> - Lets the coder use state with functions.
> - Can only be called from the top level. Not permitted to use in **loops**, **nested functions** or **conditions**.
> - Can only be used with **functions**, not **classes**.
> - Has a `eslint` plugin to enforce the rules that is added by default with `create-react-app`.

## useState

simple use of `useState`

```javascript
import React, { useState } from 'react';

const App = () => {
	const [count, setCount] = useState(0);
	return (
		<div>
			<button onClick={() => setCount(value)}>btn 1</button>
		</div>
	);
};
```

we can also pass function to useState when initializing and updating

```javascript
import React, { useState } from 'react';

function expensiveComputation() {
	return 10;
}

const App = () => {
	const [count, setCount] = useState(() => expensiveComputation());
	return (
		<div>
			{/*in the function we have current state and return the new state */}
			<button onClick={() => setCount((currentVal) => currentVal + 1)}>
				btn 1
			</button>
		</div>
	);
};
```

updating objects

```javascript
import React, { useState } from 'react';
import './App.css';

function App() {
	const [form, handleChange] = useState({ email: '', password: '' });
	return (
		<div className='App'>
			<input
				type='text'
				value={form.email}
				onChange={(e) =>
					handleChange({
						...form,
						email: e.target.value,
					})
				}
				placeholder='email'
			/>

			<input
				type='password'
				value={form.password}
				onChange={(e) =>
					handleChange({
						...form,
						password: e.target.value,
					})
				}
				placeholder='password'
			/>
		</div>
	);
}

export default App;
```

using custom hooks

```javascript
import { useState } from 'react';

export const useForm = (initialValues) => {
	const [values, setValues] = useState(initialValues);

	return [
		values,
		(e) => {
			setValues({
				...values,
				[e.target.name]: e.target.value,
			});
		},
	];
};
```

```javascript
import React from 'react';
import { useForm } from './useForm';
import './App.css';

function App() {
	const [values, handleChange] = useForm({ email: '', password: '' });
	return (
		<div className='App'>
			<input
				type='text'
				name='email'
				value={values.email}
				onChange={handleChange}
				placeholder='email'
			/>

			<input
				type='password'
				name='password'
				value={values.password}
				onChange={handleChange}
				placeholder='password'
			/>
		</div>
	);
}

export default App;
```

## useEffect

> - By using this we tell that the component needs to do something after render.
> - This hook runs after each render.
> - Effects scheduled with useEffect don’t block the browser from updating the screen.
> - In order to clean up subscriptions or events like `onMouseMove` so that we can avoid memory leaks, we can use this hook.
> - We can use multiple `useEffect`. They will be executed in the order they are specified.

```javascript
useEffect(() => {
	// do something here
}, [the codes inside will run again if anyone of the variables inside this array changes]);
```

clean up things when the component gets unmounted

```javascript
import React, { useEffect } from 'react';

const Foo = () => {
	useEffect(() => {
		console.log('mounted');

		return () => {
			console.log('unmounted. do the cleanups here');
		};
	}, []);

	return <div>hi i am foo!!!</div>;
};

export default Foo;
```

multiple `useEffect`

```javascript
import React, { useEffect, useState } from 'react';
import './App.css';

function App() {
	const [count, setCount] = useState(0);
	const [count2, setCount2] = useState(0);

	useEffect(() => {
		console.log('render 1');
	}, [count]);

	useEffect(() => {
		console.log('render 2');
	}, [count2]);

	return (
		<div className='App'>
			<div>count1: {count}</div>
			<div>count2: {count2}</div>
			<button onClick={() => setCount((curr) => 1 + curr)}>count1</button>
			<button onClick={() => setCount2((curr) => 1 + curr)}>count2</button>
		</div>
	);
}

export default App;
```

## useRef

> - Returns a mutable `ref` object whose `.current` property is initialized with the passed arg.
> - The returned object will persist for the full lifetime of the component.
> - Mainly two uses: accessing dom nodes and keeping a mutable variable.
> - If we create a ref using `createRef` in a functional component, React will create a new instance of the ref on every re-render instead of keeping in between renders.
> - A ref created with useRef will be created only when the component has been mounted
> - `Unless you’re doing lazy initialization, avoid setting refs during rendering — this can lead to surprising behavior. Instead, typically you want to modify refs in event handlers and effects.`
> - Use it to manage focus, text selection or media playback, triggering animations or integrating with third-party DOM libraries.
> - When using as a mutable object, changing the object will not re-render the whole component.

accessing DOM

```javascript
import React, { useRef } from 'react';
import './App.css';

function App() {
	const text = useRef();

	const changeText = () => {
		text.current.style = 'font-size: 30px';
		text.current.appendChild(document.createTextNode('Water'));
	};

	return (
		<div>
			<div ref={text}>hello hoooks</div>
			<button onClick={changeText}>focus</button>
		</div>
	);
}

export default App;
```

## useReducer

> - An alternative to `useState`.
> - `useReducer` is usually preferable to useState when you have complex state logic that involves multiple sub-values or when the next state depends on the previous one
> - `useReducer` also lets you optimize performance for components that trigger deep updates because you can pass dispatch down instead of callbacks
> - If you return the same value from a Reducer Hook as the current state, React will bail out without rendering the children or firing effects.

```javascript
import React, { useReducer } from 'react';
import './App.css';

function reducer(state, action) {
	switch (action.type) {
		case 'increment':
			return state + 1;
		case 'decrement':
			return state - 1;
		default:
			return state;
	}
}

function App() {
	// function, initialstate
	const [count, dispatch] = useReducer(reducer, 0);

	return (
		<div>
			<div>count: {count}</div>
			<button onClick={() => dispatch({ type: 'increment' })}>+</button>
			<button onClick={() => dispatch({ type: 'decrement' })}>-</button>
		</div>
	);
}

export default App;
```

```javascript
import React, { useReducer, useState } from 'react';
import './App.css';

function reducer(state, action) {
	switch (action.type) {
		case 'add-todo':
			return {
				todos: [...state.todos, { text: action.payload, completed: false }],
			};
		case 'toggle':
			return {
				todos: state.todos.map((obj, idx) =>
					idx === action.payload ? { ...obj, completed: !obj.completed } : obj
				),
			};
		default:
			return state;
	}
}

function App() {
	// function, initialstate
	const [{ todos }, dispatch] = useReducer(reducer, { todos: [] });
	const [text, setText] = useState('');

	return (
		<div>
			<input
				placeholder='text'
				type='text'
				onChange={(e) => setText(e.target.value)}
				value={text}
			/>
			<button
				onClick={() => {
					dispatch({ type: 'add-todo', payload: text });
					setText('');
				}}
			>
				add
			</button>
			<h1>To-Do</h1>
			{todos &&
				todos.map((obj, idx) => (
					<>
						<p key={idx}>
							{obj.text}. completed: {obj.completed ? 'true' : 'false'}
						</p>
						<button onClick={() => dispatch({ type: 'toggle', payload: idx })}>
							mark as completed
						</button>
					</>
				))}
		</div>
	);
}

export default App;
```

## useContext

> - `const value = useContext(MyContext);` Accepts a context object(returned by `React.createContext`) and returns the current context value for that context.
> - The current context value is determined by the value prop of the nearest `<MyContext.Provider>` above the calling component in the tree.
