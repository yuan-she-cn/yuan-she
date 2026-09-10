# Cocos

以下示例均在 Cocos Creator 3.8.8 测试通过。

## 场景

- 创建场景：Assets -> 右键 -> Create -> Scene
- 编辑场景：Assets -> 双击

### 场景属性

- Auto Release Assets 是否自动释放
- Ambient
  - Sky Lighting Color
  - Sky Illum
  - Ground Lighting Color
- Skybox
- Fog
- Shadows
- Octree Scene Culling
- Skin
- PostSettings

## 节点

- 创建节点：Hierarchy -> 右键 -> Create -> Empty Node

### 节点属性

- Node.Position 位置
- Node.Rotation 旋转
- Node.Scale 缩放
- Node.Mobility 可移动性（光照相关）
- Node.Layer 可见性

## 组件

- 添加组件：Inspector -> Add Component

## 预制件

- 创建预制件：Assets -> 右键 -> Create -> Node Prefab
- 编辑预制件：Assets -> 双击
- 使用预制件：Assets 拖拽到 Hierarchy
- 编辑预制件节点：Hierarchy -> 点击

## 脚本

- 创建脚本：Assets -> 右键 -> Create -> TypeScript
- 挂载脚本：Inspector -> Add Component -> Custom Script -> 选择脚本
- 配置脚本编辑器：File -> Preferences -> Program Manager -> Default Script Editor

### 生命周期函数

- onLoad 节点首次激活时触发
- onEnable 节点 active 属性或组件 enabled 属性变为 true 时触发
- start 组件首次激活前触发
- update(deltaTime) 每一帧渲染前触发
- lateUpdate(deltaTime) 每一帧渲染后触发
- onDisable 节点 active 属性或组件 enabled 属性变为 false 时触发
- onDestroy 节点或组件消毁前触发

### 装饰器

**组件装饰器**

- @ccclass(String) 注释为 Cocos Creator 类，控制序列化、编辑器展示
- @executeInEditMode(Boolean) 允许在编辑器模式运行
- @requireComponent(Component) 依赖组件
- @executionOrder(Number) 生命周期函数的执行优先级
- @disallowMultiple(Boolean) 禁止多个同类组件
- @menu(Path) 设置组件菜单路径，在编辑器 Add Component 时方便查找
- @help(Url) 设置帮助文档地址，在编辑器 节点属性-组件标题(名称后面) 可点击查看

**属性装饰器**

- @property(Object) 注释为 Cocos Creator 属性，控制序列化、编辑器展示

**属性装饰器参数**

- type 指定 Cocos Creator 类型，内置界面的类型：Color、RealCurve、CurveRange、Gradient、GradientRange
- visible 是否在编辑器中显示，默认 true，private 修饰或 \_ 开头的属性默认不显示
- displayName 显示名称，类型 String
- tooltip 设置提示，类型 String
- multiline 设置为多行文本框，默认 false
- readonly 设置为只读，默认 false
- min 设置最小值，类型 Number
- max 设置最大值，类型 Number
- step 设置步长，类型 Number
- range min & max & step 的组合，类型 Array
- slide 设置为滑动条，默认 false
- group 设置分组，类型 {id: String, name: String, displayOrder: Number, style: 'tab' | 'section'}
- serializable 是否序列化，默认 true
- formerlySerializedAs 序列化名称，类型 String
- editorOnly 导出项目前剔除，默认 false
- override 是否覆盖，默认 false
- displayOrder 设置排序，类型 Number

### 组件创建

```TypeScript
import { _decorator, Component } from "cc";
const { ccclass } = _decorator;

@ccclass("component")
export class component extends Component {
}
```

### 组件和节点操作

```TypeScript
import { _decorator, Node, Component, Label, Button, find, Vec3, director, instantiate, Prefab } from "cc";
const { ccclass, property } = _decorator;

@ccclass("component")
export class component extends Component {
  // 获取其他节点，需要在编辑器中拖拽赋值，取值为赋值节点
  @property(Node)
  private otherNode: Node = null;
  // 获取其他组件，需要在编辑器中拖拽赋值，取值为赋值节点的组件
  @property(Label)
  private otherLabel: Label = null;
  // 获取预制节点，需要在编辑器中拖拽赋值，取值为赋值预制件
  @property(Prefab)
  private prefab: Prefab = null;

  start() {
    // 获取当前场景
    const scene = director.getScene();
    // 获取所在节点
    const node = this.node;
    // 获取所在节点其他组件
    const label = this.getComponent(Label);
    const button = node.getComponent(Button);
    // 创建组件
    // const label = this.node.addComponent(Label);
    // 销毁组件
    label.destroy();
    // 获取子节点
    const children = node.children;
    const camera = node.getChildByName("Camera");
    // 通过路径获取节点
    const sprite = find("Camera/Sprite", node);
    const editBox = find("Canvas/Camera/EditBox");

    // 节点是否激活
    // const active = node.activeInHierarchy;
    // 关闭节点
    // node.active = false;
    // 激活节点
    // node.active = true;
    // 更改父节点
    // sprite.parent = node;
    // sprite.removeFromParent();
    // node.addChild(sprite);
    // 更改节点位置
    // sprite.setPosition(100, 100, 0);
    // sprite.setPosition(new Vec3(100, 100, 0));
    // sprite.position = new Vec3(100, 100, 0);
    // 更改节点旋转
    // sprite.setRotationFromEuler(0, 0, 90);
    // 更改节点缩放
    // sprite.setScale(2, 2, 2);
    // 组件是否激活
    // const enabled = this.otherLabel.enabled;
    // 关闭组件
    // this.otherLabel.enabled = false;
    // 激活组件
    // this.otherLabel.enabled = true;

    // 创建节点
    const newNode = new Node("newNode");
    const newSprite = instantiate(sprite);
    // 添加节点
    scene.addChild(newNode);
    // 销毁节点
    newNode.destroy();
    // 节点是否有效（未销毁）
    const isValid = newNode.isValid;
    // 创建预制节点
    const newPrefab = instantiate(this.prefab);
  }
}
```

