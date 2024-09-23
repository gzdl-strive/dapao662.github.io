---
title: 深拷贝
date: 2022-08-07 20:32:24
toc: true
---

```js
const cloneDeep = (target, hash = new WeakMap()) => {
  // 对传入的参数进行处理————如果是非对象类型或Null直接返回即可
  if (typeof target !== 'object' || target === null) {
    return target;
  }
  // 哈希表中如果存在直接返回
  if (hash.has(target)) {
    return hash.get(target);
  }
  const cloneTarget = Array.isArray(target) ? [] : {};
  hash.set(target, cloneTarget);
  for (const i in target) {
    if (Object.prototype.hasOwnProperty.call(target, i)) {
      cloneTarget[i] = 
        typeof target[i] === 'object'
          && target[i] !== null
            ? cloneDeep(target[i], hash)
            : target[i];
    }
  }
  return cloneTarget;
}
```