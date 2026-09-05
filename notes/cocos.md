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

### 示例代码

**创建组件**

```TypeScript
import { _decorator, Component } from "cc";
const { ccclass } = _decorator;

@ccclass("component")
export class component extends Component {
}
```

**节点和组件操作**

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

**计时器**

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
