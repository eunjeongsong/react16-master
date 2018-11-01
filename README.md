# React 16

### return types

- 이전에는 return component or null.
- React 16 에서는 String 리턴 가능.
- Fragment 태그를 사용해서 필요없는 span 태그의 사용을 없앰.

### portals

- React 는 div 를 찾아서 마운트함.
- 마운트된 div 이외의 영역을 변경하고 싶을 때.

### Error Boundaries

- 컴포넌트로 하여금 자식의 에러를 관리할 수 있게함.
- 자식의 에러에만 한정으로 관리 가능하기 때문에 portals 의 에러는 캐치불가.

```html
<body>
  <noscript>
    You need to enable JavaScript to run this app.
  </noscript>
  <header>
    <h1>Can't touch this</h1>
    <span id="touchme"></span>
  </header>
  <div id="root"></div>
  <!--
      This HTML file is a template.
      If you open it directly in the browser, you will see an empty page.

      You can add webfonts, meta tags, or analytics to this file.
      The build step will place the bundled scripts into the <body> tag.

      To begin the development, run `npm start` or `yarn start`.
      To create a production bundle, use `npm run build` or `yarn build`.
    -->
</body>
```

```js
import React, { Component, Fragment } from "react";
import { createPortal } from "react-dom";

const BoundaryHOC = ProtectedComponent =>
  class BoundaryHOC extends Component {
    state = {
      hasError: false
    };
    componentDidCatch = () => {
      this.setState({
        hasError: true
      });
    };
    render() {
      const { hasError } = this.state;
      if (hasError) {
        return <ErrorFallback />;
      } else {
        return <ProtectedComponent />;
      }
    }
  };

class ErrorMaker extends Component {
  state = {
    friends: ["song", "eun", "jeong"]
  };
  componentDidMount = () => {
    setTimeout(() => {
      this.setState({
        friends: undefined
      });
    }, 2000);
  };
  render() {
    const { friends } = this.state;
    return friends.map(friend => ` ${friend} `);
  }
}

const PErrorMaker = BoundaryHOC(ErrorMaker);

class Portals extends Component {
  render() {
    return createPortal(<Message />, document.getElementById("touchme"));
  }
}

const Message = () => "Just touched it!";

const PPortals = BoundaryHOC(Portals);

class ReturnTypes extends Component {
  render() {
    return "hello ";
  }
}

const ErrorFallback = () => "Sorry something went wrong";

class App extends Component {
  render() {
    return (
      <Fragment>
        <ReturnTypes />
        <PPortals />
        <PErrorMaker />
      </Fragment>
    );
  }
}

export default App;
```

### this.state = null

- set State null 을 이용하면, 컴포넌트의 업데이트 시점을 조작 가능.
- set State Function 을 아래와 같이 따로 코딩 가능.

```js
import React, { Component } from "react";

const MAX_PIZZAS = 20;

const eatPizza = (state, props) => {
  const { pizzas } = state;
  if (pizzas < MAX_PIZZAS) {
    return {
      pizzas: pizzas + 1
    };
  } else {
    return null;
  }
};

class Controlled extends Component {
  state = {
    pizzas: 0
  };
  render() {
    const { pizzas } = this.state;
    return (
      <button onClick={this._handleClick}>{`I have eaten ${pizzas} ${
        pizzas === 1 ? "pizza" : "pizzas"
      }`}</button>
    );
  }
  _handleClick = () => {
    this.setState(eatPizza);
  };
}

class App extends Component {
  render() {
    return <Controlled />;
  }
}

export default App;
```
