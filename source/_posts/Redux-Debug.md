---
title: Redux-Debug
date: 2018-07-24 19:13:25
tags:
---

### 1. dispatch 同一个 action 在极短的时间内 2 次

如果 reducer 里面是用的`return fromJS(action.payload)`,然后 dispatch 同一个 action 在极短的时间内 2 次，导致的结果就是第二次使用的 immutable state 是旧的，它没有被第一次的 action 所更改，这样的后果就是状态丢失了。JS 执行时单线程，所以 reudx 这里并没有 race condition,真正的原因是这两次 dispatch 是在同一 render 桢中运行的，所以它们 mapStateToProps 中原始 state 是相同的，事实上就是第二次修改覆盖了第一次修改（reducer 中使用了 fromJS,而非 mergeDeep)。

解决方案：

1. 多个 action 修改不同部分
2. 多个修改合并到一个 action
   我先选择#2

### 2. yield select 连续多个

好像如果有一个循环，大约 100 多次，在循环体里面 yield select(selctor), 那么可能导致 saga 不工作，但是 redux 还是工作的。

<!-- more -->

### 3. redux-persist-immutable

这个库会把 Immutable.List 成员中的 undefined 变为 null，但是 Immutable.Map 中我不清楚。
因为我们遇到一个 undefined 被 redux-persist-immutable 转为 null 存储了，这就是这个 bug 难以推断的原因。

- redux-immutable 可以设置 redux 的根节点是 Immutable.Map，但是这个必要性我还不清楚，好像有性能优势。
- redux-persist 可以持久化 redux 的数据，可以设置白名单, 默认序列化是用 JOSN.stringify, 反序列化是 JSON.parse。
- redux-persist-immutable 可以持久化 redux 中 Immutable 类型的数据，如 Map,List,transform 用的是 redux-persist-transform-immutable。
- redux-persist-transform-immutable，这个库很简单，就是做 Immutable 的序列化，反序列化,它包装成一个 redux-persist 的 transform。

```js
var transit = require("transit-immutable-js");
var reduxPersist = require("redux-persist");

module.exports = function(config) {
  config = config || {};

  var transitInstance = transit;
  if (config.records) {
    transitInstance = transit.withRecords(config.records);
  }

  return reduxPersist.createTransform(
    function(state) {
      return transitInstance.toJSON(state);
    },
    function(raw) {
      return transitInstance.fromJSON(raw);
    },
    config
  );
};
```

- transit-immutable-js 这个是序列化，反序列化 Immutable 类型的工具库，依赖 transit-js
- transit-js

```js
writer.marshal = function(em, obj, asMapKey, cache) {
  if (em.transform !== null) {
    obj = em.transform(obj);
  }

  var h =
      em.handler(obj) || //obj 是undefiend, 但是在handler逻辑中，handlers.constructor用的是 x == null，所以undefined通过了条件返回了null。
      (em.handlerForForeign ? em.handlerForForeign(obj, em.handlers) : null),
    tag = h ? h.tag(obj) : null, // h 是NilHandler， 它的tag是'_'
    rep = h ? h.rep(obj) : null;

  if (h != null && tag != null) {
    switch (tag) {
      case "_": // 因为tag == '_', 所以进来这个case分支
        return em.emitNil(asMapKey, cache); // 这里就是NilHandler
        break;
      // more...
      default:
        return writer.emitEncoded(em, h, tag, rep, obj, asMapKey, cache);
        break;
    }
  }
};

writer.JSONMarshaller.prototype.handler = function(obj) {
  var h = this.handlers.get(handlers.constructor(obj)); // handlers.constructor 传undefined返回一个null, 然后以null为key，this.handlers.get(null)获取到NilHandler
  // more...
};

handlers.constructor = function(x) {
  if (x == null) {
    // 这里x是undefined， 通过条件后返回了null
    return null;
  } 
  // more...
};

handlers.defaultHandlers = function(hs) {
  hs.set(null, new handlers.NilHandler()); // null 为key, NilHandler 是value
  hs.set(String, new handlers.StringHandler());
  // more...
  return hs;
};

writer.JSONMarshaller.prototype.emitNil = function(asMapKey, cache) {
  if (asMapKey) {
    return this.emitString(d.ESC, "_", "", asMapKey, cache);
  } else {
    return null; // 这就是最终返回了null
  }
};

/**
 * @constructor
 */
handlers.NilHandler = function Transit$NilHandler() {};
handlers.NilHandler.prototype.tag = function(v) {
  return "_";
};
handlers.NilHandler.prototype.rep = function(v) {
  return null;
};
handlers.NilHandler.prototype.stringRep = function(v) {
  return "null";
};
```
