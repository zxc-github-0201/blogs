### qiankun父子应用通信

##### 父应用

在src目录下创建一个actions文件；

```
import { initGlobalState } from "qiankun";

const initialState = {
  //这里写初始化数据
  userName: "testInfo",
  userId: "1232",
  token: "",
};

// 初始化 state
const actions = initGlobalState(initialState);
actions.onGlobalStateChange((state, prev) => {
  //监听公共状态的变化
  console.log("主应用: 变更前");
  console.log(prev);
  console.log("主应用: 变更后");
  console.log(state);
});
export default actions;
```

然后在需要的地方修改：

```
actions.setGlobalState({ token: token }))
```

##### 子应用

同样在src目录下新建一个action.js文件

```
function emptyAction() {
  console.warn("Current execute action is empty");
}

class Actions {
  actions = {
    onGlobalStateChange: emptyAction,
    setGlobalState: emptyAction,
  };

  setActions(actions) {
    this.actions = actions;
  }

  onGlobalStateChange(...args) {
    return this.actions.onGlobalStateChange(...args);
  }

  setGlobalState(...args) {
    return this.actions.setGlobalState(...args);
  }
}

const actions = new Actions();

export default actions;

```

在index文件：

```
const win: any = window;

function render(props: any) {
  const { container } = props;
  console.log("render", props);
  actions.onGlobalStateChange((state: any) => {
    console.log("全局状态修改", state);
  }, true);

  ReactDOM.render(
    <App />,
    container
      ? container.querySelector("#root")
      : document.querySelector("#root")
  );
}

if (!win.__POWERED_BY_QIANKUN__) {
  render({});
}

export async function bootstrap() {
  console.log("[react16] react app bootstraped");
}

export async function mount(props: any) {
  console.log("[react16] props from main framework", props);
  actions.setActions(props);
  render(props);
}

export async function unmount(props: any) {
  const { container } = props;
  ReactDOM.unmountComponentAtNode(
    container
      ? container.querySelector("#root")
      : document.querySelector("#root")
  );
}
```

在需要使用的地方

```
useEffect(() => {
    actions.onGlobalStateChange((state: any) => {
      console.log("state", state);
    });
  }, []);
```

