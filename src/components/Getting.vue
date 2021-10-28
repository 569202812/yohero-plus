<template>
  <div v-if="loading">抓取中。。。</div>
  <div v-else>
    <div>
      <button @click="handleGetData1">请求</button>
    </div>

    <table class="table" cellspacing="0">
      <thead>
        <tr>
          <td>角色名称</td>
          <td>price</td>
          <td>部位数</td>
          <td>itemId</td>
          <td>跳转链接</td>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, index) in formatData" :key="index">
          <td>{{ item.occn }} {{ roleMap[item.occn] }}</td>
          <td>{{ +item.price / 1000000000000000000 }}</td>
          <td>{{ item.occnNum }}</td>
          <td>{{ item.itemId }}</td>
          <td>
            <a :href="item.link" target="_blank">{{ item.link }}</a>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script lang="ts">
import { onMounted, reactive, ref, computed } from "vue";
import axios from "axios";

type RecordItem = {
  amt: number;
  bidId: string;
  bidTime: number;
  bidprice: string;
  callNum: number;
  ctime: number;
  genesis: number;
  id: number;
  itemId: number;
  level: number;
  loadedFromDb: boolean;
  occn: 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8;
  parts: number[];
  pexp: number;
  pid: string;
  playerId: string;
  price: string;
  skills: number[];
  sprice: number;
  summers: number[];
  table: string;
};

export default {
  setup() {
    // 对应关系
    const roleMap = ref({
      1: "练气师",
      2: "萨满",
      3: "法师",
      4: "战士",
      5: "奶妈",
      6: "术士",
      7: "骑士",
      8: "刺客",
    });

    const dataSource = ref<RecordItem[]>([]);
    const loading = ref(false);

    const formatData = computed(() => {
      console.log(dataSource.value.length);
      // 出现次数超过三
      const greaterArr = dataSource.value.filter((item: any) => {
        const occnNumArr = item.parts.filter(
          (num: number) => num === item.occn
        );
        return occnNumArr.length > 2;
      });
      const newData = greaterArr
        .sort((prev, next) => +prev.price - +next.price)
        .map((item) => {
          const occnNumArr = item.parts.filter((num) => num === item.occn);
          return {
            ...item,
            occnNum: occnNumArr.length,
            link: `https://yohero.fi/token/${item.itemId}`,
          };
        });
      console.log(newData);
      return newData;
    });

    const handleGetData = (page = 1) => {
      loading.value = true;
      var data = JSON.stringify({
        code: 1096,
        playerId: "",
        message: {
          occns: [],
          minCallNum: 0,
          maxCallNum: 7,
          order: "4",
          page,
        },
      });

      var config = {
        method: "post",
        // url: "/api/api",
        url: "https://yohero.fi/api",
        headers: {
          "Content-Type": "application/json",
        },
        data: data,
      };

      axios(config as any)
        .then(function (response) {
          const { message } = response.data;

          const { list } = JSON.parse(message || "{}");

          if (list && list.length) {
            // setTimeout(() => {
            handleGetData(page + 1);
            // }, 5000);

            dataSource.value = [...dataSource.value, ...list];
          } else {
            console.log(JSON.stringify(dataSource.value));
            loading.value = false;
            alert("抓取完成");
          }
        })
        .catch(function (error) {
          loading.value = false;
          alert("抓取失败");
          console.log(error);
        });
    };

    onMounted(() => {
      // handleGetData(1);
    });

    const handleGetData1 = () => {
      handleGetData(1);
    };

    return {
      loading,
      roleMap,
      dataSource,
      formatData,
      handleGetData1,
    };
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.table {
  width: 100%;
  margin-top: 15px;
  border: 1px solid #000;
}
.table td {
  border: 1px solid #000;
}
</style>
