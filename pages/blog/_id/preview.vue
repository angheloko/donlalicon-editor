<template>
  <div class="max-w-2xl mx-auto">
    <article class="blog">
      <div class="mb-5">
        <div
          v-if="blog.tags.length"
          class="text-xs uppercase font-semibold text-gray-600 mb-1 flex">
          <div v-for="tag of blog.tags" :key="tag" class="mx-2 first:ml-0">
            {{ tag }}
          </div>
        </div>
        <h1>{{ blog.title }}</h1>
        <h2 v-if="blog.lead" class="font-sans text-gray-600 font-light">
          {{ blog.lead }}
        </h2>
        <div class="text-gray-600 text-xs font-light">
          {{ blog.created | toDate }}
        </div>
      </div>
      <figure v-if="blog.imageUrl" class="mb-5">
        <img :src="blog.imageUrl" :alt="blog.imageAlt" class="w-full">
        <figcaption v-if="blog.imageCaption" v-html="blog.imageCaption" class="text-center text-gray-600 text-sm my-2" />
      </figure>
      <div v-html="blog.body" class="content" />
    </article>
  </div>
</template>

<script>
import hljs from 'highlight.js/lib/highlight'
import javascript from 'highlight.js/lib/languages/javascript'
import css from 'highlight.js/lib/languages/css'

export default {
  name: 'BlogPreviewPage',
  middleware: 'authenticated-access',
  data () {
    return {
      blog: {
        tags: []
      },
      id: ''
    }
  },
  validate ({ params }) {
    return params.id !== undefined
  },
  asyncData ({ params }) {
    return {
      id: params.id
    }
  },
  async mounted () {
    hljs.registerLanguage('javascript', javascript)
    hljs.registerLanguage('css', css)

    const db = this.$firebase.firestore()

    try {
      const documentSnapshot = await db.collection('blogs').doc(this.id).get()

      if (!documentSnapshot.exists) {
        this.$nuxt.error({ statusCode: 404, message: 'Blog not found' })
        return
      }

      this.blog = {
        id: documentSnapshot.id,
        ...documentSnapshot.data()
      }

      this.$nextTick(() => {
        this.$el.querySelectorAll('pre code').forEach((block) => {
          hljs.highlightBlock(block)
        })
      })
    } catch (e) {
      this.$nuxt.error({ statusCode: 404, message: 'Blog not found' })
    }
  }
}
</script>

<style scoped>
</style>
