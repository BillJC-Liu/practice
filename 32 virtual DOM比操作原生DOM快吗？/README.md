# Virtual DOM真的比操作原生DOM快吗？
* 首先先了解React的Virtual DOM。什么是Virtal DOM？（主要使用React进行开发，这里针对React来了解）
  在React中，render执行的结果得到的并不是真正的DOM节点，结果仅仅是轻量级的JavaScript对象，称之为Virtual DOM。
  virtual DOM是React的一大亮点，具有batching（批处理）和高效的Diff算法。这让我们可以无须担心性能问题而“毫无顾忌”的算