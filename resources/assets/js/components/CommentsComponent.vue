<template>
  <div>
    <h3>Comments:</h3>
    <div style="margin-bottom:50px;" v-if="user">
      <textarea
        class="form-control"
        rows="3"
        name="body"
        placeholder="Leave a comment"
        v-model="commentBox"
      ></textarea>
      <button
        class="btn btn-success"
        style="margin-top:10px"
        @click.prevent="postComment"
      >Save Comment</button>
    </div>
    <div v-else>
      <h4>You must be logged in to submit a comment!</h4>
      <a href="/login">Login Now &gt;&gt;</a>
    </div>

    <div class="media" style="margin-top:20px;" v-for="(comment, index) in comments" :key="index">
      <div class="media-left" style="margin-right:20px;">
        <a href="#">
          <img class="media-object" src="http://placeimg.com/80/80" alt="..." />
        </a>
      </div>
      <div class="media-body">
        <h4 class="media-heading">{{comment.user.name}} said...</h4>
        <p>{{comment.body}}</p>
        <span style="color: #aaa;">on {{comment.created_at}}</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["user", "postId"],
  data() {
    return {
      commentBox: "",
      comments: ""
    };
  },
  mounted() {
    this.getComments();
    this.listen();
  },
  methods: {
    getComments() {
      axios
        .get("/api/posts/" + this.postId + "/comments")
        .then(response => {
          this.comments = response.data;
        })
        .catch(function(error) {
          console.log(error);
        });
    },
    postComment() {
      axios
        .post("/api/posts/" + this.postId + "/comment", {
          api_token: this.user.api_token,
          body: this.commentBox
        })
        .then(response => {
          this.comments.unshift(response.data);
          this.commentBox = "";
        })
        .catch(error => {
          console.log(error);
        });
    },
    listen() {
      Echo.channel("post." + this.postId).listen("NewComment", res => {
        this.comments.unshift(res);
      });
    }
    // listen() {
    //   Echo.private("post." + this.post.id).listen("NewComment", comment => {
    //     this.comments.unshift(comment);
    //   });
    // }
  }
  //   mounted() {
  //     console.log("response.data");
  //     this.getComments();
  //     //this.listen();
  //   },
  //   methods: {
  //     getComments() {
  //       axios
  //         .get("/api/posts/" + this.post.id + "/comments")
  //         .then(response => {
  //           this.comments = response.data;
  //           console.log("response.data");
  //         })
  //         .catch(function(error) {
  //           console.log(error);
  //         });
  //     },
  //     postComment() {
  //       axios
  //         .post("/api/posts/" + this.post.id + "/comment", {
  //           api_token: this.user.api_token,
  //           body: this.commentBox
  //         })
  //         .then(response => {
  //           this.comments.unshift(response.data);
  //           this.commentBox = "";
  //         })
  //         .catch(error => {
  //           console.log(error);
  //         });
  //     },
  //     listen() {
  //       Echo.private("post." + this.post.id).listen("NewComment", comment => {
  //         this.comments.unshift(comment);
  //       });
  //     }
  //}
};
</script>
