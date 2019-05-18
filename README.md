## 四维项目升级汇总

### 主要升级包
> 升级前
```bash
"tinper-bee": "1.5.0",
"yyuap-bpm": "^0.3.20",
"yyuap-ref": "^1.1.64"
```
> 升级后
```bash
"tinper-bee": "^2.0.10",
"bee-message": "^2.0.4", (单独引)
"pap-refer": "^1.0.2", (平台级参照)
"ref-multiple-table": "^2.0.2", (基础参照)
"yyuap-bpm": "^0.3.38", (流程组件里依旧是 还是用苏飞提供的参照)
```
### 升包内存溢出问题 (修改package.json)
项目开发中，当项目过大的时候，node内存溢出而无法启动工程或者无法build项目，即是项目构建过程中频繁报内存溢出：FATAL ERROR: CALL_AND_RETRY_LAST Allocation failed - process out of memory
> 修改前
```bash
"dev": "cross-env NODE_ENV=production uba server --chunks ",
"build": "cross-env NODE_ENV=production uba build",
```
> 修改后
+ window
```bash
"dev":"cross-env NODE_ENV=production node --max_old_space_size=9000 ./node_modules/uba-build/bin/uba-server --chunks",
"build":"cross-env NODE_ENV=production node --max_old_space_size=9000 ./node_modules/uba-build/bin/uba-build -p",
```
+ mac 
```bash
 "dev": "node --max_old_space_size=9000 ./node_modules/.bin/uba-server --chunks",
 "build":"node --max_old_space_size=9000 ./node_modules/.bin/uba build"
```

