<template>
  <div>
    <SkuForm
      :source-attribute="sourceAttribute"
      :attribute.sync="attribute"
      :sku.sync="sku"
      :structure="structure"
    />
    <el-row type="flex" :gutter="20">
      <el-col>
        <el-divider content-position="left">attribute 数据</el-divider>
        <pre><code>{{ attribute }}</code></pre>
      </el-col>
      <el-col>
        <el-divider content-position="left">sku 数据</el-divider>
        <pre><code>{{ sku }}</code></pre>
      </el-col>
    </el-row>
  </div>

</template>

<script>
    export default {
        name: "mytest2",
        components: {},
        data() {
            return {
                sourceAttribute: [
                    {
                        name: '颜色',
                        item: ['黑', '金', '白']
                    },
                    {
                        name: '内存',
                        item: ['16G', '32G']
                    }
                ],
               /* structure: [
                    {name: 'price', type: 'input', label: '价格1'},
                    {name: 'stock', type: 'input', label: '库存1'},
                    {name: 'wo', type: 'input', label: 'wo'}
                ],*/
                attribute: [],
                sku: []
            }
        },
        methods: {}
    }
</script>

<style scoped>

</style>
