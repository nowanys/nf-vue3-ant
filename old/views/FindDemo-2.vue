<template>
  <div class="home">
    <h1>查询演示</h1>
    <div style="background-color:#dddddd;height:400px;width:100px;float:left;">
      <a href="#" @click="myClick('companyFind')">公司信息</a> <br>
      <a href="#" @click="myClick('personFind')">员工信息</a>
    </div>
    <div style="background-color:#eee;height:400px;width:1100px;float:left;">
      <!--多列表单-->
      <div class="ant-table ant-table-body ant-table-default ant-table-bordered" >
        <table role="all">
          <tbody class="ant-table-tbody">
            <template v-for="tr in rowCount" :key="tr"><!--循环行-tr-->
              <tr>
                <template v-for="td in findMeta.colCount" :key="td"><!--循环列-td-->
                  <template  v-if="!isEnd(tr, td)"><!--判断meta是否循环完毕-->
                    <td align="right" style="padding:3px 3px;height:20px">
                      {{getMeta(tr,td).title}}：
                    </td>
                    <td align="left" style="padding:3px 3px;height:20px" v-if="!isEnd(tr, td)">
                      <nfInput v-model="modelValue[getMeta(tr,td).colName]"
                      :meta="getMeta(tr,td)" />
                    </td>
                  </template>
                  <template v-else><!--如果一行没满，补充缺失的 td-->
                    <td> </td><td> </td>
                  </template>
                </template>
              </tr><!--循环行-tr 结束 -->
            </template>
          </tbody>
        </table>
      </div>
      <!--生成查询语句-->
      <div align="left" style="padding:15px">
        <span v-for="(item,key,index) in modelValue" :key="index"><!--遍历model-->
          <template v-if="typeof item === 'object'"><!--判断是不是数组-->
            <template v-if="item.length == 2"> <!--判断数组长度-->
              {{key}} {{findWhere[item[0]].replace('{k}',item[1])}} and <br>
            </template>
            <template v-if="item.length == 3"> <!--判断数组长度-->
              {{key}} {{findWhere[item[0]].replace('{k1}',item[1]).replace('{k2}',item[2])}} and <br>
            </template>
          </template>
        </span>
      </div>
    </div>
    <div align="left" style="background-color:#EEEEFF;height:600px;width:300px;clear:both">
      {<br>
        <span v-for="(item, key, index) in modelValue" :key="index">
          <span v-if="typeof item === 'number' && !isNaN(item)">&nbsp;&nbsp;"{{key}}": {{item}}, <br></span>
          <span v-if="typeof item === 'string'">&nbsp;&nbsp;"{{key}}": "{{item}}", <br></span>
          <span v-if="typeof(item) ==='boolean'">&nbsp;&nbsp;"{{key}}": {{item}}, <br></span>
          <span v-if="typeof(item) ==='object'">
            &nbsp;&nbsp;"{{key}}": [<br>
            <span v-for="(opt, index) in item" :key="'opt'+index">&nbsp;&nbsp;&nbsp;&nbsp;{{opt}}, <br></span>
            &nbsp;&nbsp;]
          </span>
        </span>
      }
    </div>
    <div style="clear:both">
      {{modelValue}}
    </div>
  </div>
</template>

<script>
import { ref } from 'vue'
// import nfHelp from '@/components/nf-meta-help.vue'
import nfInput from '@/components/nf-find/nf-find-item.vue'

export default {
  name: 'FindDemo',
  components: {
    // nfHelp,
    nfInput
  },
  setup () {
    const json = require('./FindDemo.json') // 加载meta信息，json格式
    const modelValue = ref({}) // 放数据的model
    const findMeta = ref(json.companyFind.findMeta) // 查询表单的meta信息
    const findItem = ref(json.companyFind.findItem) // 表单需要的meta信息
    const findWhere = ref(json.findWhere) // 表单需要的meta信息
    const rowCount = ref(1) // 行数
    const tdCount = ref(1) // 控件数，遍历用
    const myClick = (key) => {
      // 更换表单的meta
      findItem.value = json[key].findItem
      findMeta.value = json[key].findMeta
      // 初始化
      tdCount.value = 1
      modelValue.value = {}
      // 创建model
      modelValue.value = {}
      for (var k in findItem.value) {
        var item = findItem.value[k]
        modelValue.value[item.colName] = ''
        tdCount.value += 1
      }
      // 计算行数
      rowCount.value = Math.ceil(findMeta.value.allFind.length / findMeta.value.colCount)
      // alert(rowCount.value)
    }
    myClick('companyFind')
    // 通过行、列计算meta的key
    const getMeta = (tr, td) => {
      var key = findMeta.value.allFind[(tr - 1) * findMeta.value.colCount + (td - 1)]
      return findItem.value[key]
    }
    // 通过行、列计算是否结束
    const isEnd = (tr, td) => {
      var count = (tr - 1) * findMeta.value.colCount + (td - 1)
      // alert(tdCount.value)
      return count >= findMeta.value.allFind.length
    }
    return {
      modelValue,
      findItem,
      findMeta,
      findWhere,
      rowCount,
      tdCount,
      myClick,
      getMeta,
      isEnd
    }
  }
}
</script>
