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
