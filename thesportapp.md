# The Sports app

## Description

This project focuses on creating a user-friendly UI/UX using Reactjs and Ant Design for reusable components and using React big Calendar. It includes role-based authentication with mock APIs and state management implemented via Redux saga so no backend is attached only a FE is included in this project the Concept of this project was to Create FE for a Sports  Club Booking system for there Clients . The application is deployed and accessible at the following link:

[Access the Application](https://thesportapp.netlify.app/)

## Credentials for the App

### Admin Login
- **username:** admin
- **Password:** admin123

### club Login
- **Email:** club
- **Password:** club123

## Requirements

- node ^v16.16.0
- npm ^8.11.0

## Installed Dependencies

- Immer
- Axios
- Redux (with sagas)
- React icons
- React Router
- Classnames
- MomentJS
- Reselect
- Sass

## Documentation
- [React](https://reactjs.org/docs/getting-started.html)
- [Immer](https://immerjs.github.io/immer/)
- [Redux](https://redux.js.org/api/api-reference)
- [Sass](https://sass-lang.com/documentation/)
- [React Icons](https://react-icons.github.io/react-icons/)

## Installation

To run it locally you just need to clone the project after that do the follwoing steps:

1. in your terminal go to root directory of the project run:
```bash
$ npm install
```
## Running App

&nbsp; &nbsp; After installation step is complete, you're ready to start the project!

```bash
$ npm start
```
After that you can use follwoing login for the app which is using mock api:
```bash
username: admin
password: admin123
```

## Project Structure

&nbsp; &nbsp; This project uses Fractal project structure (also known as self-contained apps, recursive route hierarchy).
Instead of building applications using a flat directory structures, where everything is separated into folders such
as containers, components etc. which is not scalable, this structure allows the application to construct its own
architecture from the beginning. Rather than grouping functionality by file type, this structure groups it by feature.
The structure below represents the fractal structure and how it will be used for this project.


```
.
├── assets
|   ├── icons
|   ├── images
├── src
|   ├── components
|   |   ├── components
|   |   ├── App
|   |   |   └── index.js
|   |   |   └── App.jsx
|   |   |   └── Container.js (can be added if needed)
|   |   └── Component
|   |       └── View.jsx
|   |       └── Container.js
|   |       └── index.js
|   |       └── Component.scss
|   |       └── components
|   |           └── ...
|   ├── redux
|   |   ├── reducers
|   |   |   └── index.js
|   |   |   └── componentReducer
|   |   |       └── index.js
|   |   ├── actions
|   |   |   └── componentActions
|   |   |       └── index.js
|   |   ├── sagas
|   |   |   └── index.js
|   |   |   └── componentSagas
|   |   |       └── index.js
|   |   ├── selectors
|   |   |   └── componentSelectors
|   |   |       └── index.js
|   |   ├── store
|   |   |   └── index.js
|   |   ├── utils
|   ├── services
|   |   └── apiService.js
|   |   └── componentServices
|   |       └── index.js
|   ├── routes
|   |   └── route.js
|   |   
|   |    
|   ├── styles
|   |   └── App.scss
|   |   └── index.scss
|   |   └── variables.scss
|   |   └── ...
|   ├── types
|   |   └── index.js
|   |   └── component
|   |   └── index.js
|   |   └── ComponentProps.js
|   |   └── ...
|   └── utils
|       └── defaultStates
|       
|       
|       
└── index.jsx
```

### Components
&nbsp; &nbsp; Components folder contains all defined components separated into respective folders. Inside the root
`Components` folder is also another `components` folder, which contains all reusable components, such as buttons, inputs,
popups, notifications, etc. Each component should have the following files:
1. **View.jsx** - JS file containing JSX elements.
2. **Container.js** - JS file containing connection to redux store.
3. **index.js** - JS file exporting View and Container wrapped.
4. **styles.scss** - SCSS file containing styles for the component.
5. **components** - (optional) folder containing Component's components in the same structure as described above

**NOTE**: *if the component does not need data from redux store, such as state or actions, Container file does not need to be created.
Instead, index file exports only the View.*

### Redux
&nbsp; &nbsp; Redux folder contains everything required for redux store:
1. **store** *(index.js)* - redux configuration
2. **reducers** - folder containing index.js (entry point for all reducers) and respective folders with reducers for each component.
3. **sagas** - folder containing index.js (entry point for all sagas) and respective folders with sagas for each component.
4. **selectors** - folder containing respective folders with selectors for each component.
5. **actions** - folder containing respective folders with actions for each component.
6. **utils** - folder containing all helpers, constants etc. that are needed for redux.

### Routes
&nbsp; &nbsp; Routes folder contains the following: 
1. **route.js** - entry point for all route declarations

### Services
&nbsp; &nbsp; Services folder contains the following:
1. **apiService.js** - service provider
2. **`component`Service.js** - file containing api definitions for component. Each component (api route) should have separate service file.
3. **utils** - folder containing API routes definitions, and helper function for handling API calls

### Deployment 
For Temporay deployment getting help of netlify for that we can run following command to create a build:

```bash
$ npm run build
```
After that simply use the build folder to upload that on netlify after that you will get a hosted link
