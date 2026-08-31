<template>
    <div v-html="getHtml"></div>
</template>

<script>
// Plugin-supplied HTML is untrusted: only render an allow-list of formatting
// elements and attributes, everything else is dropped.
const ALLOWED_TAGS = new Set([
    'a', 'abbr', 'b', 'blockquote', 'br', 'caption', 'code', 'dd', 'div', 'dl', 'dt',
    'em', 'h1', 'h2', 'h3', 'h4', 'h5', 'h6', 'hr', 'i', 'li', 'ol', 'p', 'pre',
    'small', 'span', 'strong', 'sub', 'sup', 'table', 'tbody', 'td', 'tfoot', 'th',
    'thead', 'tr', 'u', 'ul',
])
const ALLOWED_ATTRIBUTES = new Set(['class', 'title', 'colspan', 'rowspan'])
const ALLOWED_URL_PROTOCOLS = ['http:', 'https:', 'mailto:']

function isSafeUrl(url) {
    try {
        return ALLOWED_URL_PROTOCOLS.includes(new URL(url, window.location.href).protocol)
    } catch (error) {
        return false
    }
}

function sanitizeChildren(element) {
    Array.from(element.children).forEach((child) => {
        const tagName = child.tagName.toLowerCase()
        if (!ALLOWED_TAGS.has(tagName)) {
            child.remove()
            return
        }
        Array.from(child.attributes).forEach((attribute) => {
            const name = attribute.name.toLowerCase()
            if (tagName === 'a' && name === 'href') {
                if (!isSafeUrl(attribute.value)) {
                    child.removeAttribute(attribute.name)
                }
            } else if (!ALLOWED_ATTRIBUTES.has(name)) {
                child.removeAttribute(attribute.name)
            }
        })
        sanitizeChildren(child)
    })
}

function sanitizeHtml(html) {
    const doc = new DOMParser().parseFromString(String(html), 'text/html')
    sanitizeChildren(doc.body)
    return doc.body.innerHTML
}

export default {
    name: 'PluginElementHtmlElement',
    props: {
        data: {
            type: Object,
            required: true
        },
        status: {
            required: false
        },
        errors: {
            required: false
        },
    },
    computed: {
        getHtml() {
            return sanitizeHtml(this.data.html ?? 'No HTML returned')
        },
    },
}
</script>

<style scoped>
</style>