### 场景操作

```TypeScript
import { _decorator, Component, director } from "cc";
const { ccclass } = _decorator;

@ccclass("component")
export class component extends Component {
  start() {
    // 设置常驻节点，节点必须为根节点
    director.addPersistRootNode(this.node);
    // 取消常驻节点
    director.removePersistRootNode(this.node);
    // 加载并切换场景
    director.loadScene("main", function () {
      console.log("加载场景后回调");
    });
    // 预加载场景后切换场景
    director.preloadScene("main", function () {
      // 预加载场景后回调
      director.loadScene("main");
    });
  }
}
```

### 计时器

```TypeScript
import { _decorator, Component } from "cc";
const { ccclass } = _decorator;

@ccclass("component")
export class component extends Component {
  start() {
    this.scheduleOnce(function () {
      console.log("1s 后执行 1 次");
    }, 1);
    this.schedule(function () {
      console.log("间隔 1s 执行 1 次");
    }, 1);
    this.schedule(function () {
      console.log("3s 后开始执行，重复 2+1 次，每次间隔 1s");
    }, 1, 2, 3);
    let callback;
    let count = 0;
    this.schedule(callback = function () {
      count++;
      console.log("间隔 1s 执行 1 次");
      if (count == 5) {
        this.unschedule(callback);
        console.log("取消计时器");
      }
    }, 1);
    // 取消所有计时器
    this.unscheduleAllCallbacks();
  }
}
```

## 事件

### 自定义事件

```TypeScript
import { _decorator, Component, EventTarget } from "cc";
const { ccclass } = _decorator;

@ccclass("component")
export class component extends Component {
  start() {
    const eventTarget = new EventTarget();
    // 监听事件，需要手动关闭
    eventTarget.on("first", this.firstEvent, this);
    // 监听事件，响应后自动关闭
    eventTarget.once("second", this.secondEvent);
    // 取消事件，相同类型、相同回调、相同目标
    eventTarget.off("first", this.firstEvent, this);
    // 取消事件，相同类型
    eventTarget.off("second");
    // 发射事件
    eventTarget.emit("first", "param");
    eventTarget.emit("second");
  }
  // 最多支持 5 个参数
  firstEvent(event) {
    console.log("firstEvent", this, event);
  }
  secondEvent() {
    console.log("secondEvent");
  }
}
```

### 全局事件

| 输入事件 | 事件类型                     | 回调参数          | 说明         |
| -------- | ---------------------------- | ----------------- | ------------ |
| 鼠标事件 | Input.EventType.MOUSE_DOWN   | EventMouse        | 鼠标按下     |
| 鼠标事件 | Input.EventType.MOUSE_UP     | EventMouse        | 鼠标释放     |
| 鼠标事件 | Input.EventType.MOUSE_MOVE   | EventMouse        | 鼠标移动     |
| 鼠标事件 | Input.EventType.MOUSE_WHEEL  | EventMouse        | 鼠标滚轮滚动 |
| 触摸事件 | Input.EventType.TOUCH_START  | EventTouch        | 触摸开始     |
| 触摸事件 | Input.EventType.TOUCH_MOVE   | EventTouch        | 触摸移动     |
| 触摸事件 | Input.EventType.TOUCH_END    | EventTouch        | 触摸结束     |
| 触摸事件 | Input.EventType.TOUCH_CANCEL | EventTouch        | 触摸中断     |
| 键盘事件 | Input.EventType.KEY_DOWN     | EventKeyboard     | 键盘按下     |
| 键盘事件 | Input.EventType.KEY_UP       | EventKeyboard     | 键盘释放     |
| 键盘事件 | Input.EventType.KEY_PRESSING | EventKeyboard     | 键盘持续按下 |
| 设备事件 | Input.EventType.DEVICEMOTION | EventAcceleration | 设备传感     |

