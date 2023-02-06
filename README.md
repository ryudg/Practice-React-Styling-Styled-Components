# 1. Styled-Components

> styled-components란 Javascript 파일 내에서 CSS를 사용할 수 있게 해주는 대표적인 CSS-in-JS 라이브러리

# 2. CSS in JS

- 스타일 정의를 CSS 파일이 아닌 JavaScript로 작성된 컴포넌트에 바로 삽입하는 스타일 기법
- 기존에 웹사이트를 개발할 때는 HTML과 CSS, JavaScript는 각자 별도의 파일에 두는 것이 best practice로 여겨졌지만 최근에는 웹 애플리케이션을 여러 개의 재활용이 가능한 빌딩 블록으로 분리하여 개발하는 컴포넌트 기반 개발 방법이 주류가 됨
- 따라서, 웹페이지를 HTML, CSS, JavaScript 3개로 분리하는 것이 아니라, 여러 개의 컴포넌트로 분리하고, 각 컴포넌트에 HTML, CSS, JavaScript를 모두 작성하는 패턴이 많이 사용됨

# 3. Tagged Template Literal

> **Template literals**
> 내장된 표현식을 허용하는 문자열 리터럴

```javascript
const name = "react";
const message = `hello ${name}`;
console.log(message); // "hello react"

//${} 안에 일반 문자열/숫자가 아닌 객체를 넣을 경우
const obj = { a: 1 };
const text = `${obf}`;
console.log(text); // "[Object object]"

// ${} 안에 일반 문자열/숫자가 아닌 함수를 넣을 경우
const func = () => true;
const msg = `${func}`;
console.log(msg); // "() => true"
```

- Template Literal 을 사용하면서 내부에 넣은 자바스크립트 값을 조회하고 싶을 때는, <br>
  `Tagged Template Literal` 문법을 사용할 수 있다.

```javascript
const red = "빨간색";
const blue = "파란색";
function favoriteColors(texts, ...values) {
  console.log(texts);
  console.log(values);
}
favoriteColors`제가 좋아하는 색은 ${red}과 ${blue}입니다.`;
// ["제가 좋아하는 색은","과","입니다."]
// ["빨간색","파란색"]
```

> 함수 파라미터에서는 파라미터의 rest 문법을 사용 <br>
> 입력한 문자열이 모두 분해되어서, 넣어준 텍스트와 `${}`를 통해 넣어준 자바스크립트 값을 따로 따로 볼 수 있다.

```javascript
const red = "빨간색";
const blue = "파란색";
function favoriteColors(texts, ...values) {
  return texts.reduce(
    (result, text, i) =>
      `${result}${text}${values[i] ? `<b>${values[i]}</b>` : ""}`,
    ""
  );
}
favoriteColors`제가 좋아하는 색은 ${red}과 ${blue}입니다.`;
// 제가 좋아하는 색은 <b>빨간색</b>과 <b>파란색</b>입니다.
```

- styled-components 에서는 이런 문법을 사용해서 컴포넌트의 props 를 읽어옴

```javascript
const StyledDiv = styled`
  background: ${(props) => props.color};
`;
```

- Tagged Template Literal 을 사용해서, <br>
  만약 `${}` 을 통하여 함수를 넣어줬다면, 해당 함수를 사용해줄 수 있다.

```javascript
function sample(texts, ...fns) {
  const mockProps = {
    title: "안녕하세요",
    body: "내용은 내용 입니다.",
  };
  return texts.reduce(
    (result, text, i) => `${result}${text}${fns[i] ? fns[i](mockProps) : ""}`,
    ""
  );
}
sample`
 제목: ${(props) => props.title}
 내용: ${(props) => props.body}
`;
/*
"
 제목: 안녕하세요
 내용: 내용은 내용 입니다.
"
*/
```

# 4. styled-components 사용

## 4.1 Install

```bash
> npm i styled-components
```

### 4.1.1 Import

```javascript
import styled from 'styled-components';
...
```

## 4.2 Usage

```javascript
// App.js

import React from "react";
import styled from "styled-components";

const Circle = styled.div`
  width: 5rem;
  height: 5rem;
  background: black;
  border-radius: 50%;
`;

function App() {
  return <Circle />;
}

export default App;
```

- styled-components 를 사용하면 이렇게 스타일을 입력함과 동시에 해당 스타일을 가진 컴포넌트를 만들 수 있다.

<br>

- Circle에 props 전달
  - color props 값을 설정해줬으면 해당 값을 배경색으로 설정하고, 그렇지 않으면 빨간색을 배경색으로

```javascript
// App.js

import React from "react";
import styled from "styled-components";

const Circle = styled.div`
  width: 5rem;
  height: 5rem;
  background: ${(props) => props.color || "red"}
  border-radius: 50%;
`;

function App() {
  return <Circle color="blue" />;
}

export default App;
```

- Circle에 props 전달
  - huge 라는 props 를 설정됐을 때 크기를 키우기
  - 여러 줄의 CSS 코드를 조건부로 보여주고 싶다면 css 를 사용
  - ss 를 불러와서 사용을 해야 그 스타일 내부에서도 다른 props 를 조회
  ```javascript
  import styled, { css } from "styled-components";
  ```

```javascript
// App.js

import React from "react";
import styled, { css } from "styled-components";

const Circle = styled.div`
  width: 5rem;
  height: 5rem;
  background: ${(props) => props.color || "black"};
  border-radius: 50%;
  ${(props) =>
    props.huge &&
    css`
      width: 10rem;
      height: 10rem;
    `}
`;

function App() {
  return <Circle color="red" huge />;
}

export default App;
```

# 5. Button 만들기

