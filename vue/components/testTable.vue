<template>
    <div :style="`width:${props.width};height:${props.height};`">
        <div class="tablecontain" @click="divclick($event)">
            <div class="coloums">
                <div class="col" v-for="item ,index in _col" :key="index" :style="getAutow(item)">
                    <div>
                        <div class="colname">
                            <div class="coln">{{ item.colname}}</div>
                            <div class="filter" v-if="item.filter" @click="showColFilter($event,item)">筛</div>
                            <div class="sort" v-if="item.sort">
                                <div :class="{
                                up:true,
                                asc:item.sorttype==='asc'
                            }" @click="sortData(item,'asc')"></div>
                                <div :class="{
                                down:true,
                                desc:item.sorttype==='desc'
                            }" @click="sortData(item,'desc')"></div>
                            </div>
                        </div>
                        <div class="td" v-for="td,rowindex in getcolumValue(item.dataIndex)" :key="rowindex">{{td.value}}</div>
                    </div>
                </div>
            </div>
            <div v-show="ifshowFilter" class="filterbox">
                <div>
                    <input type="text" placeholder="请输入筛选字符" class="inputText" />
                </div>
                <div>
                    <input type="button" @click.stop="filterEvent($event)" value="确定" />
                </div>

            </div>
        </div>
        <div v-if="props.showpagesTool" :style="`width:${props.width};`" class="pagetool">
            <div>{{`每页${pagePar.pageSize}条`}}</div>
            <div class="pageNOs">
                <div @click="pagePar.jumpTo(item)" :class="{
                    pageNO:true,
                    checked:item==pagePar.pageNum
                }" v-for="item in pageCount">{{item}}</div>
            </div>
            <div>{{`总共${pagePar.total}条 `}}</div>
        </div>
    </div>
</template>
<script setup> 
import { onMounted, watch, ref, computed, nextTick } from "vue"
const props = defineProps({
    /**字段 */
    col: {
        type: Array,
        default: () => []
    },
    /**数据 */
    data: {
        type: Array,
        default: () => []
    },
    /**宽度 */
    width: {
        type: [String],
        default: '100%'
    },
    /**高度 */
    height: {
        type: [String],
        default: '100%'
    },
    /**是否显示分页 */
    showpagesTool: {
        type: Boolean,
        default: false
    },
})
const ifshowFilter = ref(false)//是否显示筛选框
let _currentFilterCol = null//当前筛选
let _currentSort = null//当前排序
let pagePar = ref({
    total: 0,//总数
    pageNum: 1,//页索引
    pageSize: 10,//每页数量
    jumpTo: (NO) => {
        _data.value = props.data.filter((row, index) => index < (pagePar.value.pageSize * NO) && index >= (pagePar.value.pageSize * (NO - 1)))
        nextTick(() => {
            pagePar.value.pageNum = NO
        })

    }
})//分页属性
let pageCount = computed(() => {
    let re = (Math.floor(pagePar.value.total / pagePar.value.pageSize) + 1)
    return re == 0 ? 1 : re
})



//对象排序
let sortBy = (name, sorttype) => {
    return function (o, p) {
        var a, b;
        a = o[name];
        b = p[name];
        if (a === b) {
            return 0;
        }
        if (sorttype == 'asc') {
            return a - b
        }
        else
            return b - a
    }
}
//排序事件
let sortData = (colitem, sorttype) => {
    let tempcol = null
    for (let _item of _col.value) {
        if (_item.dataIndex == colitem.dataIndex) {
            _item.sorttype = sorttype
            tempcol = _item
        }
        else
            _item.sorttype = ''
    }
    if (tempcol) {
        _dataorg.value.sort(sortBy(colitem.dataIndex, sorttype))
    }
}
//筛选数据
let filterEvent = (e) => {
    e && e.stopPropagation ? e.stopPropagation() : '';
    let el = document.querySelector('.inputText')
    if (_currentFilterCol) {
        let _temarr = _dataorg.value.filter(row => row[_currentFilterCol.dataIndex].toString().indexOf(el.value) >= 0)
        _data.value = []
        _data.value.push(..._temarr)
    }
    ifshowFilter.value = false
}
//全局点击隐藏筛选框
let divclick = (e) => {
    //e.target.classList && e.target.classList.contains('filter') ? '' : ifshowFilter.value = false

}
//显示筛选框
let showColFilter = ($event, colitem) => {
    _currentFilterCol = colitem
    let el = document.querySelector('.filterbox')
    el.style = `top:${$event.clientY + 10}px;left:${$event.clientX + 10}px;`
    ifshowFilter.value = true
}
//获取列的宽度
let getAutow = (item) => {
    if (item.width)
        return `width:${item.width};`
    else
        return `flex；1;`
}
//获取当前列的数据
let getcolumValue = (dataIndex) => {
    let _temparr = _data.value.map(row => ({ value: row[dataIndex] }))
    return _temparr
}
//监听数据源
let _data = ref([])
const _dataorg = computed({
    get: () => {
        pagePar.value.total = props.data.length
        pagePar.value.pageNum = 1
        pagePar.value.pageSize = 10
        return props.data
    }
});
const _col = computed({
    get: () => {
        return props.col.map(row => {
            let _obj = {}
            Object.assign(_obj, row)
            _obj['filter'] = _obj['filter'] == undefined ? false : _obj['filter']
            _obj['sort'] = _obj['sort'] == undefined ? false : _obj['sort']
            _obj['sorttype'] = ''
            return _obj
        })
    }
});
watch(_dataorg, () => {
    if (props.showpagesTool) {
        pagePar.value.jumpTo(1)
    }
    else
        _data.value = props.data
}, { deep: true, immediate: true })