```TypeScript
import { _decorator, Component, input, Input, EventMouse, EventKeyboard, KeyCode, EventAcceleration } from "cc";
const { ccclass } = _decorator;

@ccclass("component")
export class component extends Component {
  start() {
    // 监听事件
    input.on(Input.EventType.MOUSE_DOWN, this.mouseDown, this);
    // 取消事件
    input.off(Input.EventType.MOUSE_DOWN, this.mouseDown, this);
    // 键盘事件
    input.on(Input.EventType.KEY_DOWN, this.keyDown, this);
    // 设备事件，需要启用设备传感
    input.setAccelerometerEnabled(true);
    input.on(Input.EventType.DEVICEMOTION, this.devicemotion, this);
  }
  mouseDown(event: EventMouse) {
    console.log("mouseDown", event);
  }
  keyDown(event: EventKeyboard) {
    console.log(event.keyCode);
    switch (event.keyCode) {
      case KeyCode.KEY_A:
        console.log("A");
        break;
    }
  }
  devicemotion(event: EventAcceleration) {
    console.log(event.acc);
  }
}
```

### 节点事件

需要依赖 UITransform 组件

| 输入事件 | 事件类型                    | 回调参数   | 说明                 |
| -------- | --------------------------- | ---------- | -------------------- |
| 鼠标事件 | Node.EventType.MOUSE_DOWN   | EventMouse | 鼠标按下             |
| 鼠标事件 | Node.EventType.MOUSE_UP     | EventMouse | 鼠标释放             |
| 鼠标事件 | Node.EventType.MOUSE_ENTER  | EventMouse | 鼠标移入             |
| 鼠标事件 | Node.EventType.MOUSE_MOVE   | EventMouse | 鼠标移动             |
| 鼠标事件 | Node.EventType.MOUSE_LEAVE  | EventMouse | 鼠标移出             |
| 鼠标事件 | Node.EventType.MOUSE_WHEEL  | EventMouse | 鼠标滚轮滚动         |
| 触摸事件 | Node.EventType.TOUCH_START  | EventTouch | 触摸开始             |
| 触摸事件 | Node.EventType.TOUCH_MOVE   | EventTouch | 触摸移动             |
| 触摸事件 | Node.EventType.TOUCH_END    | EventTouch | 触摸结束，节点内离开 |
| 触摸事件 | Node.EventType.TOUCH_CANCEL | EventTouch | 触摸结束，节点外离开 |

```TypeScript
import { _decorator, Component, Node, EventMouse } from "cc";
const { ccclass } = _decorator;

@ccclass("component")
export class component extends Component {
  start() {
    // 监听事件
    this.node.on(Node.EventType.MOUSE_DOWN, this.mouseDown, this);
  }
  mouseDown(event: EventMouse) {
    console.log("mouseDown", event);
  }
}
```

**事件冒泡**

```TypeScript parent.ts
import { _decorator, Component } from "cc";
import { EventCustom } from "./event-custom";
const { ccclass } = _decorator;

@ccclass("parent")
export class parent extends Component {
  start() {
    this.node.on("first", this.firstEvent, this);
  }
  firstEvent(event: EventCustom) {
    console.log("parent");
  }
}
```

```TypeScript child.ts
import { _decorator, Component } from "cc";
import { EventCustom } from "./event-custom";
const { ccclass } = _decorator;

@ccclass("child")
export class child extends Component {
  start() {
    this.node.on("first", this.firstEvent, this);
  }
  firstEvent(event: EventCustom) {
    console.log("child");
    // 终止冒泡
    event.propagationStopped = true;
  }
}
```

```TypeScript grandson.ts
import { _decorator, Component } from "cc";
import { EventCustom } from "./event-custom";
const { ccclass } = _decorator;

@ccclass("grandson")
export class grandson extends Component {
  start() {
    this.node.on("first", this.firstEvent, this);
    // 发射事件，bubbles 参数定义是否冒泡，系统事件默认冒泡，自定义事件默认不冒泡
    this.node.dispatchEvent(new EventCustom("first", true));
  }
  firstEvent(event: EventCustom) {
    console.log("grandson");
  }
}
```

```TypeScript event-custom.ts
import { Event } from "cc";

export class EventCustom extends Event {
  constructor(type: string, bubbles?: boolean, attribute?: any) {
    super(type, bubbles);
    this.attribute = attribute;
  }
  // 自定义属性
  attribute: any;
}
```

**事件穿透**

```TypeScript node-a
import { _decorator, Component, Node, EventTouch } from "cc";
const { ccclass } = _decorator;

@ccclass("nodea")
export class nodea extends Component {
  start() {
    this.node.on(Node.EventType.TOUCH_START, this.touchStart, this);
  }
  touchStart(event: EventTouch) {
    console.log("node-a");
  }
}
```

```TypeScript node-b
import { _decorator, Component, Node, EventTouch } from "cc";
const { ccclass } = _decorator;

@ccclass("nodeb")
export class nodeb extends Component {
  start() {
    this.node.on(Node.EventType.TOUCH_START, this.touchStart, this);
  }
  touchStart(event: EventTouch) {
    console.log("node-b");
    // 开启事件穿透
    event.preventSwallow = true;
  }
}
```