```javascript
// components/Button.js

import React from "react";
import styled from "styled-components";

const StyledButton = styled.button`
  /* 공통 스타일 */
  display: inline-flex;
  outline: none;
  border: none;
  border-radius: 4px;
  color: white;
  font-weight: bold;
  cursor: pointer;
  padding-left: 1rem;
  padding-right: 1rem;

  /* 크기 */
  height: 2.25rem;
  font-size: 1rem;

  /* 색상 */
  background: #228be6;
  &:hover {
    background: #339af0;
  }
  &:active {
    background: #1c7ed6;
  }

  /* 기타 */
  & + & {
    margin-left: 1rem;
  }
`;

function Button({ children, ...rest }) {
  return <StyledButton {...rest}>{children}</StyledButton>;
}

export default Button;
```

- App.js에서 Button 컴포넌트 사용

```javascript
// App.js

import React from "react";
import styled from "styled-components";
import Button from "./components/Button";

const AppBlock = styled.div`
  width: 512px;
  margin: 0 auto;
  margin-top: 4rem;
  border: 1px solid black;
  padding: 1rem;
`;

function App() {
  return (
    <AppBlock>
      <Button>BUTTON</Button>
    </AppBlock>
  );
}

export default App;
```

## 5.1 `polished`의 스타일 관련 유틸 함수

