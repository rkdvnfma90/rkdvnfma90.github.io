---
title: RTK Cheat Sheet
date: 2022-03-19 11:56:00 +0900
categories: [RTK]
tags: [RTK]
image:
  src: https://noticon-static.tammolo.com/dgggcrkxq/image/upload/v1567749614/noticon/zgdaxpaif5ojeduonygb.png
  width: 100
  height: 100
  alt: RTK
---


## RTK (Redux Toolkit)

Redux Toolkit (이하 RTK)은 Redux 개발을 효율적으로 할수있게 하는 공식 개발도구이다. 

기존 Redux는 많은 보일러 플레이트 코드가 필요했고, 조금 더 유용하게 사용하기 위해 더 많은 패키지들을 추가로 설치해야 했다. 이런 불편한 점을 해소하기 위해 RTK가 나왔다고 할 수 있겠다!


<br/>
<br/>

## 설치

RTK는 빌트인 타입스크립트이기 때문에 별도의 타입설치가 필요하지 않다.

```terminal
yarn add @reduxjs/toolkit react-redux
```
혹은
```terminal
npx create-react-app my-app --template redux-typescript
```

<br/>
<br/>

## RTK가 포함하고 있는 API

- configureStore(): 더 간단한 설정 옵션과 기본값들을 제공하기 위해 기존 Redux의 `createStore`를 래핑한 API 이다. 자동으로 슬라이스와 리듀서들을 결합하고 미들웨어를 추가할 수 있다. `redux-thunk`를 포함하고 있다. 기본적으로 `Redux DevTools Extension`을 사용할 수 있다.
  ```jsx
  import { configureStore } from '@reduxjs/toolkit'

  import rootReducer from './reducers'

  const store = configureStore({ reducer: rootReducer })
  // The store now has redux-thunk added and the Redux DevTools Extension is turned on
  ```
- createReducer(): 리듀서 함수를 switch 구문을 사용하지 않고 작성할 수 있다. 또한 자동으로 `immer` 라이브러리를 사용하여 `state.todos[3].completed = true`와 같이 직접 상태를 변경하는 것처럼 사용할 수 있다.
  ```jsx
  import { createAction, createReducer } from '@reduxjs/toolkit'

  interface CounterState {
    value: number
  }

  const increment = createAction('counter/increment')
  const decrement = createAction('counter/decrement')
  const incrementByAmount = createAction<number>('counter/incrementByAmount')

  const initialState = { value: 0 } as CounterState

  const counterReducer = createReducer(initialState, (builder) => {
    builder
      .addCase(increment, (state, action) => {
        state.value++
      })
      .addCase(decrement, (state, action) => {
        state.value--
      })
      .addCase(incrementByAmount, (state, action) => {
        state.value += action.payload
      })
  })

  // 이렇게도 사용할 수 있다.
  const increment = createAction('increment')
  const decrement = createAction('decrement')

  const counterReducer = createReducer(0, {
    [increment]: (state, action) => state + action.payload,
    [decrement.type]: (state, action) => state - action.payload
  })
  ```
- createAction(): 주어진 문자열에 대한 액션 생성자 함수를 생성한다. 이 함수 자체에는 `toString()`이 정의되어 있기 때문에 constant 타입 대신에 사용이 가능하다.
  ```jsx
  const INCREMENT = 'counter/increment'

  function increment(amount: number) {
    return {
      type: INCREMENT,
      payload: amount,
    }
  }
  let action = increment()
  // { type: 'counter/increment' }

  action = increment(3)
  // { type: 'counter/increment', payload: 3 }

  console.log(increment.toString())
  // 'counter/increment'

  console.log(`The action type is: ${increment}`)
  // 'The action type is: counter/increment'
  ```
- createSlice(): 리듀서 함수, 슬라이스 이름, 기본값을 넣을 수 있고 `액션 생성자`와 `액션 타입`을 가진 슬라이스 리듀서를 생성한다.
  ```jsx
  import { createSlice, PayloadAction } from '@reduxjs/toolkit'

  interface CounterState {
    value: number
  }

  const initialState = { value: 0 } as CounterState

  const counterSlice = createSlice({
    name: 'counter',
    initialState,
    reducers: {
      increment(state) {
        state.value++
      },
      decrement(state) {
        state.value--
      },
      incrementByAmount(state, action: PayloadAction<number>) {
        state.value += action.payload
      },
    },
  })

  export const { increment, decrement, incrementByAmount } = counterSlice.actions
  export default counterSlice.reducer
  ```

