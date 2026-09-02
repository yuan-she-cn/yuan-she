# Cocos

以下示例均在 Cocos Creator 3.8.8 测试通过。

## 场景

- 创建场景：Assets -> 右键 -> Create -> Scene
- 切换场景：Assets -> 双击

```TypeScript
// 切换场景
director.loadScene
```

### 场景属性

- Auto Release Assets 是否自动释放

```TypeScript
// 释放资源
assetManager.releaseAsset
// 保留资源
Asset.addRef
```

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

```TypeScript
// 获取 Rotation
Node.eulerAngles
```

- Node.Scale 缩放
- Node.Mobility 可移动性（光照相关）
- Node.Layer 可见性

## 组件

- 添加组件：Inspector -> Add Component

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
