# Setup store

## Install

- redux
- react-redux
- redux-thunk

## Code

- app root component: wrap in `<Provider store={myStore}>` from react-redux

  ```js
  <Provider store={myStore}>
    <GridContainer>
      <AppRouter />
    </GridContainer>
  </Provider>
  ```

- Create myStore: use the createStore function from redux

  - To use the chrome extension: `window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__`
  - To use async actions: `thunk`

  ```js
  const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose;

  const myStore = createStore(rootReducer, composeEnhancers(applyMiddleware(thunk)));
  ```

- Make a root reducer which combines all needed reducers

  ```js
  const rootReducer = combineReducers({
    noteReducer,
    weatherReducer,
  });
  ```

- Make constants to name the actions

  ```js
  const prefix = (type) => `WEATHER__${type}`;

  export const WEATHER__LOAD = prefix('LOAD');
  export const WEATHER__LOADED = prefix('LOADED');
  export const WEATHER__LOAD__FAILED = prefix('LOAD__FAILED');
  ```

- Make reducer which is a switch case of action type names and returns a state

  ```js
  const weatherReducer = (state, action) => {
    switch (action.type) {
      case WEATHER__LOAD:
        return { ...initialState, loading: true };
      case WEATHER__LOADED:
        return { ...initialState, loaded: true, weather: action.payload };
      case WEATHER__LOAD__FAILED:
        return { ...initialState, loadfailed: true };
      default:
        return initialState;
    }
  };
  ```

- Make action functions: dispath a type and extra info needed in the components

  ```js
  export const getWeather = () => async (dispatch, getState) => {
    dispatch({
      type: WEATHER__LOAD,
    });

    const weather = await axios.get(
      'http://api.weatherstack.com/current?access_key=3c62ae86f5bddca030bf10fc24fddca4&query=Genk',
    );

    if (weather) {
      dispatch({
        type: WEATHER__LOADED,
        payload: weather.data,
      });
    } else {
      dispatch({
        type: WEATHER__LOAD__FAILED,
      });
    }
  };
  ```

- Make class component (rcredux)
  - mapStateToProps: arrow function which returns object with needed reducers
  - mapDispatchToProps: object with needed actions
  - export connect: combine the state and dispatch with the component
