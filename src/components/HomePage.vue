<template>
  <body class="home">
    <div class="row">
      <div class="col">
        <ul class="sidebar">
          <li class="sidebarComponent"><i class="bi bi-house-fill"></i>Home</li>
          <li class="sidebarComponent"><i class="bi bi-search"></i>Explore</li>
          <li class="sidebarComponent">
            <i class="bi bi-bell-fill"></i> Notifications
          </li>
          <li class="sidebarComponent">
            <i class="bi bi-envelope-fill"></i>Messages
          </li>
          <li class="sidebarComponent">
            <i class="bi bi-bookmark-fill"></i>Bookmarks
          </li>
          <li class="sidebarComponent"><i class="bi bi-list"></i>Lists</li>
          <li class="sidebarComponent"><i class="bi bi-person"></i>Profile</li>
          <li class="sidebarComponent"><i class="bi bi-three-dots"></i>More</li>
        </ul>
      </div>
      <div class="col">
        <textarea
          name="tweetMessage"
          id="tweet"
          cols="50"
          rows="5"
          class="tweet"
          v-model="message"
        ></textarea>
        <button class="btn btn-primary createPost" @click="createPost">Post</button>
        <ul class="list-group posts">
          <li
            v-for="(post, idx) in posts"
            :key="post.id"
            class="list-group-item posts" 
          >
            <img :src="require(`@/img/${post.user.imageUrl}`)" alt="profilePic" class="profilePic">
            <h3 class="postUsername">{{ post.user.twitterHandle }}</h3>

            <div v-if="!post.edit">
              <p class="postMessage">{{ post.message }}</p>
            </div>

            <div v-if="post.edit">
              <textarea
                name="editPost"
                id="editTweet"
                cols="30"
                rows="10"
                class="tweet"
                v-model="post.message"
              ></textarea>
              <button @click="updatePost(idx)">Save</button>
            </div>

            <p class="postStats">
              <i class="bi bi-heart" @click="likePost(idx)"></i>{{ post.likes }}
              <i class="bi bi-share" @click="sharePost(idx)"></i> {{ post.shares }}
            </p>
            <button class="btn btn-danger" @click="deletePost(idx)">Delete</button>
            <button class="btn btn-info" @click="beginEdit(idx)">Edit</button>
          </li>
        </ul>
      </div>
      <div class="col"></div>
    </div>
  </body>
</template>

<script>
import axios from "axios";
export default {
  name: "HomePage",

  data: () => ({
    posts: [],
    users: [],
    message: "",
    currentUser: undefined,
    newMessage: "",
    deletedAmount: 1,
  }),

  methods: {
    getPosts() {
      axios
        .get("https://localhost:44310/api/posts")
        .then((res) => {
          this.posts = res.data;
        })
        .catch((error) => {
          console.log(error.response.data);
        });
    },
    getUsers() {
      axios
        .get("https://localhost:44310/api/users")
        .then((res) => {
          this.users = res.data;
        })
        .catch((error) => {
          console.log(error.response.data);
        });
    },
    getCurrentUser() {
      axios
        .get("https://localhost:44310/api/users/4")
        .then((res) => {
          this.currentUser = res.data;
        })
        .catch((error) => {
          console.log(error.response.data);
        });
    },
    test() {
      console.log("CURRENTUSER: ", this.currentUser);
    },
    deletePost(idx) {
      axios.delete("https://localhost:44310/api/posts/" + this.posts[idx].id).then(() => {
        this.deletedAmount++
        this.getPosts();
      });
      console.log(idx);
    },
    createPost() {
      this.getCurrentUser();
      const post = {
        message: this.message,
        UserId: this.currentUser.id,
        likes: 0,
        shares: 0,
      };
      console.log(post);
      console.log(this.currentUser);
      axios
        .post("https://localhost:44310/api/posts", post, {
          headers: { "Content-Type": "application/json" },
        })
        .then((res) => ((this.id = res.data.id), this.getPosts()))
        .catch((error) => {
          console.log(error.response.data);
        });
    },
    beginEdit(idx) {
      this.posts[idx].edit = true;

    },
  
    updatePost(idx) {
        const newPost = {
          message: this.posts[idx].message,
          likes: this.posts[idx].likes,
          shares: this.posts[idx].shares
        }
        axios.put("https://localhost:44310/api/posts/" + this.posts[idx].id, newPost).then(res =>{
          console.log(res);
          this.getPosts();
          this.editting = false;
        }).catch((error) => {
          console.log(error.response.data);
        });
    },
    likePost(idx){
      console.log(this.posts[idx].id)
              const newPost = {
          message: this.posts[idx].message,
          likes: this.posts[idx].likes+1,
          shares: this.posts[idx].shares
        }
        axios.put("https://localhost:44310/api/posts/" + this.posts[idx].id, newPost).then(res =>{
          console.log(res);
          this.getPosts();
        }).catch((error) => {
          console.log(error.response.data);
        });
    },
    sharePost(idx){
              const newPost = {
          message: this.posts[idx].message,
          likes: this.posts[idx].likes,
          shares: this.posts[idx].shares+1
        }
        axios.put("https://localhost:44310/api/posts/" + this.posts[idx].id, newPost).then(res =>{
          console.log(res);
          this.getPosts();
        }).catch((error) => {
          console.log(error.response.data);
        });
    }

  },
  async mounted() {
    this.getPosts();
    this.getUsers();
    this.getCurrentUser();
  },
};
</script>

<style>
@import "bootstrap-icons/font/bootstrap-icons.css";
.home {
  background-color: #1da1f2;
}
.tweet {
  background-color: white;
  resize: none;
}
.posts {
  background-color: white;
}
.postUsername {
  background-color: white;
}
.postMessage {
  background-color: white;
}
.postStats {
  background-color: white;
}
.sidebar {
  list-style: none;
  padding: 10%;
}
.sidebarComponent {
  margin-bottom: 5%;
}
.sidebarComponent:hover {
  color: blue;
}
.profilePic {
  max-height: 150px;
  max-width: 150px;
  border-radius: 150px;
  
}
.bi:hover {
  background-color: lightblue;
  box-shadow: rgba(0,0,0,0.6);
  border-radius:100px;
}
.createPost {
  min-width: 300px
}

@import "~bootstrap/dist/css/bootstrap.css";
</style>