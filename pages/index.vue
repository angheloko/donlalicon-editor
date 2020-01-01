<template>
  <div>
    <table class="w-full">
      <thead>
        <tr>
          <th class="border px-4 py-2">
            Title
          </th>
          <th class="border px-4 py-2">
            Created
          </th>
          <th class="border px-4 py-2">
            &nbsp;
          </th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td class="border px-4 py-2">
            Intro to CSS
          </td>
          <td class="border px-4 py-2">
            25 Dec 2019
          </td>
          <td class="border px-4 py-2">
            Edit
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: 'HomePage',
  middleware: 'authenticated-access',
  data () {
    return {
      blog: {}
    }
  }
}
</script>

<style scoped>
</style>
