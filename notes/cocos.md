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
- Node.Scale 缩放
- Node.Mobility 可移动性（光照相关）
- Node.Layer 可见性