### 参照
* 参照组件 [https://design.yonyoucloud.com/tinper-acs/ref-multiple-table](https://design.yonyoucloud.com/tinper-acs/ref-multiple-table)

* 参照demo
  + 先登录 (admin/123qwe)
    [http://refdemo.app.yyuap.com/wbalone/pages/login/login.html?r=L3diYWxvbmUv](http://refdemo.app.yyuap.com/wbalone/pages/login/login.html?r=L3diYWxvbmUv)

  + 再访问
    [http://refdemo.app.yyuap.com/dist/ucf-publish/iuap-pap-demo-fe/ref-demo/index.html](http://refdemo.app.yyuap.com/dist/ucf-publish/iuap-pap-demo-fe/ref-demo/index.html)
* 参照 pap-refer (基于应用平台和基本参照组件封装的应用级参照组件，暂时无对外文档)
* 项目参照
   * 安装 pap-refer `npm install pap-refer@1.0.2
   * 引入参照组件和样式
   ```js
   import {RefMultipleTableWithInput,RefTreeTableWithInput} from 'pap-refer';
   import 'pap-refer/dist/index.css
   ```
   * 单表参照示例代码
   ```js
      <RefMultipleTableWithInput
          placeholder="请选择客户" // 参照input placeholder
          title='请选择客户'  // 参照弹框 title
          backdrop={true}  //弹出层是否有模态层，true 显示，false 不显示
          multiple={false} // 单选
          strictMode={true}  // 严格模式
          miniSearch={true} // 展示简单搜索
          valueField={"refpk"} //待提交的value的键。或者说指定真实数据的键。要求具有唯一性
          displayField={"{name}"} //input中显示的内容的格式和过滤列表显示的内容格式。
          param={{//url请求参数
              refCode: 'xwl_customer' // refcode  后端给
          }}
          refModelUrl={{
              tableBodyUrl: '/bankLoan/common-ref/blobRefTreeGrid',//表体请求
              refInfo: '/bankLoan/common-ref/refInfo',//表头请求
          }}
          showLoading={false}
          filterUrl='/bankLoan/common-ref/filterRefJSON' //快捷录入接口
          matchUrl='/bankLoan/common-ref/matchPKRefJSON' // 查询接口
          onSave={(values) => {  // 确定事件回调
              const idArray = values.map(v => v.id); // 获取选中行的数据id
              self.setState({
                  refKeyArraycustomer_id: idArray, // 将选中的id 更新的组件的 state 中
              })
          }}

          {...getFieldProps('customer_id', {
              initialValue: `{"refname":"${customer_name}","refpk":"${customer_id}"}`, // 参照初始化值
              rules: [{
                  message: '请输入内容', //  参照验证失败错误提示
                  pattern: /[^{"refname":"","refpk":""}]/   // 参照验证规则
              }]
          })}
      >
      </RefMultipleTableWithInput>
   ```
    * 左数右表参照示例代码
   ```js
    <RefTreeTableWithInput
      title='组织部门人员'
      textOption={{
          menuTitle: '组织',
          tableTitle: '人员',
      }}
      param={{//url请求参数
          refCode: 'xwl_dept',
      }}
      multiple={false}
      refModelUrl={{
          treeUrl: '/bankLoan/common-ref/blobRefTree',  // 左树数据请求
          refInfo: '/bankLoan/common-ref/refInfo',// 表头请求
          tableBodyUrl: '/bankLoan/common-ref/blobRefTreeGrid',// 表体请求
      }}
      matchUrl='/bankLoan/common-ref/matchPKRefJSON' // 快捷录入接口
      filterUrl='/bankLoan/common-ref/filterRefJSON' // 查询接口
      displayField='{refname}'    // input中显示的内容的格式和过滤列表显示的内容格式。
      valueField='refpk' //待提交的value的键。或者说指定真实数据的键。要求具有唯一性
      
      {...getFieldProps('orgId', {
          initialValue:  `{"refname":"${orgName}","refpk":"${orgId}"}`, // 参照初始化值,
          rules: [{
              message: '提示：请选择',
              pattern: /[^{"refname":"","refpk":""}|{"refpk":"","refname":""}]/
          }]
      })}
      
      onSave={(values) => {  // 确定事件回调
          const idArray = values.map(v => v.id); // 获取选中行的数据id
          self.setState({
              refKeyArrayorgId: idArray, // 将选中的id 更新的组件的 state 中
          })
      }}
  />
   ```
##### 单表参照API

参数 | 类型 |默认值| 说明 | 必选
---|---|--- | --- | ---
title |``string``|空 |打开上传的模态框显示的标题文字 | 否
className |`string`|空 | 参照class样式，作用于弹出层的样式，默认为空。 | 否
<span style="color:red;">*</span> strictMode|`bool`|false |严格模式，此配置项为业务优化使用，当为 true（启用） 时每次打开弹出层都会刷新数据，若不启用时第一次数据加载正常且部为第一页数据时不再刷新数据 | 否
multiple |`bool`| false |是否单选， true 多选，false 单选， 同时多选时不会有确认和取消按钮，多选时会出现复选框 | 否
backdrop |`bool`| true |弹出层是否有模态层，true 显示，false 不显示 | 否
param|`object`| {} |refModelUrl 中接口请求的参数 | 否
refModelUrl |`object`|{tableBodyUrl:'',refInfo:''} |弹出层数据接口地址，为了兼容其他参照保留了多连接配置。<br/>如：<br/>{ <br/>tableBodyUrl:'blobRefTreeGrid',//表体请求<br />tableBarUrl:'refInfo',//表头请求<br />} | 是
matchUrl| ``string``|空|查询并校验 value 中的 refpk 对应参照的详细记录并且修改checkedArray。 当需要请求接口获取完整数据时，可以传入checkedArray=[]，value中refpk不为空就可以|否
checkedArray| `array`|[]|已选择数据。注意，当使用RefMultipleTableWithInput 或者 搭配refcorewithinput使用时，checkedArray这个参数不起效且初始值默认[] | 否
filterUrl| ``string``|空|快捷录入接口。|否
<s>fliterColumn| ``array``|空|<s>行内筛选配置的筛选项，无则没有，详情请查看参数详解</s>|否</s>
lang|`string`| `zh_TW` |多语配置，详情查看参数详解 | 否
buttons|`object`| - |{buttons:{cancelText:'',confirmText:'',okText:''}} 按钮文字展示| 否
emptyBut| `bool` | false| 清空按钮是否展示 |否
onSave |`function( record:object )`|-- |保存回调函数，返回已选择的记录详细数据。 | 否
onCancel  |`function()` | -- | 关闭弹出层 | 否
jsonp| `bool` | false | refInfo和matchUrl的request请求传参jsonp| 否
headers| -- | -- | refInfo、tableBodyUrl和matchUrl的request请求传参headers| 否
onMatchInitValue| `function(data)` | -- | 返回matchUrl请求的全部数据|否
onAfterAjax| `function(data)`| -- |tableBodyUrl请求后的回调，返回全部数据| 否
miniSearch| `Boolean`|true|默认是简单搜索|否
size|`String`|'lg'|modal的size|否
onClear|`function`|-|复杂搜索清空操作回调|否
onSearch|`function`|-|复杂搜索搜索操作回调，返回搜索values|否
matchData | `Array` | [] | 选中的节点(<span style="color: red; font-size: 15px;">macthData和value配合使用</span>)| 否
theme| `String` | 'ref-red' | 参照主题，现在就两种选择'ref-red'或者'ref-blue' | 否
searchPanelLocale | `Object` | {'title': '条件筛选EN','resetName': '重置En','searchName': '查询EN','down':'打开EN','up':'关闭EN',} | 复杂搜索标题，按钮的文字等信息 | 否

##### 左树右表 API
参数 | 类型 |默认值| 说明 | 必选
---|---|--- | --- | ---
title |``string``|空 |打开上传的模态框显示的标题文字 | 否
className |`string`|空 | 参照class样式，作用于弹出层和 RefTreeTableWithInput 输入框的样式，默认为空。 | 否
searchable |`bool`|true |是否显示搜索框，弹出层是否带有搜索框，true 显示，false 不显示。 | 否
multiple |`bool`| false |是否单选， true 单选，false 多选 | 否
backdrop |`bool`| true |弹出层是否有模态层，true 显示，false 不显示 | 否
lang|`string`| `zh_TW` |多语配置，详情查看参数详解 | 否
buttons |`object`| `okText`: "确认", //确认按钮<br/>`cancelText`: "取消", //取消按钮<br/>`clearText`: "清空已选" //清空已选按钮|弹出层工具栏三个按钮的文字，若 
hasPage |`bool`|true |是否有分页条，true 有，false 没有 | 否
tabData |`array`|true |当参照有多个类型的数据时可启用tab标签页来区分，当个点击页签的时候，会根据配置的key再去查询。如：<br />[<br />{"title":"常用","key":"commonUse"},<br /> {"title":"全部","key":"total"},<br />{"title":"推荐","key":"recommed"}<br />] | 否
defaultExpandAll |`bool`|false |展开所有节点 true 展开，false 不展开 | 否
checkStrictly |`bool`|false|heckable状态下节点选择完全受控（父子节点选中状态不再关联）, false 关联选择，true 不关联| 否
param |`objecet`|{} |接口请求参数 | 是
refModelUrl |`object`|{treeUrl:'',tableBodyUrl:'',refInfo:'',保留了多连接配置。<br/>如：<br/>{ <br/> treeUrl: '/api/user/treeUrl.json',<br/>tableBodyUrl:'blobRefTreeTableGrid',//表体请求<br />refInfo:'refInfo',//表头请求<br />} | 是
onSave |`function( record:object )`|-- |保存回调函数，返回已选择的记录详细数据。 | 否
onCancel | `function(  )`|-- |关闭弹出层 | 否
jsonp| `bool` | false | treeUrl、tableBodyUrl、refInfo、matchUrl的request请求传参jsonp| 否
onAfterAjax| `function(data)`| -- |treeUrl和tableBodyUrl请求后的回调，返回全部数据| 否
onTreeLoading | `function(state)` | --|回调函数，三种结果'loaded'、'loading'、'fail',表示树的请求三种状态|否
menuTitle | `String` | ''| 左树的标题| 否
tableTitle | `String` | '' | 右表的标题 | 否
nodeDisplay |<code>string 或 function</code>| `{refname}` |节点渲染时可匹配的内容，这里为了提供根据数据渲染节点图标使用 | 否
lazyModal |`bool`| false |数据懒加载模式，true 启用，false 不启用 | 否
param|`object`| {} |refModelUrl 中接口请求的参数 | 否
searchable |`bool`|true |是否显示搜索框，弹出层是否带有搜索框，true 显示，false 不显示。 | 否
defaultExpandAll |`bool`|false |展开所有节点 true 展开，false 不展开。前提lazyModal是true| 否
onChange| `function(checkedArray)`| -- | 左树选中回调函数| 否
checkAllChildren| `boolean`| false|选中子节点|否
theme| `String` | 'ref-red' | 参照主题，现在就两种选择'ref-red'或者'ref-blue' | 否
<s>emptyBut|`bool`|true |是否显示清空按钮，true 显示，false 不显示、sebottomButton 为 false 则该配置无效。| 否 </s>
<s> bottomButton |`bool`|true | 是否显示弹出层下边框工具栏， false不显示 true 显示`注意该属性为临时兼容配置后期可能随时会弃用` | 否 </s>
miniSearch(右表)| `Boolean`|true|左表默认是简单搜索|否
matchUrl| ``string``|空|查询并校验 value 中的 refpk 对应参照的详细记录并且修改checkedArray。 当需要请求接口获取完整数据时，可以传入checkedArray=[]，value中refpk不为空就可以|否
checkedArray| `array`|[]|已选择数据。注意，当使用RefTreeTableWithInput 或者 搭配refcorewithinput使用时，checkedArray这个参数不起效且初始值默认[] | 否
headers(右表)| -- | -- | refInfo、tableBodyUrl和matchUrl的request请求传参headers| 否
onMatchInitValue(右表)| `function(data)` | -- | 返回matchUrl请求的全部数据|否



##### 参数解析
value、displayField value和displayField是针对input框来说。value格式必须符合'{"refname":"初级-T1","refpk":"level1"}'。refname字段不可变，refpk是该数据键，要求具有唯一性。 displayField确定input中显示内容的格式和过滤列表显示内容的格式

value、valueFiled value是input需要使用的数据，要求如上。 valueFiled指定数据源的键，要求具有唯一性。 因此value中refpk指定值应与valueFiled取值一致。

value、matchData value初始化input框值，matchData是指定参照中选中的节点。如果value有值matchData为空，那么input有值但是参照无选中数据，反之value空值matchData有值，那么input为空但是参照有选中数据
  

### 门户
* 某一wbalone项目 [https://gitee.com/liushaozhen/sany-wbalone/](https://gitee.com/liushaozhen/sany-wbalone/) 
* 改造后轻量级门户项目[https://github.com/ranyanchuan/reac_ap_fe](https://github.com/ranyanchuan/reac_ap_fe)

### 流程
* 安装最新流程包 `npm install yyuap-bpm@0.3.38 `
* 安装最新表格参照 `npm install ref-multiple-table@2.0.2`
* 添加项目级 BPM (源代码在工程中)
```js
banklaon_react/src/components/Bpm/BpmButtonSubmit.js
banklaon_react/src/components/Bpm/common.js
banklaon_react/src/components/Bpm/BpmTaskApprovalWrap.js
```
* 在 `banklaon_react/src/pages/xml_sale_order/components/SaleOrder-edit/edit.js`中引入项目级 BPM
```bash
import {BpmTaskApprovalWrap} from 'yyuap-bpm'; // 只引入 BpmTaskApprovalWrap，去掉之前引入的  BpmButtonSubmit
import BpmButtonSubmit from "components/Bpm/BpmButtonSubmit";  //项目级 BPM
import BpmTaskApprovalWrap from "components/Bpm/BpmTaskApprovalWrap";

```
