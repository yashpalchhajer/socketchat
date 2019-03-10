<template>
  <div class="container">
    <div class="row justify-content-center">
      <div class="col-md-8">
        <div class="card">
          <p v-for="(message,key) in messages" :key="key">{{message}}</p>
          <input type="text" name id v-model="text">
          <button type="button" @click="postMessage" :disabled="!contentExists">Send</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      text: "",
      messages: []
    };
  },
  computed: {
    contentExists() {
      return this.text.length > 0;
    }
  },
  methods: {
    postMessage() {
      axios.post("/post", { message: this.text }).then(({ data }) => {
        this.messages.push(data.content);
        this.text = "";
      });
    }
  },
  created() {
    axios.get("/getAll").then(({ data }) => {
      this.messages = data;
    });

  },
  mounted(){
    Echo.channel('public').listen('MessagePushed',({message}) => {
        this.messages.push(message.content);
    })
  }
};
</script>
