<template>
  <div>
    <ion-app>
      <ion-content padding>
        <ion-text mode="ios" class="ion-no-margin">
          <h1>Edit task</h1>
        </ion-text>
        <ion-item>
          <ion-input placeholder="Title" autofocus :value="task.title" @ionInput="task.title = $event.target.value;" />
        </ion-item>
        <ion-item>
          <ion-textarea placeholder="Description" auto-grow :value="task.description"
            @ionInput="task.description = $event.target.value;" />
        </ion-item>
        <ion-item lines="none">
          <ion-text>
            Deadline
          </ion-text>
        </ion-item>
        <v-date-picker v-model="task.deadline" :data="task.deadline" is-inline color="indigo" title-position="left"
          is-expanded :min-date='new Date()' locale="id"></v-date-picker>
        <div class="ion-padding-vertical">
          <ion-button color="primary" expand="block" mode="ios" @click="save">
            Save
          </ion-button>
          <router-link :to="{name: 'todo'}" style="text-decoration: none !important;">
            <ion-button color="danger" expand="block" mode="ios">
              Cancel
            </ion-button>
          </router-link>
        </div>
      </ion-content>
    </ion-app>
  </div>
</template>

<script>
  var moment = require('moment');

  export default {
    name: "EditTodo",
    data() {
      return {
        task: {
          title: "",
          description: "",
          deadline: new Date(),
          id: ""
        }
      };
    },
    created() {
      const id = this.$route.params.id
      this.$store.dispatch('show', id)
        .then((response) => {
          const data = response.data.data
          this.task.title = data.title
          this.task.deadline = new Date(data.deadline)
          this.task.description = data.description
          this.task.id = id
        })
        .catch((error) => {
          console.log(error.response)
        })
    },
    methods: {
      save() {
        this.$store.dispatch('update', {
            title: this.task.title,
            description: this.task.description,
            deadline: moment(this.task.deadline).format("YYYY-MM-DD"),
            id: this.task.id
          })
          .then((response) => {
            this.$ionic.toastController.create({
              message: 'Success!',
              duration: 1000,
              mode: 'ios',
              posittion: 'bottom',
              color: 'primary'
            }).then(t => t.present())

            setTimeout(() => {
              this.$router.push({
                name: 'todo'
              })
            }, 1000)
          })
          .catch((error) => {
            if (error.response.status === 400) {
              this.$ionic.toastController.create({
                message: 'Something wrong!',
                duration: 2000,
                mode: 'ios',
                posittion: 'bottom',
                color: 'warning'
              }).then(t => t.present())
            }
          })
      }
    }
  }

</script>
