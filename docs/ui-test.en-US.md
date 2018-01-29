---
order: 15
title: UI Test
type: 进阶
---

UI testing is an important part of the project development process. Effective test cases can sort out business needs and ensure the quality and progress of development, It make engineers can refactor the code and add new features without trepidation.

Ant Design Pro comes packaged with a handy set of easy-to-use React unit tests and E2E test framework to run test cases by running the following command at the project root.

```bash
$ npm run test:all  # Execute all the tests
```

<img src="https://gw.alipayobjects.com/zos/rmsportal/bNkhdMosBxuEhhKgnROo.png" width="700" />

Now we start a quick introduction to writing your business test cases in your project.

## Unit Tests

Unit tests are used to test the appearance of React UI components。We referenced [create-react-app](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#running-tests), using [jest](http://facebook.github.io/jest/) as a test framework.

jest is a node-side test framework that uses jsdom to simulate the DOM environment and is suitable for quick testing of the logical behavior of React components. If requiring a real browser, please refer to the E2E test section.

### Write a case

For example, we could create a file `src/routes/Result/Success.test.js` to test the UI appearance of successful page components.

```jsx
import React from 'react';
import { shallow } from 'enzyme';
import Success from './Success';   // Introduce the corresponding React component

it('renders with Result', () => {
  const wrapper = shallow(<Success />);                           // Rendering
  expect(wrapper.find('Result').length).toBe(1);                  // Has Result component
  expect(wrapper.find('Result').prop('type')).toBe('success');    // The type of the Result component is success
});
```

Here [enzyme](http://airbnb.io/enzyme/docs/api/index.html) is used as a test library, which provides a number of useful APIs to help us test React components. Assert part use the jest acquiescence [jasmine2 expect grammar](https://facebook.github.io/jest/docs/en/expect.html#content).

### Local execution

Use the following command will be search and execute `* .test.js` format test case file in `src`.

```bash
$ npm test .test.js
```

#### Executes a single or a set of cases

```bash
$ npm test src/routes/Result/Success.test.js  # test Success.test.js
$ npm test src/routes                         # test all test case file in routes
```

### Test dva wrapped components

Components wrapped by dva `connect` can use the following way to test.

```jsx
import React from 'react';
import { shallow } from 'enzyme';
import Dashboard from './Dashboard';

it('renders Dashboard', () => {
  // Use the wrapped components
  const wrapper = shallow(
    <Dashboard.WrappedComponent user={{ list: [] }} />
  );
  expect(wrapper.find('Table').props().dataSource).toEqual([]);
});
```

## e2e test

End-to-end testing, also known as smoke testing, is used to test the process and appearance of front-end applications in a real-world browser environment, equivalent to operating application instead of manually.

We import [nightmare](http://www.nightmarejs.org/) as a tool for E2E testing, nightmare use electron as the browser environment by default to runing your application, and provides a very semantic API for describing business logic.

### Write a e2e test case

Assuming there is a demand, the user enters the wrong user name and password on the login page, and after clicking login, an error message box will appear.

<img src="https://gw.alipayobjects.com/zos/rmsportal/oZeYewGOUJkmqXAPoOFC.gif" width="400" />

We write a test case to ensure this process. Create a `Login.e2e.js` file in the `src/e2e/`directory and describe the test cases as described above for your business needs.

```js
import Nightmare from 'nightmare';

describe('Login', () => {
  it('should login with failure', async () => {
    await Nightmare().goto('http://localhost:8000/#/user/login')
      .type('#userName', 'mockuser')
      .type('#password', 'wrong_password')
      .click('button[type="submit"]')
      .wait('.ant-alert-error')  // should display error
      .end();
  });
});
```

More api of nightmare can refer to [segmentio/nightmare # api](https://github.com/segmentio/nightmare#api).

### Run test cases

Run the following command to execute all `*.e2e.js` test case files under src.

```bash
$ npm test .e2e.js
```

<img src="https://gw.alipayobjects.com/zos/rmsportal/LGCXopksUYMUhjRgdYSz.png" width="700" />

> Note: Test e2e use case locally needs to start `npm start`, otherwise it will report `Failed: navigation error` error.

## watch mode

```
$ npm test -- --watch
```

Add `--watch` configuration can enter the watch mode, when you modify and save the file, Jest will automatically execute the corresponding test cases. Jest's command line tools also provide a variety of convenient shortcuts to execute the use cases you need.

<img src="https://gw.alipayobjects.com/zos/rmsportal/MnmxiavystfcBDskyKRg.png" width="700" />

## Test coverage

```
$ npm test -- --coverage
```

Add a `--coverage` configuration to display the project's test coverage.

<img src="https://camo.githubusercontent.com/bd0bbda8e44ea747e4c199d0e212d40563ad2fcb/687474703a2f2f692e696d6775722e636f6d2f356246686e54532e706e67" width="700" />

## Focus or ignore use cases

Use `xit()` instead of `it()` to temporarily ignore the test case, `fit()` to focus on the current test case and ignore all other test cases. These two methods can help you to focus on the current need of test cases in the development process.

## Access integration testing services

If you need access to integrated test environment such as travis, CircleCI, Gitlab CI, you can refer to `.travis.yml` provided by this repository.

Note that the e2e test requires integrated environment support, and if not, you can run unit tests separately using `npm test .test.js`.

## Reference link

For more testing tips and functions, please refer to the following link.

- [create-react-app tests](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#running-tests)
- [jest](https://facebook.github.io/jest/)
- [enzyme](http://airbnb.io/enzyme/)
- [nightmare](http://www.nightmarejs.org/)
- [jest-with-nightmare](https://github.com/vigetlabs/jest-with-nightmare)
