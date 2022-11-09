### Pubsub.js

``` js
//pub发布事件,sub订阅事件
const listeners = {};
window.listeners = listeners;
const pubSub = {
  pub: (name, opts = {}) => {
    !listeners[name] &&
      console.log(
        `%cnamepubsub ${name}`,
        "background: #ffe58f; color: #000; padding: 3px; border-radius: 2px;",
        opts
      );
    if (!listeners[name]) return;
    console.log(
      `%cnamepubsub ${name}`,
      "background: green; color: white; padding: 3px; border-radius: 2px",
      opts
    );
    listeners[name].forEach((cb) => cb(opts, name));
  },
  sub: (name, cb) => {
    if (listeners[name]) {
      listeners[name].push(cb);
    } else {
      listeners[name] = [cb];
    }
    return () => {
      const idx = listeners[name].indexOf(cb);
      if (idx > -1) listeners[name].splice(idx, 1);
    };
  },
  unSub(name, cb) {
    let cbArr = listeners[name];
    if (!cbArr || !cbArr.length) return;
    listeners[name] = cbArr.filter((v) => v !== cb);
  },
};
window.reactPubSub = pubSub;
export default pubSub;

```

使用方式

```js
// 监听页面  function为要触发的操作 
useEffect(() => {
   pubSub.sub("函数名",function());
   // 离开页面卸载监听
   return () => {
       pubSub.unSub("函数名",function())
   }
},[])

// 触发页面  type为要传递的参数，没有可不传
pubSub.pub("函数名",type: {})
```