- createAsyncThunk: 프로미스를 리턴하는 액션 타입 문자열과 함수를 허용하고, 이 프로미스를 기반으로 하는 `pending/fulfilled/rejected` thunk를 생성한다.
  ```jsx
  import { createAsyncThunk, createSlice } from '@reduxjs/toolkit'
  import { userAPI } from './userAPI'

  // First, create the thunk
  const fetchUserById = createAsyncThunk(
    'users/fetchByIdStatus',
    async (userId, thunkAPI) => {
      const response = await userAPI.fetchById(userId)
      return response.data
    }
  )

  // Then, handle actions in your reducers:
  const usersSlice = createSlice({
    name: 'users',
    initialState: { entities: [], loading: 'idle' },
    reducers: {
      // standard reducer logic, with auto-generated action types per reducer
    },
    extraReducers: (builder) => {
      // Add reducers for additional action types here, and handle loading state as needed
      builder.addCase(fetchUserById.fulfilled, (state, action) => {
        // Add user to the state array
        state.entities.push(action.payload)
      })
    },
  })

  // Later, dispatch the thunk as needed in the app
  dispatch(fetchUserById(123))
  ```

- createEntityAdapter: 스토어에서 정규화된 데이터를 관리하기 위해 재사용 가능한 리듀서, selector의 집합을 생성한다.
- createSelector: `reselect` 라이브러리의 createSelector.

<br/>
<br/>

## createAction 
액션 생성자 함수 생성

```ts
const increment = createAction("counter/increment");
const action = increment(10); // { type: "counter/increment", payload: 10 }
```

createAction은 `타입 문자열`만 제공하면 바로사용할 수 있고, 생성된 액션 생성자의 파라미터로 값을 넘기면 그 값은 `payload` 필드에 들어간다.

리턴될 payload 객체를 커스터마이징 하고 싶다면 createAction 함수의 두 번째 인자로 콜백 함수를 넘기면 된다.

```ts
const addTodo = createAction("todo/add", (text) => {
  return {
    payload: {
      text,
      uuid: generateUUID();
    }
  }
});

addTodo("제주도 여행");
/*
해당 객체 리턴
{
  type: "todo/add",
  payload: {
    text: "제주도 여행",
    uuid: "AOF438ASDFXVBC32",
  }
}
*/
```

콜백함수 안에서는 액션 생성자 함수의 파라미터로 전달받지 않은 데이터들을 추가할 수 있고 이 때 객체의 형태는 반드시 `FSA(Flux Standard Action)` 형태를 따라야 한다.

<br/>
<br/>

## createReducer

리듀서 생성

```ts
const increment = createAction("increment");
const decrement = createAction("decrement");

const counterReducer = createReducer(0, {
  [increment]: (state, action) => state + action.payload,
  [decrement.type]: (state, action) => state - action.payload
});
```

createReducer의 두 번째 파라미터인 리듀서 맵에는 액션 생성자에 전달한 문자열을 그대로 넣어도 되지만 ("increment") 액션 생성자 함수 그 자체를 넣어도 된다. 그 이유는 createAction이 리턴하는 액션 생성자 함수의 `toString`을 오버라이딩 했기 때문

<br/>
<br/>

## createSlice

액션과 리듀서를 한번에 생성

```ts
const todoSlice = createSlice({
  name: "todo", // 액션 타입 문자열의 prefix
  initialState: [], // 초기값
  reducers: { // 리듀서 맵
    addTodo: {
      // 리듀서 함수
      reducer: (state, action) => {
        state.push(action.payload);
      },
      // createAction 함수의 두 번째 파라미터의 콜백 함수라고 보면 된다.
      prepare: (text: string) => ({
        payload: {
          id: generateUUID(),
          text
        }
      })
    },

    // 리듀서와 액션 생성 함수를 분리하지 않고 사용할 수 있다.
    removeTodo: (state, action) => {
      state.splice(
        state.findIndex(item => item.id === action.payload.id),
        1
      );
    }
  }

});

// 액션과 리듀서를 아래와 같이 가져올 수 있다.
const { addTodo, removeTodo } = todoSlice.actions;
const { reducer } = todoSlice;
```

<br/>
<br/>

## createSelector

리덕스 스토어에서 state 값을 조회하기 위해 사용한다. 

여기서 selector란 의미는 Redux에서 어떤 state를 기반으로 새로운 state를 리턴하는 함수를 말한다. 예를 들어 `Todo 리스트에서 완료된 것 만 필터링`할 때 보통 사용하곤 한다.

RTK의 createSelector는 reselect 라이브러리의 createSelector와 같다. (메모이제이션 지원)

[화해 블로그 참조](http://blog.hwahae.co.kr/all/tech/tech-tech/6946/)
```ts
const shopItemsSelector = state => state.shop.items;
const taxPercentSelector = state => state.shop.taxPercent;

// subtotal 값을 메모이제이션
const subtotalSelector = createSelector(
  shopItemsSelector,
  items => items.reduce((subtotal, item) => subtotal + item.value, 0)
);

const totalSelector = createSelector(
  subtotalSelector,
  taxSelector,
  (subtotal, tax) => ({ total: subtotal + tax })
);
```

createSelector를 호출할 때 파라미터의 개수제한은 없지만 가장 마지막 파라미터는 상태 객체를 리턴할 콜백 함수가 들어가야 한다. 이 콜백 함수의 파라미터는 앞에 있는 파라미터에서 리턴한 객체가 차례대로 위치한다.