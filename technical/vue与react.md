---
layout: page
title: vue与react生态对比
parent: 技术文档
---

### 生态对比

以下整理 vue 中的功能，在 react 中做到一致的实现，帮助梳理

|     vue 概念      |               react 概念               |                                                                     不同                                                                      |
| :---------------: | :------------------------------------: | :-------------------------------------------------------------------------------------------------------------------------------------------: |
|     templete      |               react 组件               |                                                   实际渲染原理不同，初始使用时一般无需考虑                                                    |
|       data        | 类组件的 state 和函数式组件的 useState |                                                                 更新方式不同                                                                  |
|  computed，watch  |                useMemo                 |                                                                                                                                               |
|      method       |              useCallback               |                                                                                                                                               |
|       props       |                 props                  |                                                                                                                                               |
|       slot        |                 props                  |                                                                                                                                               |
|   vuex 的 state   |            redux 中的 state            |                                                                                                                                               |
|  vuex 的 getter   |          redux 中的 selector           |                                                                                                                                               |
| vuex 的 mutations |      redux 中的 action 和 reducer      |                 mutations 包含了 mutation 自身和 dispatch 后对 store 的变化，对应 action 自身和 reducer 中 action 的回调函数                  |
|  vuex 的 actions  |  redux 中的 action 和 saga 或者 epic   | vuex 中的 action 不触发 state 改变，但是执行副作用后触发 mutation，对应 redux-observable 或者 redux-saga 中的 action 和 epic 或者 saga action |
|  vuex 的 module   |                   无                   |                                                   需要通过 combineReducer 自行组合 reducer                                                    |
