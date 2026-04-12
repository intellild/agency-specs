## 1. 查找 useP2PAuthSync 使用位置

- [x] 1.1 搜索项目中所有使用 `useP2PAuthSync` 的文件
- [x] 1.2 确认移除 hook 后不影响组件功能

## 2. 修改 p2pConfigAtom

- [x] 2.1 将 `p2pConfigAtom` 从 `atomWithStorage` 改为 computed `atom`
- [x] 2.2 实现从 `authAtom` 派生 P2P 配置的逻辑

## 3. 移除 useP2PAuthSync

- [x] 3.1 从 `hooks.ts` 中移除 `useP2PAuthSync` 函数
- [x] 3.2 从 `index.ts` 中移除 `useP2PAuthSync` 导出

## 4. 更新使用方代码

- [x] 4.1 移除所有组件中 `useP2PAuthSync()` 的调用

## 5. 验证

- [x] 5.1 运行类型检查确保无编译错误
- [x] 5.2 验证 P2P 功能正常工作