- Sass 를 사용 할 때에는 `lighten()` 또는 `darken()` 과 같은 유틸 함수를 사용하여 색상에 변화를 줄 수 있었다
- CSS in JS 에서도 비슷한 유틸 함수를 사용하고 싶다면 [polished](https://polished.js.org/docs/) 라는 라이브러리를 사용

### 5.1.1 Install

```bash
> npm i polished
```

### 5.1.2 Import & Usage

```javascript
import { lighten, modularScale } from "polished";
```

## 5.2 `polished`사용 styling

```javascript
// components/Button.js

import React from "react";
import styled from "styled-components";
import { darken, lighten } from "polished";

const StyledButton = styled.button`
  /* 공통 스타일 */
  display: inline-flex;
  outline: none;
  border: none;
  border-radius: 4px;
  color: white;
  font-weight: bold;
  cursor: pointer;
  padding-left: 1rem;
  padding-right: 1rem;

  /* 크기 */
  height: 2.25rem;
  font-size: 1rem;

  /* 색상 */
  background: #228be6;
  &:hover {
    background: ${lighten(0.1, "#228be6")};
  }
  &:active {
    background: ${darken(0.1, "#228be6")};
  }

  /* 기타 */
  & + & {
    margin-left: 1rem;
  }
`;

function Button({ children, ...rest }) {
  return <StyledButton {...rest}>{children}</StyledButton>;
}

export default Button;
```

## 5.3 styled-components의 ThemeProvider

> 테마를 적용하는 helper 컴포넌트. Context API를 통해 컴포넌트 트리의 어떤 곳에서든 스타일된 컴포넌트에 테마를 주입.

- 색상 코드를 지닌 변수를 Button.js 에서 선언을 하는 대신 `ThemeProvider`을 사용하여 styled-components 로 만드는 모든 컴포넌트에서 조회하여 사용 할 수 있는 전역적인 값을 설정.

```javascript
// App.js

import React from "react";
import styled, { ThemeProvider } from "styled-components";
import Button from "./components/Button";

const AppBlock = styled.div`
  width: 512px;
  margin: 0 auto;
  margin-top: 4rem;
  border: 1px solid black;
  padding: 1rem;
`;

function App() {
  return (
    <ThemeProvider
      theme={{
        palette: {
          blue: "#228be6",
          gray: "#495057",
          pink: "#f06595",
        },
      }}
    >
      <AppBlock>
        <Button>BUTTON</Button>
      </AppBlock>
    </ThemeProvider>
  );
}

export default App;
```

- `theme` 을 설정하면 `ThemeProvider` 내부에 렌더링된 styled-components 로 만든 컴포넌트에서 `palette` 를 조회하여 사용 할 수 있다.
  <br><br>

- Button 컴포넌트에 palette.blue 값을 조회

```javascript
// components/Button.js

import React from "react";
import styled, { css } from "styled-components";
import { darken, lighten } from "polished";

const StyledButton = styled.button`
  /* 공통 스타일 */
  display: inline-flex;
  outline: none;
  border: none;
  border-radius: 4px;
  color: white;
  font-weight: bold;
  cursor: pointer;
  padding-left: 1rem;
  padding-right: 1rem;

  /* 크기 */
  height: 2.25rem;
  font-size: 1rem;

  /* 색상 */
  ${(props) => {
    const selected = props.theme.palette.blue;
    return css`
      background: ${selected};
      &:hover {
        background: ${lighten(0.1, selected)};
      }
      &:active {
        background: ${darken(0.1, selected)};
      }
    `;
  }}

  /* 기타 */
  & + & {
    margin-left: 1rem;
  }
`;

function Button({ children, ...rest }) {
  return <StyledButton {...rest}>{children}</StyledButton>;
}

export default Button;
```

- `ThemeProvider` 로 설정한 값은 styled-components 에서 `props.theme` 로 조회 할 수 있다.
  <br><br>

- selected 값을 무조건 blue 값을 가르키게 했는데, 이를 Button 컴포넌트가 `color` props 를 를 통하여 받아오게 될 색상을 사용하도록 수정

```javascript
// components/Button.js

import React from "react";
import styled, { css } from "styled-components";
import { darken, lighten } from "polished";

const StyledButton = styled.button`
  /* 공통 스타일 */
  display: inline-flex;
  outline: none;
  border: none;
  border-radius: 4px;
  color: white;
  font-weight: bold;
  cursor: pointer;
  padding-left: 1rem;
  padding-right: 1rem;

  /* 크기 */
  height: 2.25rem;
  font-size: 1rem;

  /* 색상 */
  ${(props) => {
    const selected = props.theme.palette[props.color];
    return css`
      background: ${selected};
      &:hover {
        background: ${lighten(0.1, selected)};
      }
      &:active {
        background: ${darken(0.1, selected)};
      }
    `;
  }}

  /* 기타 */
  & + & {
    margin-left: 1rem;
  }
`;

function Button({ children, ...rest }) {
  return <StyledButton {...rest}>{children}</StyledButton>;
}

Button.defaultProps = {
  color: "blue",
};

export default Button;
```

- 기본 색상이 blue가 되도록 설정

<br><br>

- App 컴포넌트에서 gray와 pink 색 버튼 렌더링

```javascript
// App.js

import React from "react";
import styled, { ThemeProvider } from "styled-components";
import Button from "./components/Button";

const AppBlock = styled.div`
  width: 512px;
  margin: 0 auto;
  margin-top: 4rem;
  border: 1px solid black;
  padding: 1rem;
`;

function App() {
  return (
    <ThemeProvider
      theme={{
        palette: {
          blue: "#228be6",
          gray: "#495057",
          pink: "#f06595",
        },
      }}
    >
      <AppBlock>
        <Button>BUTTON</Button>
        <Button color="gray">BUTTON</Button>
        <Button color="pink">BUTTON</Button>
      </AppBlock>
    </ThemeProvider>
  );
}

export default App;
```

- Button 리팩토링
  - `props.theme.palette.blue`를 값을 조회하는 대신에 비구조화 할당 문법을 사용하여 가독성 높이기

```javascript
// components/Button.js
import React from "react";
import styled, { css } from "styled-components";
import { darken, lighten } from "polished";

const StyledButton = styled.button`
  /* 공통 스타일 */
  display: inline-flex;
  outline: none;
  border: none;
  border-radius: 4px;
  color: white;
  font-weight: bold;
  cursor: pointer;
  padding-left: 1rem;
  padding-right: 1rem;

  /* 크기 */
  height: 2.25rem;
  font-size: 1rem;

  /* 색상 */
  ${({ theme, color }) => {
    const selected = theme.palette[color];
    return css`
      background: ${selected};
      &:hover {
        background: ${lighten(0.1, selected)};
      }
      &:active {
        background: ${darken(0.1, selected)};
      }
    `;
  }}

  /* 기타 */
  & + & {
    margin-left: 1rem;
  }
`;

function Button({ children, color, ...rest }) {
  return (
    <StyledButton color={color} {...rest}>
      {children}
    </StyledButton>
  );
}

Button.defaultProps = {
  color: "blue",
};

export default Button;
```

<br><br>

## 5.4 색상에 관련된 코드를 분리

```javascript
// components/Button.js

import React from "react";
import styled, { css } from "styled-components";
import { darken, lighten } from "polished";

const colorStyles = css`
  ${({ theme, color }) => {
    const selected = theme.palette[color];
    return css`
      background: ${selected};
      &:hover {
        background: ${lighten(0.1, selected)};
      }
      &:active {
        background: ${darken(0.1, selected)};
      }
    `;
  }}
`;

const StyledButton = styled.button`
  /* 공통 스타일 */
  display: inline-flex;
  outline: none;
  border: none;
  border-radius: 4px;
  color: white;
  font-weight: bold;
  cursor: pointer;
  padding-left: 1rem;
  padding-right: 1rem;

  /* 크기 */
  height: 2.25rem;
  font-size: 1rem;

  /* 색상 */
  ${colorStyles}

  /* 기타 */
  & + & {
    margin-left: 1rem;
  }
`;

function Button({ children, color, ...rest }) {
  return (
    <StyledButton color={color} {...rest}>
      {children}
    </StyledButton>
  );
}

Button.defaultProps = {
  color: "blue",
};

export default Button;
```

## 5.5 `size` props 설정, 크기가 다양한 버튼 만들기

- `sizeStyles` 에 해당하는 코드를 따로 분리하지 않고 `StyledButton` 의 스타일 내부에 바로 적어도 상관은 없다. 다만, 이렇게 분리해두면 나중에 유지보수를 할 때 더 편해질 수 있다.

```javascript
// components/Button.js

import React from "react";
import styled, { css } from "styled-components";
import { darken, lighten } from "polished";

const colorStyles = css`
  ${({ theme, color }) => {
    const selected = theme.palette[color];
    return css`
      background: ${selected};
      &:hover {
        background: ${lighten(0.1, selected)};
      }
      &:active {
        background: ${darken(0.1, selected)};
      }
    `;
  }}
`;

const sizeStyles = css`
  ${(props) =>
    props.size === "large" &&
    css`
      height: 3rem;
      font-size: 1.25rem;
    `}

  ${(props) =>
    props.size === "medium" &&
    css`
      height: 2.25rem;
      font-size: 1rem;
    `}

    ${(props) =>
    props.size === "small" &&
    css`
      height: 1.75rem;
      font-size: 0.875rem;
    `}
`;

const StyledButton = styled.button`
  /* 공통 스타일 */
  display: inline-flex;
  outline: none;
  border: none;
  border-radius: 4px;
  color: white;
  font-weight: bold;
  cursor: pointer;
  padding-left: 1rem;
  padding-right: 1rem;

  /* 크기 */
  ${sizeStyles}

  /* 색상 */
  ${colorStyles}

  /* 기타 */
  & + & {
    margin-left: 1rem;
  }
`;

function Button({ children, color, size, ...rest }) {
  return (
    <StyledButton color={color} size={size} {...rest}>
      {children}
    </StyledButton>
  );
}

Button.defaultProps = {
  color: "blue",
  size: "medium",
};

export default Button;
```

- App 컴포넌트에서 버튼 랜더링

```javascript
import React from "react";
import styled, { ThemeProvider } from "styled-components";
import Button from "./components/Button";

const AppBlock = styled.div`
  width: 512px;
  margin: 0 auto;
  margin-top: 4rem;
  border: 1px solid black;
  padding: 1rem;
`;

const ButtonGroup = styled.div`
  & + & {
    margin-top: 1rem;
  }
`;

function App() {
  return (
    <ThemeProvider
      theme={{
        palette: {
          blue: "#228be6",
          gray: "#495057",
          pink: "#f06595",
        },
      }}
    >
      <AppBlock>
        <ButtonGroup>
          <Button size="large">BUTTON</Button>
          <Button>BUTTON</Button>
          <Button size="small">BUTTON</Button>
        </ButtonGroup>
        <ButtonGroup>
          <Button color="gray" size="large">
            BUTTON
          </Button>
          <Button color="gray">BUTTON</Button>
          <Button color="gray" size="small">
            BUTTON
          </Button>
        </ButtonGroup>
        <ButtonGroup>
          <Button color="pink" size="large">
            BUTTON
          </Button>
          <Button color="pink">BUTTON</Button>
          <Button color="pink" size="small">
            BUTTON
          </Button>
        </ButtonGroup>
      </AppBlock>
    </ThemeProvider>
  );
}

export default App;
```

- `sizeStyles` 리팩토링

```javascript
// components/Button.js

import React from "react";
import styled, { css } from "styled-components";
import { darken, lighten } from "polished";

const colorStyles = css`
  ${({ theme, color }) => {
    const selected = theme.palette[color];
    return css`
      background: ${selected};
      &:hover {
        background: ${lighten(0.1, selected)};
      }
      &:active {
        background: ${darken(0.1, selected)};
      }
    `;
  }}
`;

const sizes = {
  large: {
    height: "3rem",
    fontSize: "1.25rem",
  },
  medium: {
    height: "2.25rem",
    fontSize: "1rem",
  },
  small: {
    height: "1.75rem",
    fontSize: "0.875rem",
  },
};

const sizeStyles = css`
  ${({ size }) => css`
    height: ${sizes[size].height};
    font-size: ${sizes[size].fontSize};
  `}
`;

const StyledButton = styled.button`
  /* 공통 스타일 */
  display: inline-flex;
  outline: none;
  border: none;
  border-radius: 4px;
  color: white;
  font-weight: bold;
  cursor: pointer;
  padding-left: 1rem;
  padding-right: 1rem;

  /* 크기 */
  ${sizeStyles}

  /* 색상 */
  ${colorStyles}

  /* 기타 */
  & + & {
    margin-left: 1rem;
  }
`;

function Button({ children, color, size, ...rest }) {
  return (
    <StyledButton color={color} size={size} {...rest}>
      {children}
    </StyledButton>
  );
}

Button.defaultProps = {
  color: "blue",
  size: "medium",
};

export default Button;
```

## 5.5 `outline` 테투리 설정

- Button 컴포넌트에 `outline` 이라는 `props` 를 설정하여 이 값이 `true` 일 때에는 테두리만 지닌 버튼을 보여주도록 설정

```javascript
// components/Button.js

import React from "react";
import styled, { css } from "styled-components";
import { darken, lighten } from "polished";

const colorStyles = css`
  ${({ theme, color }) => {
    const selected = theme.palette[color];
    return css`
      background: ${selected};
      &:hover {
        background: ${lighten(0.1, selected)};
      }
      &:active {
        background: ${darken(0.1, selected)};
      }
      ${(props) =>
        props.outline &&
        css`
          color: ${selected};
          background: none;
          border: 1px solid ${selected};
          &:hover {
            background: ${selected};
            color: white;
          }
        `}
    `;
  }}
`;

const sizes = {
  large: {
    height: "3rem",
    fontSize: "1.25rem",
  },
  medium: {
    height: "2.25rem",
    fontSize: "1rem",
  },
  small: {
    height: "1.75rem",
    fontSize: "0.875rem",
  },
};

const sizeStyles = css`
  ${({ size }) => css`
    height: ${sizes[size].height};
    font-size: ${sizes[size].fontSize};
  `}
`;

const StyledButton = styled.button`
  /* 공통 스타일 */
  display: inline-flex;
  outline: none;
  border: none;
  border-radius: 4px;
  color: white;
  font-weight: bold;
  cursor: pointer;
  padding-left: 1rem;
  padding-right: 1rem;

  /* 크기 */
  ${sizeStyles}

  /* 색상 */
  ${colorStyles}

  /* 기타 */
  & + & {
    margin-left: 1rem;
  }
`;

function Button({ children, color, size, outline, ...rest }) {
  return (
    <StyledButton color={color} size={size} outline={outline} {...rest}>
      {children}
    </StyledButton>
  );
}

Button.defaultProps = {
  color: "blue",
  size: "medium",
};

export default Button;
```

- `outline` 스타일을 가진 버튼 렌더링

```javascript
// App.js

import React from "react";
import styled, { ThemeProvider } from "styled-components";
import Button from "./components/Button";

const AppBlock = styled.div`
  width: 512px;
  margin: 0 auto;
  margin-top: 4rem;
  border: 1px solid black;
  padding: 1rem;
`;

const ButtonGroup = styled.div`
  & + & {
    margin-top: 1rem;
  }
`;

function App() {
  return (
    <ThemeProvider
      theme={{
        palette: {
          blue: "#228be6",
          gray: "#495057",
          pink: "#f06595",
        },
      }}
    >
      <AppBlock>
        <ButtonGroup>
          <Button size="large">BUTTON</Button>
          <Button>BUTTON</Button>
          <Button size="small">BUTTON</Button>
        </ButtonGroup>
        <ButtonGroup>
          <Button color="gray" size="large">
            BUTTON
          </Button>
          <Button color="gray">BUTTON</Button>
          <Button color="gray" size="small">
            BUTTON
          </Button>
        </ButtonGroup>
        <ButtonGroup>
          <Button color="pink" size="large">
            BUTTON
          </Button>
          <Button color="pink">BUTTON</Button>
          <Button color="pink" size="small">
            BUTTON
          </Button>
        </ButtonGroup>
        <ButtonGroup>
          <Button size="large" outline>
            BUTTON
          </Button>
          <Button color="gray" outline>
            BUTTON
          </Button>
          <Button color="pink" size="small" outline>
            BUTTON
          </Button>
        </ButtonGroup>
      </AppBlock>
    </ThemeProvider>
  );
}

export default App;
```

## 5.6 `fullWidth ` 버튼

```javascript
// components/Button.js

import React from "react";
import styled, { css } from "styled-components";
import { darken, lighten } from "polished";

const colorStyles = css`
  ${({ theme, color }) => {
    const selected = theme.palette[color];
    return css`
      background: ${selected};
      &:hover {
        background: ${lighten(0.1, selected)};
      }
      &:active {
        background: ${darken(0.1, selected)};
      }
      ${(props) =>
        props.outline &&
        css`
          color: ${selected};
          background: none;
          border: 1px solid ${selected};
          &:hover {
            background: ${selected};
            color: white;
          }
        `}
    `;
  }}
`;

const sizes = {
  large: {
    height: "3rem",
    fontSize: "1.25rem",
  },
  medium: {
    height: "2.25rem",
    fontSize: "1rem",
  },
  small: {
    height: "1.75rem",
    fontSize: "0.875rem",
  },
};

const sizeStyles = css`
  ${({ size }) => css`
    height: ${sizes[size].height};
    font-size: ${sizes[size].fontSize};
  `}
`;

// const fullWidthStyle = css`
//   ${(props) =>
//     props.fullWidth &&
//     css`
//       width: 100%;
//       justify-content: center;
//       & + & {  // & + &
//         margin-top: 1rem;
//       }
//     `}
// `;
const fullWidthStyle = css`
  ${(props) => {
    if (props.fullWidth && props.fullWidth === true)
      return css`
        width: 100%;
        justify-content: center;
        &.fullWidth + &.fullWidth {
          margin-left: 0;
          margin-top: 1rem;
        }
      `;
    else
      return css`
        & + & {
          margin-left: 1rem;
        `;
  }}
`;

const StyledButton = styled.button`
  // 공통스타일
  outline: none;
  border: none;
  border-radius: 4px;
  color: white;
  font-weight: bold;
  cursor: pointer;
  padding-left: 1rem;
  padding-right: 1rem;

  /* 크기 */
  ${sizeStyles}

  /* 색상 */
  ${colorStyles}

  /* fullWid */
  ${fullWidthStyle}
`;

function Button({ children, color, size, outline, fullWidth, ...rest }) {
  return (
    <StyledButton
      color={color}
      size={size}
      outline={outline}
      fullWidth={fullWidth}
      className={fullWidth ? "fullWidth" : ""}
      {...rest}
    >
      {children}
    </StyledButton>
  );
}

Button.defaultProps = {
  color: "blue",
  size: "medium",
};

export default Button;
```

- `fullWidth` 스타일 컴포넌트 렌더링

```javascript
// App.js

import React from "react";
import styled, { ThemeProvider } from "styled-components";
import Button from "./components/Button";

const AppBlock = styled.div`
  width: 512px;
  margin: 0 auto;
  margin-top: 4rem;
  border: 1px solid black;
  padding: 1rem;
`;

const ButtonGroup = styled.div`
  & + & {
    margin-top: 1rem;
  }
`;

function App() {
  return (
    <ThemeProvider
      theme={{
        palette: {
          blue: "#228be6",
          gray: "#495057",
          pink: "#f06595",
        },
      }}
    >
      <AppBlock>
        <ButtonGroup>
          <Button size="large">BUTTON</Button>
          <Button>BUTTON</Button>
          <Button size="small">BUTTON</Button>
        </ButtonGroup>
        <ButtonGroup>
          <Button color="gray" size="large">
            BUTTON
          </Button>
          <Button color="gray">BUTTON</Button>
          <Button color="gray" size="small">
            BUTTON
          </Button>
        </ButtonGroup>
        <ButtonGroup>
          <Button color="pink" size="large">
            BUTTON
          </Button>
          <Button color="pink">BUTTON</Button>
          <Button color="pink" size="small">
            BUTTON
          </Button>
        </ButtonGroup>
        <ButtonGroup>
          <Button size="large" outline>
            BUTTON
          </Button>
          <Button color="gray" outline>
            BUTTON
          </Button>
          <Button color="pink" size="small" outline>
            BUTTON
          </Button>
        </ButtonGroup>
        <ButtonGroup>
          <Button size="large" fullWidth>
            BUTTON
          </Button>
          <Button size="large" color="gray" fullWidth>
            BUTTON
          </Button>
          <Button size="large" color="pink" fullWidth>
            BUTTON
          </Button>
        </ButtonGroup>
      </AppBlock>
    </ThemeProvider>
  );
}

export default App;
```

# 6. Dialog 만들기

```javascript
// components/Dialog.js

import React from "react";
import styled from "styled-components";
import Button from "./Button";

const DarkBackground = styled.div`
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(0, 0, 0, 0.8);
`;

const DialogBlock = styled.div`
  width: 320px;
  padding: 1.5rem;
  background: white;
  border-radius: 2px;
  h3 {
    margin: 0;
    font-size: 1.5rem;
  }
  p {
    font-size: 1.125rem;
  }
`;

const ButtonGroup = styled.div`
  margin-top: 3rem;
  display: flex;
  justify-content: flex-end;
`;

function Dialog({ title, children, confirmText, cancelText }) {
  return (
    <DarkBackground>
      <DialogBlock>
        <h3>{title}</h3>
        <p>{children}</p>
        <ButtonGroup>
          <Button color="gray">{cancelText}</Button>
          <Button color="pink">{confirmText}</Button>
        </ButtonGroup>
      </DialogBlock>
    </DarkBackground>
  );
}

Dialog.defaultProps = {
  confirmText: "확인",
  cancelText: "취소",
};

export default Dialog;
```

> h3, 과 p 를 스타일링 때 굳이 아래와 같이 따로 따로 컴포넌트를 만들어주지 않아도
>
> ```javascript
> const Title = styled.h3``;
> const Description = styled.p``;
> ```
>
> styled-components 에서도 Nested CSS 문법을 사용 할 수 있기 때문에 DialogBlock 안에 있는 h3 와 p 에게 특정 스타일을 주고 싶다면 다음과 같이 작성할 수 있다.
>
> ```javascript
> const DialogBlock = styled.div`
>   h3 {
>     // ...
>   }
>   p {
>     // ...
>   }
> `;
> ```

- App 컴포넌트에 랜더링

```javascript
// App.js

import React from "react";
import styled, { ThemeProvider } from "styled-components";
import Button from "./components/Button";
import Dialog from "./components/Dialog";

const AppBlock = styled.div`
  width: 512px;
  margin: 0 auto;
  margin-top: 4rem;
  border: 1px solid black;
  padding: 1rem;
`;

const ButtonGroup = styled.div`
  & + & {
    margin-top: 1rem;
  }
`;

function App() {
  return (
    <ThemeProvider
      theme={{
        palette: {
          blue: "#228be6",
          gray: "#495057",
          pink: "#f06595",
        },
      }}
    >
      <>
        <AppBlock>
          <ButtonGroup>
            <Button size="large">BUTTON</Button>
            <Button>BUTTON</Button>
            <Button size="small">BUTTON</Button>
          </ButtonGroup>
          <ButtonGroup>
            <Button color="gray" size="large">
              BUTTON
            </Button>
            <Button color="gray">BUTTON</Button>
            <Button color="gray" size="small">
              BUTTON
            </Button>
          </ButtonGroup>
          <ButtonGroup>
            <Button color="pink" size="large">
              BUTTON
            </Button>
            <Button color="pink">BUTTON</Button>
            <Button color="pink" size="small">
              BUTTON
            </Button>
          </ButtonGroup>
          <ButtonGroup>
            <Button size="large" outline>
              BUTTON
            </Button>
            <Button color="gray" outline>
              BUTTON
            </Button>
            <Button color="pink" size="small" outline>
              BUTTON
            </Button>
          </ButtonGroup>
          <ButtonGroup>
            <Button size="large" fullWidth>
              BUTTON
            </Button>
            <Button size="large" color="gray" fullWidth>
              BUTTON
            </Button>
            <Button size="large" color="pink" fullWidth>
              BUTTON
            </Button>
          </ButtonGroup>
        </AppBlock>
        <Dialog
          title="정말로 삭제하시겠습니까?"
          confirmText="삭제"
          cancelText="취소"
        >
          데이터를 정말로 삭제하시겠습니까?
        </Dialog>
      </>
    </ThemeProvider>
  );
}

export default App;
```

- styled-components로 컴포넌트의 스타일을 특정 상황에서 덮어쓰는 방법
  (취소 삭제 여백 줄이기)

  - 컴포넌트의 스타일을 커스터마이징 할 때에는 해당 컴포넌트에서 `className` `props` 를 내부 엘리먼트에게 전달이 되고 있는지 확인

  ```javascript
  const MyComponent = ({ className }) => {
    return <div className={className}></div>;
  };

  const ExtendedComponent = styled(MyComponent)`
    background: black;
  `;
  ```

  - Button 컴포넌트의 경우에는 `...rest` 를 통하여 전달이 되고 있다

```javascript
// components/Dialog.js

import React from "react";
import styled from "styled-components";
import Button from "./Button";

const DarkBackground = styled.div`
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(0, 0, 0, 0.8);
`;

const DialogBlock = styled.div`
  width: 320px;
  padding: 1.5rem;
  background: white;
  border-radius: 2px;
  h3 {
    margin: 0;
    font-size: 1.5rem;
  }
  p {
    font-size: 1.125rem;
  }
`;

const ButtonGroup = styled.div`
  margin-top: 3rem;
  display: flex;
  justify-content: flex-end;
`;

const ShortMarginButton = styled(Button)`
  & + & {
    margin-left: 0.5rem;
  }
`;

function Dialog({ title, children, confirmText, cancelText }) {
  return (
    <DarkBackground>
      <DialogBlock>
        <h3>{title}</h3>
        <p>{children}</p>
        <ButtonGroup>
          <ShortMarginButton color="gray">{cancelText}</ShortMarginButton>
          <ShortMarginButton color="pink">{confirmText}</ShortMarginButton>
        </ButtonGroup>
      </DialogBlock>
    </DarkBackground>
  );
}

Dialog.defaultProps = {
  confirmText: "확인",
  cancelText: "취소",
};

export default Dialog;
```

- 열고 닫을 수 있는 기능을 구현
  - Dialog 에서 `onConfirm` 과 `onCancel` 을 `props` 로 받아오도록 하고 해당 함수들을 각 버튼들에게 `onClick` 으로 설정
  - visible props 도 받아와서 이 값이 false 일 때 컴포넌트에서 null 을 반환하도록 설정

```javascript
// components/Dialog.js

import React from "react";
import styled from "styled-components";
import Button from "./Button";

const DarkBackground = styled.div`
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(0, 0, 0, 0.8);
`;

const DialogBlock = styled.div`
  width: 320px;
  padding: 1.5rem;
  background: white;
  border-radius: 2px;
  h3 {
    margin: 0;
    font-size: 1.5rem;
  }
  p {
    font-size: 1.125rem;
  }
`;

const ButtonGroup = styled.div`
  margin-top: 3rem;
  display: flex;
  justify-content: flex-end;
`;

const ShortMarginButton = styled(Button)`
  & + & {
    margin-left: 0.5rem;
  }
`;

function Dialog({
  title,
  children,
  confirmText,
  cancelText,
  onConfirm,
  onCancel,
  visible,
}) {
  if (!visible) return null;
  return (
    <DarkBackground>
      <DialogBlock>
        <h3>{title}</h3>
        <p>{children}</p>
        <ButtonGroup>
          <ShortMarginButton color="gray" onClick={onCancel}>
            {cancelText}
          </ShortMarginButton>
          <ShortMarginButton color="pink" onClick={onConfirm}>
            {confirmText}
          </ShortMarginButton>
        </ButtonGroup>
      </DialogBlock>
    </DarkBackground>
  );
}

Dialog.defaultProps = {
  confirmText: "확인",
  cancelText: "취소",
};

export default Dialog;
```

- App 컴포넌트에서 visible 관리

```javascript
// App.js

import React, { useState } from "react";
import styled, { ThemeProvider } from "styled-components";
import Button from "./components/Button";
import Dialog from "./components/Dialog";

const AppBlock = styled.div`
  width: 512px;
  margin: 0 auto;
  margin-top: 4rem;
  border: 1px solid black;
  padding: 1rem;
`;

const ButtonGroup = styled.div`
  & + & {
    margin-top: 1rem;
  }
`;

function App() {
  const [dialog, setDialog] = useState(false);
  const onClick = () => {
    setDialog(true);
  };
  const onConfirm = () => {
    console.log("확인");
    setDialog(false);
  };
  const onCancel = () => {
    console.log("취소");
    setDialog(false);
  };

  return (
    <ThemeProvider
      theme={{
        palette: {
          blue: "#228be6",
          gray: "#495057",
          pink: "#f06595",
        },
      }}
    >
      <>
        <AppBlock>
          <ButtonGroup>
            <Button size="large">BUTTON</Button>
            <Button>BUTTON</Button>
            <Button size="small">BUTTON</Button>
          </ButtonGroup>
          <ButtonGroup>
            <Button color="gray" size="large">
              BUTTON
            </Button>
            <Button color="gray">BUTTON</Button>
            <Button color="gray" size="small">
              BUTTON
            </Button>
          </ButtonGroup>
          <ButtonGroup>
            <Button color="pink" size="large">
              BUTTON
            </Button>
            <Button color="pink">BUTTON</Button>
            <Button color="pink" size="small">
              BUTTON
            </Button>
          </ButtonGroup>
          <ButtonGroup>
            <Button size="large" outline>
              BUTTON
            </Button>
            <Button color="gray" outline>
              BUTTON
            </Button>
            <Button color="pink" size="small" outline>
              BUTTON
            </Button>
          </ButtonGroup>
          <ButtonGroup>
            <Button size="large" fullWidth>
              BUTTON
            </Button>
            <Button size="large" color="gray" fullWidth>
              BUTTON
            </Button>
            <Button size="large" color="pink" fullWidth onClick={onClick}>
              삭제
            </Button>
          </ButtonGroup>
        </AppBlock>
        <Dialog
          title="정말로 삭제하시겠습니까?"
          confirmText="삭제"
          cancelText="취소"
          onConfirm={onConfirm}
          onCancel={onCancel}
          visible={dialog}
        >
          데이터를 정말로 삭제하시겠습니까?
        </Dialog>
      </>
    </ThemeProvider>
  );
}

export default App;
```

# 7. Dialog 트랜지션 구현

- 트랜지션 효과를 적용 할 때에는 CSS Keyframe 을 사용하며, <br>
  styled-components 에서 이를 사용 할 때에는 keyframes 라는 유틸을 사용

```javascript
// components/Dialog.js

import React from "react";
import styled, { keyframes } from "styled-components";
import Button from "./Button";

const fadeIn = keyframes`
  from {
    opacity: 0
  }
  to {
    opacity: 1
  }
`;

const slideUp = keyframes`
  from {
    transform: translateY(200px);
  }
  to {
    transform: translateY(0px);
  }
`;

const DarkBackground = styled.div`
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(0, 0, 0, 0.8);

  animation-duration: 0.25s;
  animation-timing-function: ease-out;
  animation-name: ${fadeIn};
  animation-fill-mode: forwards;
`;

const DialogBlock = styled.div`
  width: 320px;
  padding: 1.5rem;
  background: white;
  border-radius: 2px;
  h3 {
    margin: 0;
    font-size: 1.5rem;
  }
  p {
    font-size: 1.125rem;
  }

  animation-duration: 0.25s;
  animation-timing-function: ease-out;
  animation-name: ${slideUp};
  animation-fill-mode: forwards;
`;

const ButtonGroup = styled.div`
  margin-top: 3rem;
  display: flex;
  justify-content: flex-end;
`;

const ShortMarginButton = styled(Button)`
  & + & {
    margin-left: 0.5rem;
  }
`;

function Dialog({
  title,
  children,
  confirmText,
  cancelText,
  onConfirm,
  onCancel,
  visible,
}) {
  if (!visible) return null;
  return (
    <DarkBackground>
      <DialogBlock>
        <h3>{title}</h3>
        <p>{children}</p>
        <ButtonGroup>
          <ShortMarginButton color="gray" onClick={onCancel}>
            {cancelText}
          </ShortMarginButton>
          <ShortMarginButton color="pink" onClick={onConfirm}>
            {confirmText}
          </ShortMarginButton>
        </ButtonGroup>
      </DialogBlock>
    </DarkBackground>
  );
}

Dialog.defaultProps = {
  confirmText: "확인",
  cancelText: "취소",
};

export default Dialog;
```

- 사라지는 트랜지션 효과 구현
  - 사라지는 효과를 구현하려면 Dialog 컴포넌트에서 두개의 로컬 상태를 관리해야함
  - 하나는 현재 트랜지션 효과를 보여주고 있는 중이라는 상태를 의미하는 `animate`
  - 나머지 하나는 실제로 컴포넌트가 사라지는 시점을 지연시키기 위한 `localVisible` 값
  - `useEffect` 를 사용해서 `visible` 값이 `true` 에서 `false` 로 바뀌는 시점을 감지하여 `animate` 값을 `true` 로 바꿔주고 `setTimeout` 함수를 사용하여 250ms 이후 `false로` 바꾸어 주어야
  - 추가적으로, `!visible` 조건에서 `null` 를 반환하는 대신에 `!animate && !localVisible` 조건에서 `null` 을 반환하도록 수정

```javascript
// components/Dialog.js

import React, { useState, useEffect } from "react";
import styled, { keyframes } from "styled-components";
import Button from "./Button";

const fadeIn = keyframes`
  from {
    opacity: 0
  }
  to {
    opacity: 1
  }
`;

const slideUp = keyframes`
  from {
    transform: translateY(200px);
  }
  to {
    transform: translateY(0px);
  }
`;

const DarkBackground = styled.div`
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(0, 0, 0, 0.8);

  animation-duration: 0.25s;
  animation-timing-function: ease-out;
  animation-name: ${fadeIn};
  animation-fill-mode: forwards;
`;

const DialogBlock = styled.div`
  width: 320px;
  padding: 1.5rem;
  background: white;
  border-radius: 2px;
  h3 {
    margin: 0;
    font-size: 1.5rem;
  }
  p {
    font-size: 1.125rem;
  }

  animation-duration: 0.25s;
  animation-timing-function: ease-out;
  animation-name: ${slideUp};
  animation-fill-mode: forwards;
`;

const ButtonGroup = styled.div`
  margin-top: 3rem;
  display: flex;
  justify-content: flex-end;
`;

const ShortMarginButton = styled(Button)`
  & + & {
    margin-left: 0.5rem;
  }
`;

function Dialog({
  title,
  children,
  confirmText,
  cancelText,
  onConfirm,
  onCancel,
  visible,
}) {
  const [animate, setAnimate] = useState(false);
  const [localVisible, setLocalVisible] = useState(visible);

  useEffect(() => {
    // visible 값이 true -> false 가 되는 것을 감지
    if (localVisible && !visible) {
      setAnimate(true);
      setTimeout(() => setAnimate(false), 250);
    }
    setLocalVisible(visible);
  }, [localVisible, visible]);

  if (!animate && !localVisible) return null;
  return (
    <DarkBackground>
      <DialogBlock>
        <h3>{title}</h3>
        <p>{children}</p>
        <ButtonGroup>
          <ShortMarginButton color="gray" onClick={onCancel}>
            {cancelText}
          </ShortMarginButton>
          <ShortMarginButton color="pink" onClick={onConfirm}>
            {confirmText}
          </ShortMarginButton>
        </ButtonGroup>
      </DialogBlock>
    </DarkBackground>
  );
}

Dialog.defaultProps = {
  confirmText: "확인",
  cancelText: "취소",
};

export default Dialog;
```

- "확인 / 취소"를 눌렀을 때 약간의 딜레이 이후에 Dialog 가 사라짐.

- 이제 DarkBackground 와 DialogBlock 에 `disappear` 라는 props 를 주어서 사라지는 효과가 나타나도록 설정.
  - 각 컴포넌트의 `disappear` 값은 `!visible` 로 하기

```javascript
// components/Dialog.js

import React, { useState, useEffect } from "react";
import styled, { keyframes, css } from "styled-components";
import Button from "./Button";

const fadeIn = keyframes`
  from {
    opacity: 0
  }
  to {
    opacity: 1
  }
`;

const fadeOut = keyframes`
  from {
    opacity: 1
  }
  to {
    opacity: 0
  }
`;

const slideUp = keyframes`
  from {
    transform: translateY(200px);
  }
  to {
    transform: translateY(0px);
  }
`;

const slideDown = keyframes`
  from {
    transform: translateY(0px);
  }
  to {
    transform: translateY(200px);
  }
`;

const DarkBackground = styled.div`
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(0, 0, 0, 0.8);

  animation-duration: 0.25s;
  animation-timing-function: ease-out;
  animation-name: ${fadeIn};
  animation-fill-mode: forwards;

  ${(props) =>
    props.disappear &&
    css`
      animation-name: ${fadeOut};
    `}
`;

const DialogBlock = styled.div`
  width: 320px;
  padding: 1.5rem;
  background: white;
  border-radius: 2px;
  h3 {
    margin: 0;
    font-size: 1.5rem;
  }
  p {
    font-size: 1.125rem;
  }

  animation-duration: 0.25s;
  animation-timing-function: ease-out;
  animation-name: ${slideUp};
  animation-fill-mode: forwards;

  ${(props) =>
    props.disappear &&
    css`
      animation-name: ${slideDown};
    `}
`;

const ButtonGroup = styled.div`
  margin-top: 3rem;
  display: flex;
  justify-content: flex-end;
`;

const ShortMarginButton = styled(Button)`
  & + & {
    margin-left: 0.5rem;
  }
`;

function Dialog({
  title,
  children,
  confirmText,
  cancelText,
  onConfirm,
  onCancel,
  visible,
}) {
  const [animate, setAnimate] = useState(false);
  const [localVisible, setLocalVisible] = useState(visible);

  useEffect(() => {
    // visible 값이 true -> false 가 되는 것을 감지
    if (localVisible && !visible) {
      setAnimate(true);
      setTimeout(() => setAnimate(false), 250);
    }
    setLocalVisible(visible);
  }, [localVisible, visible]);

  if (!animate && !localVisible) return null;
  return (
    <DarkBackground disappear={!visible}>
      <DialogBlock disappear={!visible}>
        <h3>{title}</h3>
        <p>{children}</p>
        <ButtonGroup>
          <ShortMarginButton color="gray" onClick={onCancel}>
            {cancelText}
          </ShortMarginButton>
          <ShortMarginButton color="pink" onClick={onConfirm}>
            {confirmText}
          </ShortMarginButton>
        </ButtonGroup>
      </DialogBlock>
    </DarkBackground>
  );
}

Dialog.defaultProps = {
  confirmText: "확인",
  cancelText: "취소",
};

export default Dialog;
```
