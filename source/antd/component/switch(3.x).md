---
title: 3.x Switch 赋值问题
date: 2020-11-04 14:45:50
tag: Ant Design Pro
---

**如果使用的版本高于3.x的话，解决方法请移步[switch赋值](/antd/component/switch "switch赋值")**
```js
<Col span={8}>
   <Form.Item label="最新" name="isnew">
        {
            this.props.form.getFieldDecorator('status', {
                valuePropName: 'checked',
            })(	<Switch onChange={(checked) => this.changeSwitch(checked,"switch赋值")} checkedChildren="开启" unCheckedChildren="关闭"></Switch>)
        }
    </Form.Item>
</Col>
```

**如果报错getFieldDecorator is undefined...**
>没有因为没有使用Form.create()定义组件,配置如下：
```js
class CustomizedForm extends React.Component {}

CustomizedForm = Form.create({})(CustomizedForm);
```
>经过 `Form.create` 包装的组件将会自带 `this.props.form` 属性
注意：使用 `getFieldsValue` `getFieldValue` `setFieldsValue `等时，应确保对应的 `field` 已经用 `getFieldDecorator` 注册过了。
