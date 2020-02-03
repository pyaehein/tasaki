<template>
  <div class="container">
    <div style="width: 500px">
      <el-form label-position="left" label-width="100px" :model="form">
        <el-form-item label="Title">
          <el-input :minlength="3" :maxlength="50" show-word-limit v-model="form.title"/>
        </el-form-item>
        <el-form-item label="Description">
          <el-input
            :minlength="10"
            :maxlength="150"
            show-word-limit
            type="textarea"
            :rows="3"
            v-model="form.body">
          </el-input>
        </el-form-item>
        <el-form-item label="Post">
          <el-select v-model="form.id" placeholder="please select your news">
            <el-option v-for="post in posts" :label="post.title" :value="post.id" :key="post.id"/>
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" round :disabled="buttonDisable" @click="sendNotification">Send Notification to Mobiles</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>

export default {
  head: {
    title: 'Tasaki Air Myanmar - Notification'
  },
  data () {
    return {
      buttonDisable: false,
      form: {
        title: '',
        body: '',
        id: null,
      },
      posts: [],
    }
  },
  methods: {
    async loadingPosts() {
      let posts = await this.$axios.get('https://www.tasakiair.com/jsonapi/node/news?jsonapi_include=1&fields[node--news]=id,title')
      this.posts = posts.data.data
    },
    async sendNotification () {
      let vm = this
      vm.buttonDisable = true
      let headers = {
        Authorization: 'key=AIzaSyBU-9hiZucytd3pN0_DCyjewDR9gF-skG0'
      }
      this.$axios.post('https://fcm.googleapis.com/fcm/send', {
        to: "/topics/all",
        notification: vm.form,
        data: vm.form
      }, {headers}).then(() => {
        this.$notify({
          title: 'Success',
          message: 'Your notification has been sent.',
          type: 'success'
        });
        this.form = {
          title: '',
            body: '',
            id: null,
        }
        vm.buttonDisable = false
      }).catch(function (error) {
        this.$notify.error({
          title: 'Error',
          message: 'Notification failed to send.'
        });
        vm.buttonDisable = false
      });
    }
  },
  mounted() {
    this.loadingPosts()
  }
}
</script>

<style>
.container {
  padding-top: 3vh;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: flex-start;
  text-align: center;
}
.el-select {
  width: 400px;
}
</style>
