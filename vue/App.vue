
<template>
    <div class="App">

        <h2>第一题 实现一个 Table 组件,传入 data 和 col 后可以渲染出一个表格。表格中的数据可以排序,筛选</h2>
        <h2>第五题 [hard] 表格带分页</h2>
        <testTable :data="mockdata" :col="mockcol" showpagesTool width="800px" height="200px" style="margin:20px 20px;"></testTable>
        <br>
        <h2>第二题 [easy] 实现弹框组件</h2>
        <Modal v-model="ifshow" showfooter showtitle>
            <template #title>
                这里是title <input type="button" @click="ifshow = !ifshow" value="关闭" />
            </template>
            这是正文这是正文这是正文这是正文这是正文这是正文这是正文这是正文这是正文这是正文这是正文这是正文这是正文这是正文这是正文这是正文这是正文
            <template #footer>
                这里是页脚 <input type="button" @click="ifshow = !ifshow" value="关闭" />
            </template>
        </Modal>
        <input type="button" @click="ifshow = !ifshow" value="弹窗" />

        <h2>第三题 [hard]滚动加载</h2>
        <h3>花瓣网自动滚动加载</h3>
        <ScrollImg v-model="dataList"></ScrollImg>
        <h3>瀑布流滚动加载</h3>
        <LoadImg v-model="imgelist" width="700px" height="600px"></LoadImg>
        <h2>第四题 [hard] 实现气泡框</h2>
        <div class="testpop">
            <Popover v-model="showleftPop" placement="left" showtitle>
                <template #title>
                    这里是title
                </template>
                <template #content>
                    <div>这里是content1213212312</div>
                    <div>这里是content</div>
                    <div>这里是content2</div>
                </template>
                <input type="button" @click="showleftPop = !showleftPop" value="LEFT" />
            </Popover>
        </div>
    </div>
</template>

<script setup>
import { onMounted, watch, ref, computed } from "vue"
import testTable from './components/testTable.vue';
import Modal from './components/Modal.vue';
import LoadImg from './components/ScrollImg.vue';
import ScrollImg from './components/ScrollImg1.vue';
import Popover from './components/Popover.vue';
let mockcol = ref([{
    colname: '序号',
    dataIndex: '序号',
    width: '200px',
    filter: true,
    sort: true,
},
{
    colname: '字段1',
    dataIndex: 'c1',
    width: '300px',

}
    ,
{
    colname: '字段2',
    dataIndex: 'c2',
    width: '150px',
},
{
    colname: '字段3',
    dataIndex: 'c3',
    width: '200px',
},
{
    colname: '字段4',
    dataIndex: 'c4',

},
{
    colname: '字段5',
    dataIndex: 'c5',
    width: '100px',
}
])
let mockdata = ref([{
    '序号': 1,
    'c1': 'sdf1',
    'c2': 'sdf2',
    'c3': 'sdf3',
    'c4': 'sdf4',
    'c5': 'sdf5',
},
{
    '序号': 2,
    'c1': 'sdf6',
    'c2': 'sdf7',
    'c3': 'sdf8',
    'c4': 'sdf9',
    'c5': 'sdf10',
},
{
    '序号': 3,
    'c1': 'sdf11',
    'c2': 'sdf12',
    'c3': 'sdf13',
    'c4': 'sdf14',
    'c5': 'sdf15',
},
{
    '序号': 4,
    'c1': 'sdf16',
    'c2': 'sdf17',
    'c3': 'sdf18',
    'c4': 'sdf19',
    'c5': 'sdf20',
},
{
    '序号': 5,
    'c1': 'sdf21',
    'c2': 'sdf22',
    'c3': 'sdf23',
    'c4': 'sdf24',
    'c5': 'sdf25',
},
{
    '序号': 6,
    'c1': 'sdf21',
    'c2': 'sdf22',
    'c3': 'sdf23',
    'c4': 'sdf24',
    'c5': 'sdf25',
},
{
    '序号': 7,
    'c1': 'sdf21',
    'c2': 'sdf22',
    'c3': 'sdf23',
    'c4': 'sdf24',
    'c5': 'sdf25',
},
{
    '序号': 8,
    'c1': 'sdf21',
    'c2': 'sdf22',
    'c3': 'sdf23',
    'c4': 'sdf24',
    'c5': 'sdf25',
},
{
    '序号': 9,
    'c1': 'sdf21',
    'c2': 'sdf22',
    'c3': 'sdf23',
    'c4': 'sdf24',
    'c5': 'sdf25',
},
{
    '序号': 10,
    'c1': 'sdf21',
    'c2': 'sdf22',
    'c3': 'sdf23',
    'c4': 'sdf24',
    'c5': 'sdf25',
},
{
    '序号': 11,
    'c1': 'sdf21',
    'c2': 'sdf22',
    'c3': 'sdf23',
    'c4': 'sdf24',
    'c5': 'sdf25',
},
{
    '序号': 12,
    'c1': 'sdf21',
    'c2': 'sdf22',
    'c3': 'sdf23',
    'c4': 'sdf24',
    'c5': 'sdf25',
}
])

let ifshow = ref(false)
let imgelist = ref([
    '2.jpg', '3.png', 'login-background.jpg', 'profile.jpg', 'profile1.jpg', 'about.png'
])
let dataList = ref({
    col1: [{
        imgname: '图1',
        url: '',
    }
        ,
    {
        imgname: '图2',
        url: ''
    },
    {
        imgname: '图3',
        url: ''
    }
    ]
    ,
    col2: [{
        imgname: '图1',
        url: ''
    }
        ,
    {
        imgname: '图2',
        url: ''
    },
    {
        imgname: '图3',
        url: ''
    }
    ],
    col3: [{
        imgname: '图1',
        url: ''
    }
        ,
    {
        imgname: '图2',
        url: ''
    },
    {
        imgname: '图3',
        url: ''
    }
    ],
    col4: [{
        imgname: '图1',
        url: ''
    }
        ,
    {
        imgname: '图2',
        url: ''
    },
    {
        imgname: '图3',
        url: ''
    }
    ]
    , col5: [{
        imgname: '图1',
        url: ''
    }
        ,
    {
        imgname: '图2',
        url: ''
    },
    {
        imgname: '图3',
        url: ''
    }
    ]
    , col6: [{
        imgname: '图1',
        url: ''
    }
        ,
    {
        imgname: '图2',
        url: ''
    },
    {
        imgname: '图3',
        url: ''
    }
    ], col7: [{
        imgname: '图1',
        url: ''
    }
        ,
    {
        imgname: '图2',
        url: ''
    },
    {
        imgname: '图3',
        url: ''
    }
    ]

})

let showleftPop = ref(false)
let showrightPop = ref(false)
</script>
<style lang="less" scoped>
.App {
    width: 100%;
    height: 100%;

    padding: 20px;
    .testpop {
        height: 200px;
        line-height: 200px;
        display: flex;
        justify-content: center;
    }
}
</style>
