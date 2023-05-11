<template>
    <div v-if="ifshow" class="popmain" :id="`${currentUUID}popmain`">
        <div class="sj" :id="`${currentUUID}sj`"></div>
        <div v-if="showtitle" class="title">
            <slot name="title"> </slot>
        </div>
        <div class="content">
            <slot name="content"> </slot>
        </div>
    </div>
    <div class="element" :id="`${currentUUID}element`">
        <slot></slot>
    </div>

</template>
<script setup> 
import { getCurrentInstance, onMounted, watch, ref, computed, nextTick } from "vue"
const props = defineProps({
    /**是否显示 */
    modelValue: {
        type: Boolean,
        default: false
    },
    /**title */
    showtitle: {
        type: Boolean,
        default: false
    },
    /**位置 */
    placement: {
        type: String,
        default: 'top'
    },
})
const emit = defineEmits(['update:modelValue']);
const ifshow = computed({
    get: () => {
        nextTick(() => {
            console.log(props.modelValue);
            if (props.modelValue)
                initPlace()
            else {
                let dom = document.querySelector('.popmain')
                if (dom)
                    dom.style = `opacity:0`
            }

        })
        return props.modelValue
    },
    set: (val) => {
        emit('update:modelValue', val)
    }
});
let generateUUID = () => {
    let d = new Date().getTime();
    let uuid = 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function (c) {
        let r = (d + Math.random() * 16) % 16 | 0;
        d = Math.floor(d / 16);
        return (c == 'x' ? r : (r & 0x3 | 0x8)).toString(16);
    });
    return uuid;
}
const currentUUID = generateUUID()
//初始化弹框位置
let initPlace = () => {

    let _popdiv = document.getElementById(`${currentUUID}popmain`).getBoundingClientRect()
    let _el = document.getElementById(`${currentUUID}element`).getBoundingClientRect()

    let newposition = {
        x: 0,
        y: 0,
        sjx: 0,
        sjy: 0,
        sjshadow: ''
    }
    switch (props.placement) {
        case 'top': {
            let _tempx = _el.x + _el.width / 2
            let _tempy = _el.y - 10

            newposition.x = _tempx - _popdiv.width / 2
            newposition.y = _tempy - _popdiv.height

            newposition.sjx = _popdiv.width / 2 - 5
            newposition.sjy = _tempy - 15
            newposition.sjshadow = 'box-shadow: 5px 5px 15px 1px rgba(0, 0, 0, 0.2);'
            break
        }
        case 'left': {
            let _tempx = _el.x - 10
            let _tempy = _el.y + _el.height / 2

            newposition.x = _tempx - _popdiv.width
            newposition.y = _tempy - _popdiv.height / 2

            newposition.sjx = _popdiv.width - 5
            newposition.sjy = _popdiv.height / 2
            newposition.sjshadow = 'box-shadow: 15px -15px 15px 1px rgba(0, 0, 0, 0.2);'
            break
        }
        case 'right': {
            let _tempx = _el.x + _el.width + 10
            let _tempy = _el.y + _el.height / 2

            newposition.x = _tempx
            newposition.y = _tempy - _popdiv.height / 2

            newposition.sjx = - 5
            newposition.sjy = _popdiv.height / 2
            newposition.sjshadow = 'box-shadow: -15px 15px 15px 1px rgba(0, 0, 0, 0.2);'
            break
        }
        case 'bottom': {
            let _tempx = _el.x + _el.width / 2
            let _tempy = _el.y + _el.height + 10

            newposition.x = _tempx - _popdiv.width / 2
            newposition.y = _tempy

            newposition.sjx = _popdiv.width / 2
            newposition.sjy = -5
            newposition.sjshadow = 'box-shadow: -10px -10px 15px 1px rgba(0, 0, 0, 0.2);'
            break
        }
        default:
            break
    }

    document.getElementById(`${currentUUID}popmain`).style = `top:${newposition.y}px;left:${newposition.x}px;opacity:1`
    document.getElementById(`${currentUUID}sj`).style = `${newposition.sjshadow}top:${newposition.sjy}px;left:${newposition.sjx}px;`
}
onMounted(() => {
    window.onscroll = () => {
        ifshow.value = false
    }

})
</script>
<style lang="less" scoped>
.popmain {
    opacity: 0;
    border-radius: 5px;
    position: fixed;
    z-index: 1111;
    padding: 10px;
    box-shadow: 0 6px 16px 0 rgba(0, 0, 0, 0.08),
        0 3px 6px -4px rgba(0, 0, 0, 0.12), 0 9px 28px 8px rgba(0, 0, 0, 0.05);
    background-color: #ffff;
    height: auto;
    transition: opacity 1s ease;
    .title {
        height: 30px;
        line-height: 30px;
        font-weight: 600;
    }
    .content {
        font-size: 13px;
        line-height: normal;
    }
    .sj {
        position: absolute;
        width: 10px;
        height: 10px;
        background-color: #ffff;
        transform: rotate(45deg);
    }
}
</style>