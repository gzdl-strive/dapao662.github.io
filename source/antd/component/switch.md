---
title: Switch
date: 2020-10-29 14:45:50
tag: Ant Design Pro
---

### react中编辑页面给switch赋值

```js
<Col span={8}>
   <Form.Item label="最新" name="isnew">
               <Switch />
    </Form.Item>
</Col>
```
**这边不管是使用Switch组件的defaultChecked还是使用Form表单的setFieldsValue()都不能正确显示**

**上[Ant Design官网](https://ant-design.gitee.io/components/form-cn/?theme=compact#API "form")上找switch的API也没有关于如何设置的问题，直到看到Form表单里Form.Item的这个属性:**
![image](/assets/antdImg/switch.png "image")
于是赋值`valuePropName=“checked”`
```js
<Col span={8}>
    <Form.Item label="最新" name="isnew" valuePropName="checked">
        <Switch />
    </Form.Item>
</Col>
```
>这回就可以正确赋值了!!


### onChange事件
```js
// 类组件
//onChange 变化时回调函数 Function(checked: boolean, event: Event)
<Switch onChange={(checked) => this.changeSwitch(checked, "abc")}/>
```
