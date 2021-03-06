# React + Storybook Configuration and Setup

> A guide that will help you to setup and run React + Storybook configuration.

![react](https://img.shields.io/badge/react-v16.5.2-blue.svg)
![storybook](https://img.shields.io/badge/storybook-v4.0.0@alpha24-ff69b4.svg)

- A quick way:

  - Clone the repo:

  ```sh
  git clone git@github.com:IvaTsu/react-storybook-config.git
  ```

  - Install dependencies:

  ```sh
  yarn
  ```

  - Run Storybook on local server:

  ```sh
  yarn run storybook
  ```

---

- Manual configuration:

  - Create a needed folder:

  ```sh
  mkdir react-storybook-config
  ```

  So, now you should have such folder structure:

  ```
  react-storybook-config
  |--
  ```

  - Inside the `./react-storybook-config/` folder run

  ```sh
  yarn init -y
  ```

  This command creates a default `package.json` file. I will use `yarn` from this point and beyond.

- Let's start with adding dependencies:

  - `yarn add -D @storybook/react@4.0.0-alpha.24 @babel/core babel-loader` - it adds _@storybook/react_, _@babel/core_ and _babel-loader_ to your _dev dependencies_.

  - `yarn add react react-dom` - it adds _react_ and _react-dom_ to the _dependencies_.

  - Don't forger to add `node_modules/` to `.gitignore` file.

- Create 2 additional folders `.storybook` and `src` by running `mkdir .storybook src` command in your `root` folder.

- Create an empty config file for the _storybook_: `touch .storybook/config.js`. This file is a **required** one for storybook to run.

- Add script for further convenient use of storybook in the futute:

  - Go to the `package.json` file.
  - Add "scripts" there in the following way:

  ```json
  "scripts": {
      "storybook": "start-storybook -p 6006 -c .storybook"
  }
  ```

  - Here `-p` flag means port you want to run server on, `-c` flag means folder that contains config file.

  - Now wen you run command `yarn run storybook`, it starts a server at localhost:6006 and you can navigate there to see the results.

- Add first _React Story_ to _Storybook_:

  - Go to the `.storybook/config.js` and import _configure_ method from _storybook_.

  ```
  import { configure } from "@storybook/react";
  ```

  - Define utility function that will look through everything in `./src` folder and use a RegExp to find files that will ends with `.stories.js`

  ```
  const req = require.context("../src/", true, /.stories.js$/);
  ```

  - Define a function `loadStories` in the following way:

  ```
  function loadStories() {
      req.keys().forEach(file => req(file));
  }
  ```

  - for the every file that is matched, it is needed to require the file to build the story.
  - Configure current function with the current module:

  ```
  configure(loadStories, module);
  ```

  - Create an example component: - Create `Button.js` file inside `src` folder: `touch ./src/Button.js`. - Create a basic component setup:

  ```
  import React from "react";

  export default ({ bg, title }) => (
      <button style={{ backgroundColor: bg }}>{ title }</button>
  );
  ```

  - Create a story file for the component:

  - Create file `Button.stories.js` right near with `Button.js`.

  ```
  import React from "react";
  import { storiesOf } from "@storybook/react";
  import Button from "./Button";

  storiesOf("Button", module).add("with background", () => (
      <Button bg="red" title="Button" />
  ));
  ```

- Add a Welcome Story Page

  - Create `welcomeStory.js` file inside `.storybook/` folder.
  - Import needed dependencies:

  ```
  import React from "react";
  import { storiesOf } from "@storybook/react";
  ```

  - Define the story:

  ```
  storiesOf("Welcome", module).add("to the Storybook!", () => (
      <h1>Welcome to the Storybook!</h1>
  ));
  ```

  - Add it to the `loadStory` method in `./.storybook/config.js`

    ```
    ...
    const WelcomeStory = require("./welcomeStory.js");

    function loadStories() {
        WelcomeStory;
        ...
    }

    ...
    ```

- Display component's JSX with the help of JSX Addon:

  - Instal `dev` dependencies:

  ```
  yarn add -D @storybook/addons@4.0.0-alpha.24 storybook-addon-jsx
  ```

  - Create a new file `addons.js` inside `.storybook/` folder:

  ```
  import "storybook-addon-jsx/register";
  ```

  internally it executes the registration function.

  - In `config.js` file import `setAddon` method and `storybook-addon-jsx`:

  ```
  import { configure, setAddon } from "@storybook/react";
  import JSXAddon from "storybook-addon-jsx";
  ```

  and pair them together:

  ```
  setAddon(JSXAddon);
  ```

  and now it is possible to use it in any story in the following way:

  ```
  storiesOf("Button", module).addWithJSX("with background", () => (
      <Button bg="red" title="Button" />
  ));
  ```

  `.addWithJSX` will display the whole component structore at the storybook.

* Turn Story into documentation with Info Addon.

  - Install `dev` dependencies: `yarn add -D @storybook/addon-info@4.0.0-alpha.24`
  - Import addon to the Story you need:

  ```
  import { withInfo } from "@storybook/addon-info";
  ```

  - Add withInfo to your component:

  ```
  storiesOf("Button", module).addWithJSX(
      "with background",
      withInfo("description of the Button component")(
          () => (<Button bg="red" title="Button" />)
      )
  );
  ```

  At the Storybook you will see "Show info" button. After clicking on it, there will be description you wrote into `withInfo`. `withInfo` supports `markdown`, so it is possible to style the description.
  It is also possible to style `withInfo` by passing there an object like that:

  ```
  withInfo({
      styles: {
          header: {
              h1: {
                  color: 'red',
              },
          },
      },
      text: `
          description goes here
      `
  })
  ```

  It is not necessarily needed to define inline styles every time.
  It is possible create a utilities file and create some default stylings for your Storybook: - Create `utils.js` file inside `.storybook/` folder.

  ```
  import { withInfo } from "@storybook/addon-info";

      const withInfoStyle = {
          header: {
              h1: {
                  marginRight: "2rem",
                  fontSize: "1.5rem",
                  display: "inline"
              },
              body: {
                  padding: 0
              },
              h2: {
                  display: "inline"
              }
          },
          infoBody: {
              backgroundColor: "#EEE"
          }
      };

      export const wInfo = text =>
          withInfo({ text, inline: true, source: false, styles: withInfoStyle });
  ```

  and then apply import `wInfo` to the component's story:

  ```
  import React from "react";
  import { storiesOf } from "@storybook/react";
  import { wInfo } from "./utils";
  import Button from "./Button";

  storiesOf("Button", module).addWithJSX(
      "with background",
      wInfo("description of the Button component")(() => (
          <Button bg="red" title="Button" />
      ))
  );
  ```

- Interactive Stories with Knobs Decorator:

  - Install `dev` dependency: `yarn add -D @storybook/addon-knobs`
  - Register it in `addons.js`:

  ```
  ...
  import "@storybook/addon-knobs/register";
  ```

  - Import `addDecorator` from `@storybook/react` in `config.js` file.

  ```
  import { configure, setAddon, addDecorator } from "@storybook/react";
  ```

  - Import `withKnobs` in `config.js` file and register it.

  ```
  ...
  import { withKnobs } from "@storybook/addon-knobs/react";
  ...
  addDecorator(withKnobs);
  ```

  - Go to the Story, import appropriate `knob`:

  ```
  import React from "react";
  import { storiesOf } from "@storybook/react";
  import { text } from "@storybook/addon-knobs/react";
  import { wInfo } from "./utils";
  import Button from "./Button";

  storiesOf("Button", module).addWithJSX(
      "with background",
      wInfo("description of the Button component")(() => (
          <Button bg={text("bg", "red")} title="Button" />
      ))
  );
  ```

  Now it is alow to change a color using `"Knobs"` tab.

That's it. Enjoy!

## References:

- [Storybook Docs](https://storybook.js.org/basics/guide-react/)
- [React-Storybook Config](https://github.com/IvaTsu/react-storybook-config)
