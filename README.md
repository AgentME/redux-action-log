# redux-action-log

[![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/Macil/redux-action-log/blob/master/LICENSE.txt) [![npm version](https://img.shields.io/npm/v/redux-action-log.svg?style=flat)](https://www.npmjs.com/package/redux-action-log) [![CircleCI Status](https://circleci.com/gh/Macil/redux-action-log.svg?style=shield)](https://circleci.com/gh/Macil/redux-action-log) [![Greenkeeper badge](https://badges.greenkeeper.io/Macil/redux-action-log.svg)](https://greenkeeper.io/)

This project is a redux store enhancer which allows you to record the redux
action history and access it. It can be configured to have a maximum number of
actions to keep in the history. Early actions will be removed from the history,
and the redux state of the beginning of the history will be recorded.

## Usage

```js
import {createStore} from 'redux';
import {createActionLog} from 'redux-action-log';

import myReducer from './reducer';

const actionLog = createActionLog({limit: 100});
const store = createStore(myReducer, undefined, actionLog.enhancer);

// ...

const log = actionLog.getLog();
// {
//   initialState: undefined,
//   skipped: 0,
//   actions: [
//     {action: {type: 'ADD', payload: 3}, timestamp: 1469604366218},
//     {action: {type: 'ADD', payload: 5}, timestamp: 1469604424293}
//   ]
// }
```

If you are using other store enhancers such as applyMiddleware, then you'll
need to compose actionLog's enhancer. Make sure to place it last to let it see
actions generated by previous enhancers:

```js
import {compose, createStore, applyMiddleware} from 'redux';
import {createActionLog} from 'redux-action-log';

import myReducer from './reducer';
import someMiddleware from './someMiddleware';

const actionLog = createActionLog({limit: 100});

let enhancer = applyMiddleware(someMiddleware);
enhancer = compose(enhancer, actionLog.enhancer);

const store = createStore(myReducer, undefined, enhancer);
```

## API

This module exports the `createActionLog(options)` function. The `options`
object may have the following properties:
* `limit`: Soft limit to the number of actions recorded. May be set to null to
 mean no limit. Defaults to 200.
* `snapshotInterval`: When this many actions pass, a checkpoint containing a
 reference to the current state is made. A higher number means fewer
 checkpoints will be kept in memory, but it means that the log must go on
 longer before being culled. May be set to null to mean no checkpoints should
 be made (this is only sensible when no limit is used too). Defaults to 20.

The function returns an ActionLog object with the following properties:
* `enhancer`: Pass this to createStore once.
* `getLog()`: Returns the object `{initialState, skipped, actions}`.
* `setLimit(n)`: Change the limit option. This will cull the action log if
 necessary.
* `clear`: Completely cull the current log. It's similar to setting limit to 0
 and then back to its previous value.

## Types

[Flow](https://flowtype.org/) type declarations for this module are included!
If you are using Flow, they won't require any configuration to use.
