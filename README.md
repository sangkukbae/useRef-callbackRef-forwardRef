# useRef-callbackRef-forwardRef

**When to Use Refs**
* Managing focus, text selection, or media playback.
* Triggering imperative animations.
* Integrating with third-party DOM libraries.

:warning: Avoid using refs for anything that can be done declaratively. For example, instead of exposing open() and close() methods on a Dialog component, pass an isOpen prop to it.

## [UseRef](https://reactjs.org/docs/hooks-reference.html#useref)

```jsx
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` points to the mounted text input element
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```

**Is there something like instance variables?**

```jsx
function Timer() {
  const intervalRef = useRef();

  useEffect(() => {
    const id = setInterval(() => {
      // ...
    });
    intervalRef.current = id;
    return () => {
      clearInterval(intervalRef.current);
    };
  });

  // ...
}

// ...
  function handleCancelClick() {
    clearInterval(intervalRef.current);
  }
  // ...
```

<details><summary>createRef</summary>
  <p>

```jsx
class AutoFocusTextInput extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = React.createRef();
  }

  componentDidMount() {
    this.textInput.current.focusTextInput();
  }

  render() {
    return (
      <CustomTextInput ref={this.textInput} />
    );
  }
}
```

</p>
</details>

## [Callback Refs](https://reactjs.org/docs/refs-and-the-dom.html#callback-refs)
    
```jsx
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);

    this.textInput = null;

    this.setTextInputRef = element => {
      this.textInput = element;
    };

    this.focusTextInput = () => {
      // Focus the text input using the raw DOM API
      if (this.textInput) this.textInput.focus();
    };
  }
  componentDidMount() {
    // autofocus the input on mount
    this.focusTextInput();
  }

  render() {
    // Use the `ref` callback to store a reference to the text input DOM
    // element in an instance field (for example, this.textInput).
    return (
      <div>
        <input
          type="text"
          ref={this.setTextInputRef}
        />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}
        />
      </div>
    );
  }
}
    
```

```jsx
function CustomTextInput(props) {
  return (
    <div>
      <input ref={props.inputRef} />
    </div>
  );
}

class Parent extends React.Component {
  render() {
    return (
      <CustomTextInput
        inputRef={el => this.inputElement = el}
      />
    );
  }
}
```

**How can I measure a DOM node?**
```jsx
function MeasureExample() {
  const [height, setHeight] = useState(0);

  const measuredRef = useCallback(node => {
    if (node !== null) {
      setHeight(node.getBoundingClientRect().height);
    }
  }, []);

  return (
    <>
      <h1 ref={measuredRef}>Hello, world</h1>
      <h2>The above header is {Math.round(height)}px tall</h2>
    </>
  );
}
```
[https://codesandbox.io/s/react-callback-ref-l5e55](https://codesandbox.io/s/react-callback-ref-l5e55)

## [forwardRef](https://reactjs.org/docs/react-api.html#reactforwardref)

```jsx
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// You can now get a ref directly to the DOM button:
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;
```

**useImperativeHandle**   
`useImperativeHandle` customizes the instance value that is exposed to parent components when using ref. As always, imperative code using refs should be avoided in most cases. useImperativeHandle should be used with `forwardRef`.

```jsx
function FancyInput(props, ref) {
  const inputRef = useRef();
  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus();
    }
  }));
  return <input ref={inputRef} ... />;
}
FancyInput = forwardRef(FancyInput);
```

:memo: **참고 자료**   
* [https://medium.com/trabe/react-useref-hook-b6c9d39e2022](https://medium.com/trabe/react-useref-hook-b6c9d39e2022)   
* [https://blog.logrocket.com/a-guide-to-react-refs/](https://blog.logrocket.com/a-guide-to-react-refs/)   
* [https://www.youtube.com/watch?v=0ppuDPhgyrA](https://www.youtube.com/watch?v=0ppuDPhgyrA)   

