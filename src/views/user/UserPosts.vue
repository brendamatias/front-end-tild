<template>
  <section>
    <h2>Your posts</h2>
    <p v-if="noHavePosts" class="loading">You don't have any posts yet.</p>
    <LoadingData v-if="loading" />
    <transition-group v-else name="list" tag="ul">
      <ul v-for="(post, index) in userPosts" :key="index">
        <PostItem :post="post" :key="post.id">
          <button @click="deletePost(post.id)" class="delete">Delete</button>
          <button @click="updatePost(post.id, post.title, post.content)" class="update">Update</button>
        </PostItem>
      </ul>
    </transition-group>
  </section>
</template>

<script>
import PostItem from "@/components/PostItem.vue";
import LoadingData from "@/components/LoadingData.vue";
import { mapState, mapActions } from "vuex";
import { api } from "@/services.js";

export default {
  name: "UserPosts",
  components: {
    PostItem,
    LoadingData
  },
  data() {
    return {
      userPosts: this.$store.state.user.posts,
      loading: false,
      noHavePosts: false,
      erros: []
    };
  },
  methods: {
    deletePost(id) {
      this.loading = true;

      const confirm = window.confirm("Do you want to remove this post?");
      if (confirm) {
        api
          .delete(`/posts/${id}`)
          .then(() => {
            this.getPost();
          })
          .catch(error => {
            "An error occurred while trying to delete the post. <br/> Try again!";
          });
      }
    },
    updatePost(id, title, content) {
      /*this.loading = true;*/
      this.erros = [];
      var body = {
        title: title,
        content: content
      };
      this.erros.push(
        "An error occured while trying to update the post.<br/>Try again!"
      );
      /*
      api
        .put(`/posts/${id}/`, body)
        .then(() => {
          this.successes.push("Data updated successfully.");
        })
        .catch(error => {
          this.erros.push(
            "An error occured while trying to update the post.<br/>Try again!"
          );
        });*/
      this.loading = false;
    },
    async getPost() {
      await this.$store.dispatch("getUser", this.$store.state.user.email);
      this.userPosts = this.$store.state.user.posts;
      if (this.userPosts.length <= 0) {
        this.noHavePosts = true;
      }
      this.loading = false;
    }
  },
  watch: {
    getPost() {
      this.loading = true;
    }
  },
  created() {
    this.loading = true;
    this.getPost();
  }
};
</script>

<style lang="sass" scoped>
@import '@/styles/style.sass'

section 
  width: 800px
  margin: 0 auto

h2  
  text-align: center
  margin-bottom: 20px
  font-weight: 600
  padding-bottom: 15px
  border-bottom: 2px solid $blue

.list-enter,
.list-leave-to 
  opacity: 0
  transform: translate3d(20px, 0, 0)


.list-enter-active,
.list-leave-active 
  transition: all 0.3s

.delete, .update
  font-size: 12px
  text-transform: uppercase
  padding: 4px 20px  

.delete 
  position: absolute
  top: 0px
  right: 0px
  background: $red-light

.delete:hover
  background: $red-hover

.update
  background: $green

.update:hover 
  background: $green-hover

.loading 
  width: 800px
  font-size: 30px
  text-align: center
  margin-top: 60px
</style>
