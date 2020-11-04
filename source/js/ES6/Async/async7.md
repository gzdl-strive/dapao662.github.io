---
title: 结合await和任意兼容.then()的代码
date: 2020-11-04 15:42:02
tag: js
---

```js
const bluebird =require('bluebird');
/* 结合await和任意兼容.then()的代码
    使用bluebird库--yarn add bluebird
*/

async function main(){
    //隐式调用--即使await后不是Promise对象
    //const number = await 290;//相当于await Promise.resolve(290);
    console.log('waiting ....');
    await bluebird.delay(2000);//await可以库使用
    console.log('finish');
}
main();

```