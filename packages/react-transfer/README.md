Transfer 穿梭框
===

[![Open in unpkg](https://img.shields.io/badge/Open%20in-unpkg-blue)](https://uiwjs.github.io/npm-unpkg/#/pkg/@uiw/react-transfer/file/README.md)
[![NPM Downloads](https://img.shields.io/npm/dm/@uiw/react-transfer.svg?style=flat)](https://www.npmjs.com/package/@uiw/react-transfer)
[![npm version](https://img.shields.io/npm/v/@uiw/react-transfer.svg?label=@uiw/react-transfer)](https://npmjs.com/@uiw/react-transfer)

选择一个或以上的选项后，点击左右的方向按钮，可以把选中的选项移动到另一栏为选中。在 v4.14.0+ 添加。

## 基础用法

最简单的用法。

```jsx mdx:preview&bg=#fff
import React from 'react';
import { Transfer, Row } from 'uiw';

function Demo() {
  const options = [
    { label: '武汉市',  key: 1 },
    { label: '汉南区1', key: 5 },
    { label: '汉南区2', key: 6 },
    { label: '汉南区3', key: 7 },
  ]
  const [value,valueSet] = React.useState([{ label: '武汉市',  key: 1 }])

  return (
    <Row style={{ flexDirection:'column' }} >
      <Transfer
        options={options}
        value={value}
        onChange={(transfer,option)=>{
          valueSet(option)
        }}
      />
    </Row>
  );
}

export default Demo;
```

## 树形节点

使用 [`Tree`](#/components/tree) 结构作为选项节点

```jsx mdx:preview&bg=#fff
import React from 'react';
import { Transfer, Row } from 'uiw';

function Demo() {
const options = [
    {
      label: '武汉市',
      key: 1,
      children: [
        { label: '新洲区', key: 2, disabled: true },
        { label: '武昌区', key: 3 },
        {
          label: '汉南区',
          key: 4,
          children: [
            { label: '汉南区1', key: 5 },
            { label: '汉南区2', key: 6 },
            { label: '汉南区3', key: 7 },
          ]
        },
      ]
    }
  ];

  const [value,valueSet] = React.useState([{ label: '汉南区1', key: 5 }])

  return (
    <Row style={{ flexDirection:'column' }} >
      <Transfer
        options={options}
        value={value}
        onChange={(transfer,option)=>{
          valueSet(option)
        }}
      />
    </Row>
  );
}

export default Demo;
```

## 全部选择

将`selectedAll`设置为`true`,启用全部勾选功能

```jsx mdx:preview&bg=#fff
import React from 'react';
import { Transfer, Row } from 'uiw';

function Demo() {

  const options = [
    {
      label: '武汉市',
      key: 1,
      children: [
        { label: '新洲区', key: 2 },
        { label: '武昌区', key: 3 },
        {
          label: '汉南区',
          key: 4,
          children: [
            { label: '汉南区1', key: 5 },
            { label: '汉南区2', key: 6 },
            { label: '汉南区3', key: 7 },
          ]
        },
      ]
    }
  ];

  const [value,valueSet] = React.useState([{ label: '武昌区', key: 3 }, { label: '汉南区1', key: 5 }])

  return (
    <Row style={{ flexDirection:'column' }} >
      <Transfer
        options={options}
        selectedAll={true}
        value={value}
        onChange={(transfer,option)=>{
          valueSet(option)
        }}
      />
    </Row>
  );
}

export default Demo;
```

## 搜索选项

将`showSearch`设置为`true`，启用选项搜索框

```jsx mdx:preview&bg=#fff
import React from 'react';
import { Transfer, Row } from 'uiw';

function Demo() {
const options = [
    {
      label: '武汉市',
      key: 1,
      children: [
        { label: '新洲区', key: 2, disabled: true },
        { label: '武昌区', key: 3 },
        {
          label: '汉南区',
          key: 4,
          children: [
            { label: '汉南区1', key: 5 },
            { label: '汉南区2', key: 6 },
            { label: '汉南区3', key: 7 },
          ]
        },
      ]
    }
  ];

  const [value,valueSet] = React.useState([
    { label: '武昌区', key: 3 },
    { label: '汉南区1', key: 5 },
    { label: '汉南区2', key: 6 },
  ])

  return (
    <Row style={{ flexDirection:'column' }} >
      <Transfer
        options={options}
        showSearch={true}
        placeholder="搜索内容"
        value={value}
        onChange={(transfer,option)=>{
          valueSet(option)
        }}
      />
    </Row>
  );
}

export default Demo;
```

## Form中使用

```jsx mdx:preview&bg=#fff
import React from 'react';
import { Transfer, Form, Row, Col, Button } from 'uiw';

function Demo() {

  const options = [
    { label: '武汉市',  key: 1 },
    { label: '汉南区1', key: 5 },
    { label: '汉南区2', key: 6 },
    { label: '汉南区3', key: 7 },
  ]

  const [value,valueSet] = React.useState([{ label: '武汉市',  key: 1 }])
  const form=React.useRef()

  const setValue=()=>{
    form.current.setFields({
     transfer: [
        { label: '汉南区1', key: 5 },
        { label: '汉南区3', key: 7 },
      ]
    })
  }

  const resetValue=()=>{
    form.current.resetForm()
  }

  return (
    <Form
        ref={form}
        onSubmitError={(error) => {
          if (error.filed) {
            return { ...error.filed };
          }
          return null;
        }}
        onSubmit={({initial, current}) => {
          const errorObj = {};
          if (!current.searchTree) {
            errorObj.searchTree = '默认需要选择内容，选择入内容';
          }
          if(Object.keys(errorObj).length > 0) {
            const err = new Error();
            err.filed = errorObj;
            Notify.error({ title: '提交失败！', description: '请确认提交表单是否正确！' });
            throw err;
          }
          Notify.success({
            title: '提交成功！',
            description: `表单提交成功，选择值为：${current.selectField}，将自动填充初始化值！`,
          });
        }}
        fields={{
          transfer: {
            initialValue:[{ label: '汉南区2', key: 6 },{ label: '汉南区3', key: 7 },],
            children: (
              <Transfer options={options}/>
            )
          }
        }}
      >
        {({ fields, state, canSubmit }) => {
          return (
            <div>
              <Row style={{display:'flex',flexDirection:'column'}}>
                <Col >{fields.transfer}</Col>
              </Row>
              <Row>
                <Col fixed>
                  <Button disabled={!canSubmit()} type="primary" htmlType="submit">提交</Button>
                  <Button onClick={setValue} type="primary" >setValue</Button>
                  <Button onClick={resetValue} type="primary" >重置</Button>
                </Col>
              </Row>
              <Row>
                <Col>
                  <pre style={{ padding: 10, marginTop: 10 }}>
                    {JSON.stringify(state.current, null, 2)}
                  </pre>
                </Col>
              </Row>
            </div>
          )
        }}
      </Form>
  );
}

export default Demo;
```

## Props

| 参数 | 说明 | 类型 | 默认值 |
|--------- |-------- |--------- |-------- |
| bodyStyle | 选项区域样式 | Object | { height: 200px, overflow-y: auto } |
| onChange | 点击穿梭时回调 | (arrow, selectkeys) => void | - |
| value | 指定当前选中的条目 | [{label, key }] | - |
| options | 下拉数据源,可参考Tree下拉数据源 | [{ label, key, children: [{label, key}] }] | - |
| showSearch | 启用搜索 | boolean | false |
| placeholder | 搜索输入框文字 | string | - |
| onSearch | 搜索时回调函数 |  (arrow, searchValue) => void | - |
| selectedAll | 启用全部勾选 |  boolean | false |
