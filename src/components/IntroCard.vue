<template>
  <div class="card">
    <img class="profile-pic" :src="profilePic" alt="Profile Picture" />
    <h2>{{ info.name }}</h2>
    <p>{{ info.bio }}</p>
    <h3>兴趣爱好</h3>
    <ul>
      <li v-for="(interest, index) in info.interests" :key="index">
        {{ interest }}
      </li>
    </ul>
    <h3>联系方式</h3>
    <p>邮箱: <a :href="'mailto:' + info.email">{{ info.email }}</a></p>
    <p>GitHub: <a :href="info.github" target="_blank">{{ info.github }}</a></p>
  </div>
</template>

<script>
export default {
  name: "IntroCard",
  props: {
    info: Object,
  },
  data() {
    return {
      profilePic: require("../assets/profile.png"), // 替换为你的头像路径
    };
  },
};
</script>

<style scoped>
.card {
  background: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  padding: 20px;
  max-width: 400px;
  margin: 20px auto;
  text-align: left;
}

.profile-pic {
  display: block;
  margin: 0 auto 10px;
  width: 100px;
  height: 100px;
  border-radius: 50%;
  object-fit: cover;
}

h2 {
  text-align: center;
  color: #333;
}

ul {
  list-style: none;
  padding: 0;
}

a {
  color: #42b983;
  text-decoration: none;
}

a:hover {
  text-decoration: underline;
}
</style>
