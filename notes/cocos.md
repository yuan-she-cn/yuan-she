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
