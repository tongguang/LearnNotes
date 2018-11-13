#### UI优化
  - UI合批规则。
    - z==0 采用分层+重叠检测的规则。当前节点与节点树之前所有节点比较，设当前节点层级为L1，比较节点为L2，L1默认为1。 如果两个节点不重叠，L1不变，如果两个节点重叠，相同材质（可以认为是相同图集），L1 = max(L1, L2)。不同图集，L1 = max(L1, L2 + 1)。层级相同且图集相同的合批。层级不同，层级小的先渲染。层级相同，文字先渲染。
    - z！=0 合并时只会合并相邻层级，相同图集的元素。
  - 动静分离。一个界面动态改变的（Image改变颜色，sprite等，Text改变文字等，位置改变等）的放一个canvas，不变的放一个canvas。减少rebuild开销。
  - 默认不要勾选raycast
  - Text少用outline
  - 不要用透明度为0的Image来响应事件检测，自己写个不填充的。
  - 纹理
    - 纹理压缩。
    - 图集划分明确些 一般一个功能用到自己功能的图集+公用图集。
    - 关闭mipmap
    - Read/Write Enableed 开了会增加一倍纹理内存，确定这张纹理是否需要开启，没特殊情况一般是关掉的。

#### DrawCall
    - 静态合批 1.场景直接勾选static，mesh会增大一倍，导致包体和内存增大。2.代码调用StaticBatchingUtility.Combine 增加内存和cpu消耗
    - 动态合批 相同材质的小于900顶点属性的自动合批，增加一些cpu消耗。有些因素会打断动态合批，比如scale，具体可以看官网。
    - GPU Instance  将使用相同mesh，相同材质球的物体通过调用底层glDrawElementsInstanced之类的api一次性提交给gpu，减少drawcall次数，例如草，数目， skinmesh通过修改也能使用。

####  动画优化
  - 浮点数精度优化（一般精确到.3f就行）
  - 一般模型的缩放曲线可以去掉

####  GC
  - 对象池
  - StringBuilder代替 +
  - 不要同一帧new太多 可以分帧new
  - 不要在update中new

####  音效
  - 背景音乐 LoadType使用Streaming
  - 音效 一般LoadType使用CompressedInMemory，特别频繁的小文件可以用DecompressOnLoad（不过占用内存大很多，10倍。。。）
  - 压缩一般vorbis，一般不勾选Preload audio data

####  模型
  - 控制骨骼数量
  - 控制skinned mesh render数量（一个模型最好1个）
  - mipmap lod 看情况开启， 锁视角的3d游戏基本是不需要开的

####  场景
  - 实时光照相关的能不用就不用。

####  Mesh
  - Normal、Color和Tangent属性不需要就不要导出，Draw Call Batching 如果有些有这些属性有些没有，那么会导致合批的所有Mesh都带上这些属性，增加内存。
  - 顶点数量减少

####  shader
  - 使用Unity上的Mobile版本
  - 减少半透明使用
  - 光照贴图
  - 少用pow,sin,cos， 少用if分支
  - fixed、half、float 使用够用的类型

#### 总结
  - 总的来说就是优化cpu，gpu，内存。
  - cpu
    - gc
    - drawcall
    - 代码运行逻辑占用
    - 实时光，阴影。
    - 物理组件，mesh collider
    - 在start中Getcomponent缓存
  - gpu
    - 纹理压缩，mesh优化，shader优化
  - 内存
    - mono托管内存优化
    - unity加载资源占用优化


#### 资料
  - 优化 https://blog.csdn.net/candycat1992/article/details/42127811
  - 优化 http://gad.qq.com/article/detail/287508
  - 模型导入 http://gad.qq.com/article/detail/39772
