<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import { marked } from 'marked'
import 'github-markdown-css' // 引入 GitHub Markdown CSS

const markdownText = ref('# Hello Markdown')

const htmlContent = computed(() => {
    return marked(markdownText.value)
})

// 获取编辑框和预览框的 DOM 元素
const editorRef = ref<HTMLTextAreaElement | null>(null)
const previewRef = ref<HTMLDivElement | null>(null)

// 是否正在同步滚动（避免循环触发）
let isSyncingScroll = false
// 同步滚动逻辑
const syncScroll = (source: 'editor' | 'preview') => {
    if (isSyncingScroll) return // 如果正在同步滚动，直接返回

    isSyncingScroll = true // 开始同步滚动

    const editor = editorRef.value
    const preview = previewRef.value

    if (!editor || !preview) return

    if (source === 'editor') {
        // 如果编辑框滚动，同步预览框
        const editorScrollRatio = editor.scrollTop / (editor.scrollHeight - editor.clientHeight)
        preview.scrollTo({
            top: (preview.scrollHeight - preview.clientHeight) * editorScrollRatio,
            behavior: 'auto', // 禁用平滑滚动
        })
    } else if (source === 'preview') {
        // 如果预览框滚动，同步编辑框
        const previewScrollRatio = preview.scrollTop / (preview.scrollHeight - preview.clientHeight)
        editor.scrollTo({
            top: (editor.scrollHeight - editor.clientHeight) * previewScrollRatio,
            behavior: 'auto', // 禁用平滑滚动
        })
    }

    // 同步滚动结束
    setTimeout(() => {
        isSyncingScroll = false
    }, 0)
}

// 监听编辑框和预览框的滚动事件
onMounted(() => {
    const editor = editorRef.value
    const preview = previewRef.value
    if (editor) {
        editor.addEventListener('scroll', () => syncScroll('editor'))
    }

    if (preview) {
        preview.addEventListener('scroll', () => syncScroll('preview'))
    }
})
</script>

<template>
    <div class="editor-container">
        <textarea ref="editorRef" spellcheck="false" class="editor" v-model="markdownText"
            placeholder="Write your markdown here..."></textarea>
        <div ref="previewRef" class="preview" v-html="htmlContent"></div>
    </div>
</template>

<style scoped>
.editor-container {
    display: flex;
    justify-content: space-around;
    height: 100vh;
    box-sizing: border-box;
}

.editor {

    flex: 1;
    padding: 1rem;
    box-sizing: border-box;
    border: 1px solid #ccc;
    font-family: MapleMono,Inter;
    resize: none;

}

.preview {
    flex: 1;
    box-sizing: border-box;
    padding: 1rem;
    border: 1px solid #ccc;
    overflow-y: auto;
    font-family: MapleMono,Inter;
}
</style>