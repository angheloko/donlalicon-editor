<template>
  <div class="w-full max-w-xs mx-auto">
    <form
      @submit.prevent
      class="bg-white shadow-md rounded px-8 pt-6 pb-8 mb-4"
    >
      <div class="mb-4">
        <label for="email">
          Email
        </label>
        <input
          id="email"
          v-model="email"
          type="text"
          placeholder="Email"
        >
      </div>
      <div class="mb-6">
        <label for="password">
          Password
        </label>
        <input
          id="password"
          v-model="password"
          type="password"
          placeholder="Password"
        >
      </div>
      <div class="flex items-center justify-between">
        <button
          @click="submitForm"
          type="submit"
        >
          Sign In
        </button>
      </div>
    </form>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'

export default {
  name: 'LoginPage',
  middleware: 'anonymous-access',
  data () {
    return {
      email: '',
      password: ''
    }
  },
  computed: {
    ...mapGetters(['auth'])
  },
  watch: {
    auth (value) {
      if (value) {
        this.$router.push({
          path: '/'
        })
      }
    }
  },
  methods: {
    submitForm () {
      this.$firebase.auth().signInWithEmailAndPassword(this.email, this.password)
        .catch(function (error) {
          alert(error.message)
        })
    }
  }
}
</script>

<style scoped>
input[type="text"],
input[type="password"] {
  @apply shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight;
}

input[type="text"]:focus,
input[type="password"]:focus {
  @apply outline-none;
}

label {
  @apply block text-gray-700 text-sm font-bold mb-2;
}

button {
  @apply bg-blue-500 text-white font-bold py-2 px-4 rounded;
}

button:hover {
  @apply bg-blue-700;
}

button:focus {
  @apply outline-none shadow-outline;
}
</style>