onMounted(() => {

})
</script>
<style lang="less" scoped>
.filterbox {
    padding: 5px;
    border-radius: 5px;
    background-color: #ffff;
    z-index: 11;
    width: 200px;
    white-space: nowrap;
    text-overflow: ellipsis;
    overflow-x: hidden;
    position: absolute;
    box-shadow: 0 6px 16px 0 rgba(0, 0, 0, 0.08),
        0 3px 6px -4px rgba(0, 0, 0, 0.12), 0 9px 28px 8px rgba(0, 0, 0, 0.05);
    .inputText {
        outline: none;
        border-style: none;
        border-bottom: 1px solid;
        margin-bottom: 5px;
    }
}
.pagetool {
    margin-top: 20px;
    height: 30px;
    line-height: 30px;
    display: flex;
    justify-content: flex-end;
    align-items: center;
    div {
        &:nth-child(1) {
            margin-right: 20px;
        }
        &:nth-child(3) {
            margin-left: 20px;
        }
    }
    .pageNOs {
        display: flex;
        justify-content: space-evenly;
        align-content: center;
        white-space: nowrap;
        text-overflow: ellipsis;
        overflow: hidden;
        .pageNO {
            margin: 0 5px !important;
            cursor: pointer;
            width: 30px;
            height: 30px;
            margin: auto;
            text-align: center;
            &:hover {
                background-color: rgba(240, 237, 237, 0.6);
            }
        }
        .checked {
            background-color: rgb(235, 231, 231) !important;
            color: blue !important;
        }
    }
}
.tablecontain {
    width: 100%;
    height: 100%;
    border-radius: 4px;
    box-shadow: 0 0 11px 1px rgba(0, 0, 0, 0.2);
    display: block;
    padding: 1px;
    overflow: auto;
    .coloums {
        height: 100%;
        width: 100%;
        display: flex;
        .col {
            height: 100%;
            flex: none;
            //border-left: 1px solid rgba(104, 103, 103, 0.2);
            .colname {
                text-align: center;

                white-space: nowrap;
                text-overflow: ellipsis;
                overflow: hidden;
                height: 40px;
                line-height: 40px;
                background-color: #f2f3f5;
                display: flex;
                .coln {
                    flex: 1;
                }
                .filter {
                    width: 30px;
                    text-align: center;
                    font-size: 12px;
                    cursor: pointer;
                }
                .sort {
                    width: 13px;
                    height: 100%;
                    position: relative;
                    .up,
                    .down {
                        cursor: pointer;
                        width: 5px;
                        height: 5px;
                        border-radius: 5px;
                        position: absolute;
                        background: transparent;
                        border-style: solid;
                        border-color: rgb(122, 122, 122);
                        border-radius: 5px;
                        transform: rotate(45deg) !important;
                    }
                    .up {
                        border-width: 3px 0 0 3px;
                        top: 6px;
                    }
                    .down {
                        border-width: 0 3px 3px 0;
                        bottom: 6px;
                    }
                    .asc,
                    .desc {
                        border-color: #000;
                    }
                }
            }
            .td {
                text-align: center;
                height: 40px;
                line-height: 40px;
                &:nth-child(odd) {
                    background-color: rgba(228, 227, 238, 0.2);
                }
            }
        }
    }
}
</style>  