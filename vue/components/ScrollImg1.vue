<template>
    <div class="main">
        <div class="col" v-for="colindex in 7" :id="`col${colindex}`" :key="colindex">
            <div :class="{
                imgdiv:true,
                hiddenimg:item.opacity==0
            }" v-for="item,index in _dataList[`col${colindex}`]" :key="`col${colindex}${index}`">{{`col${colindex}-${item.imgname}`}}</div>
        </div>
        <div class="cover"> </div>
        <div class="footer">
            sdfsdfsdfsdfsdfsdfsdfsdfsdfsdfsdf
        </div>
    </div>
</template>
<script setup> 
import { getCurrentInstance, onMounted, watch, ref, computed, nextTick, registerRuntimeCompiler } from "vue"
const { proxy } = getCurrentInstance();
const props = defineProps({
    /**图片数组 */
    modelValue: {
        type: Object,
        default: {}
    },
})
const emit = defineEmits(['update:modelValue']);
const _dataList = computed({
    get: () => {
        let _tempobj = {}
        for (let key in props.modelValue) {
            _tempobj[key] = props.modelValue[key].map(row => ({
                imgname: row.imgname,
                url: row.url,
                opacity: 1
            }))
        }
        return _tempobj
    },
    // set: (val) => {
    //     emit('update:modelValue', val)
    // }
});


let getmargintop = (colindex) => {
    if (colindex == 'col4')
        return '500px'
    else if (colindex == 'col3' || colindex == 'col5')
        return '440px'
    else if (colindex == 'col2' || colindex == 'col6')
        return '380px'
    else if (colindex == 'col1' || colindex == 'col7')
        return '290px'
    else
        return ''
}
//动画 隐藏每列第一个div 然后将第一个元素移到最后一个 页面刷新后将每列的位置都往下挪  给个过渡效果恢复到原来的位置 实现滚动效果
let scrolltimeout = (timeout, colindexarr) => {
    setTimeout((colindexarr) => {
        for (let item of colindexarr)
            for (let i = 0; i < _dataList.value[item].length; i++) {
                _dataList.value[item][i].opacity = i == 0 ? 0 : 1
            }
        proxy.$forceUpdate()
    }, timeout, colindexarr);

    setTimeout((colindexarr) => {
        for (let item of colindexarr) {
            let _temobj = _dataList.value[item].shift()
            nextTick(() => {
                let el = document.getElementById(item)
                if (el)
                    el.style.marginTop = getmargintop(item)
                _dataList.value[item].push(_temobj)
                _dataList.value[item][_dataList.value[item].length - 1].opacity = 1
                setTimeout((item) => {
                    let el = document.getElementById(item)
                    if (el)
                        el.style.marginTop = ''
                }, 100, item);
            })
        }
        proxy.$forceUpdate()
    }, (timeout + 1200), colindexarr);


}
//动画事件
let autoScroll = () => {
    scrolltimeout(1000, ['col4'])
    scrolltimeout(2000, ['col3', 'col5'])
    scrolltimeout(3000, ['col2', 'col6'])
    scrolltimeout(4000, ['col1', 'col7'])
}
onMounted(() => {
    requestAnimationFrame(autoScroll)
    setInterval(autoScroll, 10000)
})
</script>
<style lang="less" scoped>
.hiddenimg {
    opacity: 0 !important;
}
.main {
    width: 100vw;
    overflow: hidden;
    height: 650px;
    padding: 20px;
    display: flex;
    position: relative;
    .col {
        width: 224px;
        height: 100%;
        &:nth-child(4) {
            margin-top: 210px;
        }
        &:nth-child(3),
        &:nth-child(5) {
            margin-top: 150px;
        }
        &:nth-child(2),
        &:nth-child(6) {
            margin-top: 90px;
        }
        display: flex;
        flex-direction: column;
        align-items: center;
        transition: margin 0.5s ease;
        .imgdiv {
            cursor: pointer;
            opacity: 1;
            flex: none;
            width: 200px;
            height: 292px;
            background-color: #d8ccfd;
            border-radius: 5px;
            margin-bottom: 5px;
            transition: opacity 1.2s ease, transform 0.3s ease;

            &:hover {
                transform: translateY(-10px) scale(1.1);
            }
        }
    }
    .cover {
        height: 200px;
        width: 100%;
        position: absolute;
        bottom: 100px;
        background: linear-gradient(
            to bottom,
            rgba(255, 255, 255, 0),
            rgba(255, 255, 255, 0.5),
            rgba(255, 255, 255, 0.7)
        );
    }
    .footer {
        padding: 20px;
        height: 100px;
        line-height: 100px;
        width: 100%;
        position: absolute;
        bottom: 0px;
        background-color: #ffff;
    }
}
</style>