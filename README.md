# Redux: Saga vs Thunk

<table>
  <colgroup>
    <col width="50%"/>
    <col width="50%"/>
  </colgroup>
  <tr>
    <th>Saga</th>
    <th>Thunk</th>
  </tr>
  <tr>
    <th colspan="2">Цели</th>
  </tr>
  <tr>
    <td colspan="2"  align="center">Интеграция оркестрирующего и асинхронного кода в <code>Redux.store</code></td>
  </tr>
  <tr>
    <th colspan="2">Под капотом</th>
  </tr>
  <tr>
    <td><code>Generators</code>, которые нравятся <b>Саше Нечаеву</b></td>
    <td><code>Plain Function</code></td>
  </tr>
  <tr>
    <th colspan="2">Совместимость со стандартами</th>
  </tr>
  <tr>
    <td>ES6, ES7</td>
    <td>ES5, ES6, ES7</td>
  </tr>
  <tr>
    <th colspan="2">Promises</th>
  </tr>
  <tr>
    <td>Требует использования</td>
    <td>Не требует использования, но поддерживает</td>
  </tr>
  <tr>
    <th colspan="2">Action creators</th>
  </tr>
  <tr>
    <td valign="top">
<pre lang="javascript">// Обычные
//
//
{
  someAction: someId => 
    ({ type: ACTION_TYPE, payload: someId }),
}
</pre>
</td>
    <td valign="top">
<pre lang="javascript">// несколько необычные, но понятные
import { actionHandler } from './action-handlers';
//
{
  someAction: someId => 
    actionHandler.bind(null, someId),
}
</pre>
    </td>
  </tr>
  <tr>
    <th colspan="2">Action Handlers</th>
  </tr>
  <tr>
    <td valign="top">
<pre lang="javascript">
import { put, call } from 'redux-saga/effects';
//
export function* actionHandler(someId) {
  yield put( actions.startLoading() );
  try {
    const stuff = yield call( 
      fetch(`/fetch-stuff/${someId}`)
    );
    yield put( actions.setStuff(stuff) );
  } catch (err) {
    yield put( actions.error(err) );
  }
  yield put(actions.endLoading())
}
</pre>
    </td>
    <td valign="top">
<pre lang="javascript">
// ES7 sample
//
export async function actionHandler(someId, dispatch) {
  dispatch( actions.startLoading() );
  try {
    const stuff = await (
      fetch(`/fetch-stuff/${someId}`)
    );
    dispatch( actions.setStuff(stuff) )
  } catch (err) {
    dispatch( actions.error(err) );
  }
  dispatch( actions.endLoading() );
}
</pre>
    </td>
  </tr>
  <tr>
    <td>&nbsp;</tr>
    <td>
<pre lang="javascript">
// ES5 sample
//
export function actionHandler(someId, dispatch) {
  dispatch( actions.startLoading() );
  fetch(`/fetch-stuff/${someId}`, function(err, stuff) {
    !err ? 
      dispatch( actions.setStuff(stuff) ) :
      dispatch( actions.error(err) )
    ;  
    dispatch( actions.endLoading() );
  });
}
</pre>
    </td>
  </tr>
  <tr>
    <th colspan="2">Подписка на действия</th>
  </tr>
  <tr>
    <td valign="top">
<pre lang="javascript">
import { takeLatest } from 'redux-saga';
export default function* saga() {
  yield* takeLatest(
    ACTION_TYPE, 
    actionHandler
  );
}
</pre>
    </td>
    <td valign="top">
<pre lang="javascript">
// не надо подписываться на действия
</pre>
    </td>
  </tr>
    <tr>
    <th colspan="2">Подключение к стору</th>
  </tr>
  <tr>
    <td valign="top">
<pre lang="javascript">
import createSagaMiddleware from 'redux-saga';
import saga from './sagas';
const sagaMiddleWare = createSagaMiddleware();
//
const store = createStore(
  combineReducers({ ...reducers }),
  applyMiddleware([ 
    sagaMiddleware, ...middlewares 
  ])
);
//
sagaMiddleware.run(saga);
</pre>
    </td>
    <td valign="top">
<pre lang="javascript">
import thunk from 'redux-thunk';
//
//
//
const store = createStore(
  combineReducers({ ...reducers }),
  applyMiddleware([ 
    thunk, ...middlewares 
  ])
);
</pre>
    </td>
  </tr>
  <tr>
    <th colspan="2">Генерация действий</th>
  </tr>
  <tr>
    <td colspan="2" align="center">Как обычных действий</td>
  </tr>
  <tr>
    <th colspan="2">Пошаговое тестирование обработчика действия</th>
  </tr>
  <tr>
    <td>Поддерживается, благодаря генераторам</td>
    <td>Не поддерживается. Требуется mock-нуть <code>dispatch</code> и <code>getState</code></td>
  </tr>
  <tr>
    <th colspan="2">Использование в PDFFiller</th>
  </tr>
  <tr>
    <td>jsfiller</td>
    <td>s2s-roles-app, filePicker</td>
  </tr>
  <tr>
    <th colspan="2">Миграция</th>
  </tr>
  <tr>
    <td>Миграция на <code>saga</code> потребует Обязательной промисификации API,
        которое использует <code>callback</code>-подход..</td>
    <td>Миграция на <code>thunk</code> позволит использовать существующее API,
        которое использует <code>callback</code>-подход, без изменений.
        Миграция с <code>saga</code> на <code>thunk</code> относительно несложная,
        т.к. изменения каснуться только saga's и объявления действий и не потребуется изменять 
        код, генерирующий действия.
        Дополнительно немного уменьшиться размер сборки -- <code>redux-saga</code>
        будет заменена на легковесную функцию из <code>redux-thunk</code>
        </td>
  </tr>
  <tr>
    <th colspan="2">Исходники</th>
  </tr>
  <tr>
    <td><a href="https://github.com/redux-saga/redux-saga">https://github.com/redux-saga/redux-saga</a></td>
    <td><a href="https://github.com/gaearon/redux-thunk">https://github.com/gaearon/redux-thunk</a></td>
  </tr>

</table>

