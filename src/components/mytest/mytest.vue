<template>
  <div>
    <el-form>

      <!--SKU-->
      <div class="wrap" v-if="this.obj.length > 0">
        <div v-if="goodsId == '' || goodsId == undefined || goodsId == null">
          <div class="control-group choose_config">
            <el-form-item label="商品规格">
              <div class="controls">
                <div v-for="(item,index) in obj" :key="index">
                  <h3 class="Father_Title">{{Object.keys(item).join()}}</h3>
                  <ul :class="'Father_Item' + ' ' + 'Father_Item'+index">
                    <li class="myli" v-for="(val,index) in item[Object.keys(item).join()]" :key="index"><label><input
                      id="Checkbox0" type="checkbox" @change="init"
                      class="checkbox check_item" :value="val">{{val}}</label></li>
                  </ul>
                  <br>
                </div>
              </div>
            </el-form-item>
          </div>
          <!-- 生成表格 -->
          <div class="control-group mt10">
            <el-form-item>
              <div class="controls2" id="createTable"></div>
            </el-form-item>
          </div>
        </div>
      </div>
    </el-form>
  </div>

</template>

<script>
    import $ from 'jquery'

    export default {
        name: "mytest",
        components: {},
        data() {
            return {
                form: {
                    array1: ''
                },
                goodsId: '',
                obj: [
                    {内存: ["2G"]},
                    {容量: ["64G", "128G"]},
                    {尺寸: ["50", "55", "67"]}
                ]
            };
        },
        methods: {
            // 添加商品规格列表
            init() {
                /*$('.checkbox', parent).each(function () {
                    debugger
                    var bCheck2 = true;
                    if (_this.hasClass('check_all')) {
                        if (_this.get(0).checked) {
                            bCheck2 = true;
                            $('.check_item', parent).prop('checked', bCheck2);
                        } else {
                            bCheck2 = false;
                            $('.check_item', parent).prop('checked', bCheck2);
                        }
                        return false;
                    } else {
                        if ((!this.checked) && (!$(this).hasClass('check_all'))) {
                            bCheck2 = false;
                            $('.check_all', parent).prop('checked', bCheck2);
                            return false;
                        }
                    }
                    $('.check_all', parent).prop('checked', bCheck2);
                });*/
                this.Creat_Table();
                this.diplay();
            },
            Creat_Table() {

                //this.hebingFunction();
                var SKUObj = $('.Father_Title');
                var arrayTile = new Array(); // 表格标题数组
                var arrayInfor = new Array(); // 盛放每组选中的CheckBox值的对象
                var arrayColumn = new Array(); // 指定列，用来合并哪些列
                var bCheck = true; // 是否全选，只有全选，表格才会生成
                var columnIndex = 0;
                $.each(SKUObj, function (i, item) {

                    arrayColumn.push(columnIndex++);
                    arrayTile.push(SKUObj.eq(i).text().replace('：', ''));
                    var itemName = '.Father_Item' + i;
                    var bCheck2 = true; // 是否全选

                    // 获取选中的checkbox的值
                    var order = new Array();
                    $(itemName + ' .check_item:checked').each(function () {
                        order.push($(this).val());
                    });

                    arrayInfor.push(order);
                    if (order.join() == '') {
                        bCheck = false;
                    }
                })

                // 开始生成表格
                if (bCheck) {
                    $('#createTable').html('');
                    var table = $('<table id="process" class="columnList"></table>');
                    table.appendTo($('#createTable'));
                    var thead = $('<thead></thead>');
                    thead.appendTo(table);
                    var trHead = $('<tr></tr>');
                    trHead.appendTo(thead);
                    // 创建表头
                    var str = '';
                    $.each(arrayTile, function (index, item) {
                        str += '<th width="100">' + item + '</th>';
                    })
                    str += '<th  width="">商品价格</th><th width="">市场价格</th><th width="">库存</th><th width="">已售销量</th><th width="">库存预警值</th>';

                    trHead.append(str);
                    var tbody = $('<tbody></tbody>');
                    tbody.appendTo(table);
                    var zuheDate = this.doExchange(arrayInfor);
                    if (zuheDate.length > 0) {
                        //创建行
                        $.each(zuheDate, function (index, item) {
                            var td_array = item.split(',');
                            var tr = $('<tr></tr>');
                            tr.appendTo(tbody);
                            var str = '';
                            $.each(td_array, function (i, values) {
                                str += '<td style="text-align: center;">' + values + '</td>';

                            });
                            str += '<td ><input  value="" class="inpbox txt1 inpbox-mini" type="text"></td>';
                            str += `<td ><input  value="" class="inpbox txt2 inpbox-mini" type="text"></td>`;
                            str += '<td ><input  value="" class="inpbox txt3 inpbox-mini" type="text"></td>';
                            str += '<td ><input  value="0" disabled="disabled" class="inpbox txt4 inpbox-mini" type="text"></td>';
                            str += '<td ><input  value="" class="inpbox txt5 inpbox-mini" type="text"></td>';
                            tr.append(str);
                            $('#createTable').trigger("create");
                        });
                    }

                    //结束创建Table表
                    arrayColumn.pop(); //删除数组中最后一项
                    //合并单元格
                    //$(table).mergeCell({
                        // 目前只有cols这么一个配置项, 用数组表示列的索引,从0开始
                        //cols: arrayColumn
                    //});
                } else {
                    //未全选中,清除表格
                    document.getElementById('createTable').innerHTML = "";
                }
            },
            hebingFunction: function () {

                $.fn.mergeCell = function (options) {
                    return this.each(function () {
                        var cols = options.cols;
                        for (var i = cols.length - 1; cols[i] != undefined; i--) {
                            mergeCell($(this), cols[i]);
                        }
                        dispose($(this));
                    })
                };

                function mergeCell($table, colIndex) {

                    $table.data('col-content', ''); // 存放单元格内容
                    $table.data('col-rowspan', 1); // 存放计算的rowspan值 默认为1
                    $table.data('col-td', $()); // 存放发现的第一个与前一行比较结果不同td(jQuery封装过的), 默认一个"空"的jquery对象
                    $table.data('trNum', $('tbody tr', $table).length); // 要处理表格的总行数, 用于最后一行做特殊处理时进行判断之用
                    // 进行"扫面"处理 关键是定位col-td, 和其对应的rowspan
                    $('tbody tr', $table).each(function (index) {
                        // td:eq中的colIndex即列索引
                        var $td = $('td:eq(' + colIndex + ')', this);
                        // 获取单元格的当前内容
                        var currentContent = $td.html();
                        // 第一次时走次分支
                        if ($table.data('col-content') == '') {
                            $table.data('col-content', currentContent);
                            $table.data('col-td', $td);
                        } else {
                            // 上一行与当前行内容相同
                            if ($table.data('col-content') == currentContent) {
                                // 上一行与当前行内容相同则col-rowspan累加, 保存新值
                                var rowspan = $table.data('col-rowspan') + 1;
                                $table.data('col-rowspan', rowspan);
                                // 值得注意的是 如果用了$td.remove()就会对其他列的处理造成影响
                                $td.hide();
                                // 最后一行的情况比较特殊一点
                                // 比如最后2行 td中的内容是一样的, 那么到最后一行就应该把此时的col-td里保存的td设置rowspan
                                // 最后一行不会向下判断是否有不同的内容
                                if (++index == $table.data('trNum'))
                                    $table.data('col-td').attr('rowspan', $table.data('col-rowspan'));
                            }
                            // 上一行与当前行内容不同
                            else {
                                // col-rowspan默认为1, 如果统计出的col-rowspan没有变化, 不处理
                                if ($table.data('col-rowspan') != 1) {
                                    $table.data('col-td').attr('rowspan', $table.data('col-rowspan'));
                                }
                                // 保存第一次出现不同内容的td, 和其内容, 重置col-rowspan
                                $table.data('col-td', $td);
                                $table.data('col-content', $td.html());
                                $table.data('col-rowspan', 1);
                            }
                        }
                    })
                }

                // 同样是个private函数 清理内存之用
                function dispose($table) {
                    $table.removeData();
                }
            },
            doExchange: function (doubleArrays) {

                // 二维数组，最先两个数组组合成一个数组，与后边的数组组成新的数组，依次类推，知道二维数组变成以为数组，所有数据两两组合
                var len = doubleArrays.length;
                if (len >= 2) {
                    var arr1 = doubleArrays[0];
                    var arr2 = doubleArrays[1];
                    var len1 = arr1.length;
                    var len2 = arr2.length;
                    var newLen = len1 * len2;
                    var temp = new Array(newLen);
                    var index = 0;
                    for (var i = 0; i < len1; i++) {
                        for (var j = 0; j < len2; j++) {
                            temp[index++] = arr1[i] + ',' + arr2[j];
                        }
                    }
                    var newArray = new Array(len - 1);
                    newArray[0] = temp;
                    if (len > 2) {
                        var _count = 1;
                        for (var i = 2; i < len; i++) {
                            newArray[_count++] = doubleArrays[i];
                        }
                    }
                    return this.doExchange(newArray);
                } else {
                    this.form.array1 = doubleArrays[0] // 获得的组合数据，需要自己把后面的价格信息拼接上去
                    this.count = this.form.array1.length
                    return doubleArrays[0];
                }
            },
            diplay() { // 获取表格数据，并转换为JSON格式
                this.goodArr = []
                for (let i = 0; i < $('.txt1').length; i++) {
                    this.form.array1[i] += ',' + $('.txt1').eq(i).val()
                    this.form.array1[i] += ',' + $('.txt2').eq(i).val()
                    this.form.array1[i] += ',' + $('.txt3').eq(i).val()
                    this.form.array1[i] += ',' + $('.txt4').eq(i).val()
                    this.form.array1[i] += ',' + $('.txt5').eq(i).val()
                }

                for (let k = 0; k < this.form.array1.length; k++) {
                    let obj2 = {};
                    let str = this.form.array1[k].split(",");
                    let strSize = str.length;
                    let nameSize = this.obj.length;
                    let o = this.obj
                    if (strSize > 5) {
                        for (let j = 0; j < nameSize; j++) {
                            let first = Object.keys(o[j]).join()
                            obj2[first] = str[j]
                        }
                        obj2.price = str[strSize - 5];
                        obj2.marketPrice = str[strSize - 4];
                        obj2.stock = str[strSize - 3];
                        obj2.soldVolume = str[strSize - 2];
                        obj2.warnStock = str[strSize - 1];
                    }
                    this.goodArr.push(obj2);
                }
                console.log(this.goodArr)
            },

        }
    }
</script>

<style scoped>
  .myli {
    color: red;
    display: inline;
    margin-left: 5px
  }

  .controls {
    color: gold;
    display: inline-block;
  }

  .Father_Title {
    color: orange;
  }

  #createTable td {
    text-align: center
  }
</style>
