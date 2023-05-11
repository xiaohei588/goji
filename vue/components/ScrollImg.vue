<template>
    <div class="main" :style="`width:${props.width};height:${props.height};`">
        <div class="container">
            <img class="item" v-for="item, index in data" :key="index" :src="getAssetURL(item)" alt="">

            <div ref="ObserverItem">loading</div>
        </div>
    </div>
</template>
<script setup> 
import { getCurrentInstance, onMounted, watch, ref, computed, nextTick } from "vue"
const props = defineProps({
    /**图片数组 */
    modelValue: {
        type: Array,
        default: []
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
})
const emit = defineEmits(['update:modelValue']);
const data = computed({
    get: () => {
        return props.modelValue
    },
    set: (val) => {
        emit('update:modelValue', val)
    }
});
const { proxy } = getCurrentInstance();
let getAssetURL = (image) => {

    try {
        let re = require('../assets/' + image)
        return re.default
    }
    catch (ex) { console.log(ex); }
    // return new URL(`../assets/${image}`, import.meta.url).href

}
let sectionObserver = null

/** 观察对象回调函数 */
let Observercallback = (entries) => {

    if (!entries[0].isIntersecting) return;

    let _temparr = JSON.parse(JSON.stringify(data.value))
    for (let i of data.value)
        _temparr.push(i)
    data.value = _temparr

    nextTick(() => {
        setTimeout(() => {
            let el = document.querySelector('.container')
            if (el.style.height == '')
                el.style.height = `calc(100vh)`
            else
                el.style.height = `calc(100vh + ${el.style.height})`
        }, 200)
    }
    )

}
onMounted(() => {
    nextTick(() => {
        sectionObserver = new IntersectionObserver(Observercallback);
        sectionObserver.observe(proxy.$refs['ObserverItem']);
    })
})
</script>
<style lang="less" scoped>
.main {
    overflow-y: auto;
    overflow-x: hidden;
    .container {
        width: 100%;
        height: 100%;
        display: flex;
        flex-flow: column wrap;

        img {
            width: calc(calc(100% - 70px) / 3);
            margin: 10px;
        }
    }
}
</style>