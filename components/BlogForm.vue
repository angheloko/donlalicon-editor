<template>
  <div class="flex">
    <div class="max-w-2xl mr-5">
      <editor v-model="blog.body" />
    </div>
    <div class="flex-grow">
      <div class="mb-4">
        <label for="title">Title</label>
        <input id="title" v-model="blog.title" type="text" placeholder="Title">
      </div>
      <div class="mb-4">
        <label for="id">ID</label>
        <input id="id" ref="id" v-model="blog.id" type="text" placeholder="ID">
      </div>
      <div class="mb-4">
        <label>
          <input v-model="blog.published" class="mr-2 leading-tight" type="checkbox">
          <span class="text-sm">
            Published
          </span>
        </label>
      </div>
      <div class="mb-4">
        <label for="lead">Lead</label>
        <textarea id="lead" v-model="blog.lead" placeholder="Lead" />
      </div>
      <div class="mb-4">
        <label for="teaser">Teaser</label>
        <textarea id="teaser" v-model="blog.teaser" placeholder="Teaser" />
      </div>
      <div class="mb-4">
        <label for="imageUrl">Image URL</label>
        <input id="imageUrl" v-model="blog.imageUrl" type="text" placeholder="Image URL">
      </div>
      <div class="mb-4">
        <label for="imageAlt">Image Alt</label>
        <input id="imageAlt" v-model="blog.imageAlt" type="text" placeholder="Image Alt">
      </div>
      <div class="mb-4">
        <label for="imageCaption">Image caption</label>
        <textarea id="imageCaption" v-model="blog.imageCaption" placeholder="Image caption" />
      </div>
      <div class="mb-4">
        <button
          :disabled="!!status"
          @click="submitForm"
          type="button"
        >
          {{ status ? status : 'Save' }}
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { cloneDeep } from 'lodash'
import Editor from '~/components/Editor'

export default {
  name: 'BlogForm',
  components: { Editor },
  props: {
    value: {
      type: Object,
      default: () => {}
    }
  },
  data () {
    return {
      blog: {},
      status: ''
    }
  },
  watch: {
    value: {
      handler (newValue) {
        this.blog = cloneDeep(newValue)
      },
      immediate: true
    }
  },
  methods: {
    submitForm () {
      // Validate the form.
      // @todo Check that the ID is unique.
      if (!this.blog.id) {
        alert('Please specify the blog ID.')
        this.$refs.id.focus()
      } else {
        this.updateValue()
      }
    },
    async updateValue () {
      this.status = 'Saving...'

      const db = this.$firebase.firestore()
      const blog = cloneDeep(this.blog)

      const id = blog.id
      delete blog.id

      if (!blog.created) {
        blog.created = this.$firebase.firestore.FieldValue.serverTimestamp()
      }

      try {
        await db.collection('blogs').doc(id).set({
          ...blog,
          changed: this.$firebase.firestore.FieldValue.serverTimestamp()
        })
      } catch (error) {
        alert('Unable to save blog')
      }

      blog.id = id
      this.$emit('input', cloneDeep(blog))

      this.status = ''
    }
  }
}
</script>

<style scoped>
label {
  @apply block text-gray-600 text-sm font-bold mb-2;
}

textarea,
input[type="text"] {
  @apply shadow appearance-none border rounded w-full py-2 px-3 leading-tight;
}

textarea:focus,
input[type="text"]:focus {
  @apply outline-none border-blue-500;
}

button {
  @apply text-gray-600 font-bold py-2 px-4 rounded border-gray-500 border shadow;
}
</style>
