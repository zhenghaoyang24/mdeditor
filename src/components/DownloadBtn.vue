<script setup lang="ts">
import { defineProps } from 'vue'

const props = defineProps({
    previewRef: {
        type: Object as () => HTMLDivElement | null,
        required: true
    }
})

const downloadPDF = () => {
    const previewElement = props.previewRef

    if (!previewElement) {
        console.error('Preview element not found')
        return
    }

    // 创建一个 iframe 元素
    const iframe = document.createElement('iframe')
    iframe.style.position = 'absolute'
    iframe.style.width = '0'
    iframe.style.height = '0'
    iframe.style.border = 'none'
    iframe.style.flex = 'none'
    document.body.appendChild(iframe)

    console.log('iframe created') // 调试日志

    // 等待 iframe 加载完成
    iframe.onload = () => {
        console.log('iframe loaded') // 调试日志
        const iframeDocument = iframe.contentDocument || iframe.contentWindow?.document

        if (!iframeDocument) {
            console.error('Failed to access iframe document')
            return
        }

        // 将预览区域的内容复制到 iframe 中
        iframeDocument.body.innerHTML = previewElement.innerHTML
        console.log('iframe content:', iframeDocument.body.innerHTML) // 调试日志

        // 添加打印样式
        const style = iframeDocument.createElement('style')
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
        iframeDocument.head.appendChild(style)
        console.log('iframe styles:', iframeDocument.head.innerHTML) // 调试日志

        // 触发打印
        setTimeout(() => {
            console.log('triggering print') // 调试日志
            iframe.contentWindow?.print()
        }, 500)

        // 打印完成后移除 iframe
        iframe.contentWindow?.addEventListener('afterprint', () => {
            console.log('afterprint event triggered') // 调试日志
            document.body.removeChild(iframe)
        })
    }

    // 手动设置 iframe 内容以确保加载
    iframe.srcdoc = '<html><body></body></html>'
}
</script>

<template>
    <button @click="downloadPDF">Download PDF</button>
</template>