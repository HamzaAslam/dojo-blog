<template>
  <div class="create">
    <form @submit.prevent="handleSubmit">
      <label>Title</label>
      <input v-model="title" type="text" required />
      <label>Content:</label>
      <textarea v-model="body" required></textarea>
      <label>Tags('Hit enter to add tag')</label>
      <input v-model="tag" type="text" @keydown.enter.prevent="handleKeyDown" />
      <div class="pill" v-for="tag in tags" :key="tag">#{{ tag }}</div>
      <button>Add Post</button>
    </form>
  </div>
</template>
<script>
import { ref } from "vue";
import { useRouter } from "vue-router";
import { projectFireStore, timestamp } from "../firebase/config.js";
export default {
  setup() {
    const title = ref("");
    const body = ref("");
    const tag = ref("");
    const tags = ref([]);
    const router = useRouter();

    const handleKeyDown = () => {
      if (!tags.value.includes(tag.value)) {
        tag.value = tag.value.replace(/\s/, "");
        tags.value.push(tag.value);
      }
      tag.value = "";
    };
    const handleSubmit = async () => {
      const post = {
        title: title.value,
        body: body.value,
        tags: tags.value,
        createdAt: timestamp(),
      };

      const res = await projectFireStore.collection("posts").add(post);

      //   title.value = "";
      //   body.value = "";

      router.push("/");
    };

    return { title, body, tag, tags, handleKeyDown, handleSubmit };
  },
};
</script>
<style>
form {
  max-width: 480;
  margin: 0 auto;
  text-align: left;
}
input,
textarea {
  display: block;
  width: 100%;

  margin: 10px 0;
  box-sizing: border-box;
  padding: 10px;
  border: 1px solid #c8c6c6;
}
textarea {
  height: 160px;
}
label {
  display: inline-block;
  margin-top: 30px;
  position: relative;
  font-size: 20px;
  color: #fff;
  margin-bottom: 10px;
}
label::before {
  content: "";
  display: block;
  height: 100%;
  width: 100%;
  background: #ff8800;
  position: absolute;
  z-index: -1;
  padding-right: 40px;
  left: -30px;
  transform: rotateZ(-1.5deg);
}
button {
  display: block;
  margin-top: 30px;
  background: #ff8800;
  color: #fff;
  border: none;
  padding: 8px 16px;
  font-size: 18px;
}
.pill {
  display: inline-block;
  margin: 10px 10px 0 0;
  color: #444;
  background: #ddd;
  padding: 8px;
  border-radius: 20px;
  font-size: 14px;
}
</style>
