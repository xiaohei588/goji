<template>
    <div v-if="ifshow" class="modal" :style="`width:${props.width};height:${props.height};`">

        <div v-if="showtitle" class="title">
            <slot name="title"> </slot>
        </div>
        <div class="content">
            <slot></slot>
        </div>
        <div v-if="props.showfooter" class="footer">
            <slot name="footer"> </slot>
        </div>
        <div class="closeX" @click="ifshow=!ifshow">
            <div class="closeX1"></div>
            <div class="closeX2"></div>
        </div>

    </div>
</template>
<script setup> 
import { onMounted, watch, ref, computed, nextTick } from "vue"
const props = defineProps({
    /**是否显示 */
    modelValue: {
        type: Boolean,
        default: false
    },

    /**页眉 */
    showtitle: {
        type: Boolean,
        default: false
    },
    /**页脚 */
    showfooter: {
        type: Boolean,
        default: false
    },
    /**宽度 */
    width: {
        type: [String],
        default: '50%'
    },
    /**高度 */
    height: {
        type: [String],
        default: '50%'
    },

})

const emit = defineEmits(['update:modelValue']);
const ifshow = computed({
    get: () => {

        return props.modelValue
    },
    set: (val) => {
        emit('update:modelValue', val)
    }
});

</script>
<style lang="less"  scoped>
.modal {
    box-shadow: 0 6px 16px 0 rgba(0, 0, 0, 0.08),
        0 3px 6px -4px rgba(0, 0, 0, 0.12), 0 9px 28px 8px rgba(0, 0, 0, 0.05);
    background-color: #ffff;
    position: absolute;

    border-radius: 3px;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    display: flex;
    flex-direction: column;
    div {
        display: block;
        padding: 0px 20px;
    }
    .content,
    .footer {
        padding: 20px;
    }
    .title {
        height: 50px;
        line-height: 50px;
        border-bottom: 1px solid rgba(43, 43, 42, 0.6);
    }

    .footer {
        height: 80px;
    }
    .content {
        flex: 1;
        border-bottom: 1px solid rgba(43, 43, 42, 0.6);
    }
    .closeX {
        position: absolute;
        width: 20px;
        height: 20px;
        top: 25px;
        right: 25px;
        padding: 0px;
        background-color: transparent;
        cursor: pointer;
        transform: translateY(-50%);
        display: flex;
        justify-content: center;
        &:hover {
            background-color: rgba(211, 206, 205, 0.6);
        }
        .closeX1,
        .closeX2 {
            position: absolute;
            width: 2px;
            height: 20px;
            padding: 0px;
            background-color: black;
            border-radius: 5px;
        }
        .closeX1 {
            transform: rotate(45deg);
        }
        .closeX2 {
            transform: rotate(-45deg);
        }
    }
}
</style>