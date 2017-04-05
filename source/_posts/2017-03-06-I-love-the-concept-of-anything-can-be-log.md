---
title: I love the concept of 'anything can be log'
date: 2017-03-06 08:56:03
tags: [meta, js, redux, architecture]
---

A while ago I read [this](https://medium.com/poga/%E8%90%AC%E4%BA%8B%E8%90%AC%E7%89%A9%E7%9A%86%E6%98%AF-log-%E7%B3%BB%E7%B5%B1%E6%9E%B6%E6%A7%8B%E4%B9%9F%E4%BE%86%E9%BB%9E%E7%A7%91%E6%99%AE-caf3029359f8#.t37drl8hz) (chinese) article talking about how people build complicated architecture with log approach, back to then I didn't quite get it, but it did scratched something - something I am really longing with. A insight for better software architecture.

The article mentions that log is used in almost everywhere, because log has the following features:

**Timestamp** - a log has timestamp describing when happens what.

**Sequence** - since every log has a timestamp, we can line it up, make it an array. In that way you can see a timeline of events describing what happens in a system.

**Describing Actions** - instead of really executing something, log just describes what is going to do.

Log has been used in many domains like version control, database, replication, event stream and many other aspects for solving complicated problems.

Imagine we have a database, and it is writing something into disk, and suddenly computer crashed. Here comes the problem - how do we restore database to the point before crash and let it continue its job?

The answer is log, it writes log first, and then, really executing the action. Since we have a **log that describing the system state**, we can resume the state and make it continue its job.

Log can even be used in replication as well. Syncing data among all machines/process is not an easy thing. How do you keep data on all machines identical while sharing? Move physical data? That would be inefficient because physical data is large and plus network bandwidth issue. How about sending log instead? Log is relatively small and actually its just an **history of state**, instead of sending physical data, we send a series actions that should be carried out, and by following the same trial, state should be the same in the end.

And what's the point when it comes to front-end? Today's front-end is way more interactive than 15 years ago. Lots of actions and events come and go in our application. Trendy developers probably have heard `Redux` - a central event management implementation for resolving complicated events.

One of a reason that really makes `Redux` great is, yes, log. In `Redux` we deliver log first, and delay the **action (side effect / mutation)** afterward.

```js
const fetchDataLoadingAction = () => {
  type: "FETCH_DATA_LOADING"
};

const fetchDataDonesAction = (data) => {
  type: "FETCH_DATA_DONE",
  payload: data
};

const fetchDataFailAction = (err) => {
  type: "FETCH_DATA_FAIL",
  payload: err
};
```

Despite there are **Action** in those names (a common naming convention for redux), those are actually logs describing an action, and if we want to execute an action, we can wrap it in a function

```js

// here we use redux-thunk for handling simple async or combining serious of actions together.
const fetchData = () => (dispatch) => {
  dispatch(fetchDataLoadingAction());
  fetchDataAjax()
    .then(data => dispatch(fetchDataDoneAction(data)))
    .catch(err => dispatch(fetchDataFailAction(err)));
};

// keeping new state in our reducer
const INIT_STATE = {
  fetchState: "",
  data:       {}
};
const reducer = (state = INIT_STATE, action) => {
  switch(action.type){
    case "FETCH_DATA_LOADING": {
      return {...state, fetchState: "loading"};
    }
    case "FETCH_DATA_DONE": {
      return {fetchState: "success", data: action.payload};
    }
    case "FETCH_DATA_FAIL": {
      return {fetchState: "fail", data: action.payload};
    }
    default: return state;
  }
};

// later...
store.dispatch(fetchData());

// if you have installed redux-logger you will see something similar to this
// action: { type: "FETCH_DATA_LOADING" }
// state: { fetchState: "loading", data: {} }
// ...
// action: { type: "FETCH_DATA_DONE", payload: "foobar" }
// state: { fetchState: "success", data: "foobar" }
```

By using log, developers can manipulate the timeline of an application, performing actions like undo and redo or jump to a specific time of state for debugging. This is the beauty of log.
