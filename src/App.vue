<template>
  <div id="app">
    <header>
      <h1>欢迎来到我的自我介绍网站！</h1>
    </header>
    <main>
      <IntroCard :info="info" />

      <!-- 新增的输入框和按钮 -->
      <div class="input-container">
        <input
          type="text"
          v-model="userMessage"
          placeholder="在这里输入内容..."
        />
        <button @click="sendMessage">发送</button>
        <p v-if="responseMessage" class="response">服务器响应: {{ responseMessage }}</p>
      </div>
      <!-- 数据表格 -->
      <div class="table-container">
        <h2>编辑数据表</h2>
        <table>
          <thead>
            <tr>
              <th>姓名</th>
              <th>年龄</th>
              <th>职业</th>
              <th>操作</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(row, index) in tableData" :key="index">
              <td><input type="text" v-model="row.name" /></td>
              <td><input type="number" v-model="row.age" /></td>
              <td><input type="text" v-model="row.job" /></td>
              <td><button @click="deleteRow(index)">删除</button></td>
            </tr>
          </tbody>
        </table>
        <button @click="addRow">添加行</button>
        <button @click="sendTableData">发送数据</button>
        <p v-if="responseMessage" class="response">服务器响应: {{ responseMessage }}</p>
      </div>
      
      <MineSweeper />
    </main>
    <footer>
      <p>© 2024 [你的名字] - 自我介绍网站</p>
    </footer>
  </div>
</template>

<script>
import IntroCard from "./components/IntroCard.vue";
import MineSweeper from "./components/MineSweeper.vue";
import axios from "axios";

export default {
  name: "App",
  components: {
    IntroCard,
    MineSweeper,
  },
  data() {
    return {
      info: {
        name: "张三",
        bio: "一名全栈开发者，擅长 Vue.js 和 Python。",
        interests: ["编程", "阅读", "旅行"],
        email: "zhangsan@example.com",
        github: "https://github.com/zhangsan",
      },
      tableData: [
        { name: "张三", age: 25, job: "开发者" },
        { name: "李四", age: 30, job: "产品经理" },
      ],
      responseMessage: "", // 用于显示服务器的响应消息
      userMessage: "", // 用户输入的消息
    };
  },
  methods: {
    addRow() {
      // 添加新的一行
      this.tableData.push({ name: "", age: null, job: "" });
    },
    deleteRow(index) {
      // 删除指定行
      this.tableData.splice(index, 1);
    },
    async sendTableData() {
      // 发送表格数据到后端
      try {
        const response = await axios.post("http://localhost:8086/api/table", {
          data: this.tableData,
        });
        this.responseMessage = response.data.message || "发送成功！";
      } catch (error) {
        console.error("发送数据失败:", error);
        this.responseMessage = "发送失败，请检查服务器是否运行。";
      }
    },
    async sendMessage() {
      if (!this.userMessage.trim()) {
        alert("请输入有效的内容！");
        return;
      }
      try {
        const response = await axios.post("http://localhost:8086/api/message", {
          message: this.userMessage,
        });
        this.responseMessage = response.data; // 假设后端返回的响应是文本
      } catch (error) {
        console.error("发送消息失败:", error);
        this.responseMessage = "发送失败，请检查服务器是否运行。";
      }
    },
  },
};
</script>

<style>
/* 输入框样式 */
.input-container {
  margin: 20px auto;
  text-align: center;
}

.input-container input {
  width: 60%;
  padding: 10px;
  margin: 10px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.input-container button {
  padding: 10px 20px;
  font-size: 16px;
  color: #fff;
  background-color: #3498db;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.input-container button:hover {
  background-color: #2980b9;
}

.response {
  margin-top: 10px;
  font-size: 16px;
  color: #2c3e50;
  font-weight: bold;
}
/* 表格样式 */
.table-container {
  margin: 20px auto;
  text-align: center;
}

table {
  margin: 10px auto;
  border-collapse: collapse;
  width: 80%;
}

table th,
table td {
  border: 1px solid #ccc;
  padding: 10px;
  text-align: center;
}

table input {
  width: 100%;
  padding: 5px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

button {
  padding: 10px 15px;
  margin: 5px;
  font-size: 16px;
  color: #fff;
  background-color: #3498db;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #2980b9;
}

.response {
  margin-top: 10px;
  font-size: 16px;
  color: #2c3e50;
  font-weight: bold;
}
</style>
