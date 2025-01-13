<script setup lang="ts">
import { defineProps, onMounted, onUnmounted } from 'vue'

const props = defineProps({
    previewRef: {
        type: Object as () => HTMLElement,
        required: true
    }
})

// 保存原始的 HTML 内容
let originalBodyHTML: string

const downloadPDF = () => {
    const previewElement = props.previewRef

    if (!previewElement) {
        console.error('Preview element not found')
        return
    }

    // 保存原始的 body 内容
    originalBodyHTML = document.body.innerHTML

    // 将预览区域的内容替换为 body 的内容
    document.body.innerHTML = previewElement.innerHTML

    // 添加打印样式
    const style = document.createElement('style')
    style.innerHTML = `
        @media print {
            body {
                font-family: 'MapleMono', 'Inter', sans-serif;
                padding: 20px;
            }
            pre, code {
                font-family: 'MapleMono', monospace;
                white-space: pre-wrap;
            }
            table {
                width: 100%;
                border-collapse: collapse;
            }
            th, td {
                border: 1px solid #000;
                padding: 8px;
            }
        }
    `
    document.head.appendChild(style)

    // 触发打印
    window.print()

    // 恢复原始内容
    document.body.innerHTML = originalBodyHTML
}

// 监听打印完成事件
onMounted(() => {
    window.addEventListener('afterprint', () => {
        // 打印完成后恢复原始内容
        document.body.innerHTML = originalBodyHTML
    })
})

onUnmounted(() => {
    window.removeEventListener('afterprint', () => {
        document.body.innerHTML = originalBodyHTML
    })
})
</script>

<template>
    <button @click="downloadPDF">Download PDF</button>
</template>