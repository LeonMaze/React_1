```jsx
import React, { useReducer, createContext, useContext, useEffect, useRef } from "react";
import "./styles.css";

function appReducer(state, action) {
  switch (action.type) {
    case "reset":
      return action.payload;
    case "add":
      return [
        ...state,
        {
          id: Date.now(),
          text: "",
          completed: false
        }
      ];
    case "delete":
      return state.filter((item) => item.id !== action.payload);
    case "completed":
      return state.map((item) => {
        if (item.id === action.payload) {
          return {
            ...item,
            completed: !item.completed
          };
        }
        return item;
      });
    default:
      return state;
  }
}

const Context = createContext();

export default function App() {
  const [state, dispatch] = useReducer(appReducer, []);

  useEffect(() => {
    const raw = localStorage.getItem("data");
    dispatch({ type: "reset", payload: JSON.parse(raw) });
  }, []);

  useEffect(() => {
    localStorage.setItem("data", JSON.stringify(state));
  }, [state]);

  return (
    <Context.Provider value={dispatch}>
      <div className="app">
        <h1>Advanced Hooks App</h1>
        <button onClick={() => dispatch({ type: "add" })}>Add Item</button>
        <ListItems items={state} />
      </div>
    </Context.Provider>
  );
}

function ListItems({ items }) {
  return (
    <div className="app__listItems">
      {items.map((item) => (
        <AppItem key={item.id} {...item} />
      ))}
    </div>
  );
}

function AppItem({ id, completed, text }) {
  const dispatch = useContext(Context);
  return (
    <div key={id}>
      <input
        type="checkbox"
        checked={completed}
        onChange={() => dispatch({ type: "completed", payload: id })}
      />
      <input type="text" defaultValue={text} />
      <button onClick={() => dispatch({ type: "delete", payload: id })}>
        Delete
      </button>
    </div>
  );
}

```
