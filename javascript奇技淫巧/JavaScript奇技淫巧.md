# 复杂判断逻辑简化
> Object Map
```
const actions = {
  '1': ['processing','IndexPage'],
  '2': ['fail','FailPage'],
  '3': ['fail','FailPage'],
  '4': ['success','SuccessPage'],
  '5': ['cancel','CancelPage'],
  'default': ['other','Index'],
}
/**
 * 按钮点击事件
 * @param {number} status 活动状态：1开团进行中 2开团失败 3 商品售罄 4 开团成功 5 系统取消
 */
const onButtonClick = (status)=>{
  let action = actions[status] || actions['default'],
      logName = action[0],
      pageName = action[1]
  sendLog(logName)
  jumpTo(pageName)
}

```
* 实现简单同时也适用于复杂判断

> new Map(object)
```
const actions = ()=>{
  const functionA = ()=>{/*do sth*/}
  const functionB = ()=>{/*do sth*/}
  return new Map([
    [{identity:'guest',status:1},functionA],
    [{identity:'guest',status:2},functionA],
    [{identity:'guest',status:3},functionA],
    [{identity:'guest',status:4},functionA],
    [{identity:'guest',status:5},functionB],
    //...
  ])
}

const onButtonClick = (identity,status)=>{
  let action = [...actions()].filter(([key,value])=>(key.identity == identity && key.status == status))
  action.forEach(([key,value])=>value.call(this))
}
```
**理解**
* 较为严谨的匹配方式,但是实现较为复杂
* 适用于较为复杂的逻辑判断

> new Map(正则)
```
const actions = ()=>{
  const functionA = ()=>{/*do sth*/}
  const functionB = ()=>{/*do sth*/}
  const functionC = ()=>{/*send log*/}
  return new Map([
    [/^guest_[1-4]$/,functionA],
    [/^guest_5$/,functionB],
    [/^guest_.*$/,functionC],
    //...
  ])
}

const onButtonClick = (identity,status)=>{
  let action = [...actions()].filter(
    ([key,value])=>(key.test(`${identity}_${status}`))
  )
  action.forEach(([key,value])=>value.call(this))
}

```
**理解**
* 更加具有扩展性,但也加大出错几率
* 实现较为复杂
* 适用于较为复杂的逻辑判断