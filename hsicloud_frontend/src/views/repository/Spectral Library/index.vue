<template>
  <el-row>
    <el-col :span="10">
      <div class="grid-content bg-purple left-content">
        <h3>Spectral Library</h3>
        <template v-for="(prop, index) in items">
          <div
            slot="header"
            @click.stop="getSpec(prop.id, index)"
            :key="index"
            class=""
          >
            <br />
            <li class="infinite-list-item">{{ prop.name }}</li>

            <!-- <el-input disabled="true">{{ prop.name }}</el-input> -->
          </div>
          <dd v-for="op in prop.options" :key="op.id">
            <input
              v-model="op.status"
              type="checkbox"
              :value="op"
              @change="showSpec(op)"
            />{{ " " + op.value }}
          </dd>
        </template>
      </div>
    </el-col>
    <el-col :span="12">
      <div>
        <div class="chart-container">
          <h3>Spectral Seleted</h3>
          <chart height="400px" width="50%" :swss="allImgDate" />
        </div>
      </div>
    </el-col>
  </el-row>
</template>

<script>
import { getSpecLibByName } from "@/api/image";
import { getSpecByLib } from "@/api/image";
import { getSpecDataById } from "@/api/image";
import {
  fetchSpectralNames,
  fetchSpectralById,
  fetchSpectralInformationByID,
} from "@/api/spectrallibrary";
import chart from "@/components/common/specImg";

export default {
  components: { chart },
  props: {},
  data() {
    return {
      selectedProperties: [],
      items: [],
      select: "",
      restaurants: [],
      state: "",
      timeout: null,
      allLibNameData: [], // 库
      allSpec: [], // 库中图片
      allImgDate: [], // 图片参数
      ifshow: [],
      imgUrl: "",
      isshow: 0,
    };
  },
  created() {
    this.getLib();
  },
  methods: {
    handleChange(val) {},
    // 获取所有的库
    getLib() {
      fetchSpectralNames().then((response) => {
        response.data.forEach((x, index) => {
          this.items.push({
            name: x.name,
            type: 1,
            id: x.id,
            options: [],
          });
        });

        this.allLibNameData = response.data;
      });
    },
    // 获取id的库所有的光谱
    getSpec(id, index) {
      fetchSpectralById(id).then((response) => {
        response.data.forEach((x, index2) => {
          console.log(x);
          this.items[index].options.push({
            id: x.id,
            value: x.specname,
          });
        });

        this.allSpec = response.data;
        // this.ifshow = id
      });
    },
    // 展示所有光谱的图片
    showSpec(op) {
      // 加载光谱图片
      console.log(op);
      if (op.status == true) {
        fetchSpectralInformationByID(op.id).then((response) => {
          this.allImgDate.push({
            id: op.id,
            reflectStr: response.data[0].relectivity.split(","),
            waveLenStr: response.data[0].wavelength.split(","),
          });
        });
      } else {
        console.log("quexiao");
        this.allImgDate.splice(
          this.allImgDate.findIndex((item) => item.id === op.id),
          1
        );
      }
    },
    search_now(v1, v2) {
      // 搜索功能
      console.log(v1 + v2);
    },
    JumpToList(id) {
      this.$router.push({
        path: "/repository/List Details",
        query: { userId: id /* 参数等API*/ },
      });
    },
  },
};
</script>

<style scoped>
.col {
  color: black;
  font-weight: bold;
}

.infinite-list-item {
  justify-content: center;
  margin: 10px;
  background-color: "#e8f3fe";
  list-style-type: square;
  cursor: pointer;
}
.left-content {
  height: 100%;
}

.col2 :hover {
  color: black;
  background-color: aquamarine;
}
.curve {
  position: absolute;
  left: 400px;
  width: 450px;
  height: 300px;
  background-color: red;
}
.el-row {
  margin-bottom: 20px;
  &:last-child {
    margin-bottom: 0;
  }
}
.el-col {
  border-radius: 4px;
  padding-right: 25px;
}
.bg-purple-dark {
  background: #99a9bf;
}
.grid-content {
  border-radius: 4px;

  min-height: 26px;
  margin-left: 30px;
}
.chart-container {
  position: fixed;
  width: 100%;
}
.chart_bottom {
  position: relative;
  width: 300px;
  height: 100px;
  left: 50px;
  bottom: 40px;
}
.row-bg {
  padding: 10px 0;
  background-color: #f9fafc;
}

.chart_main {
  position: relative;
  left: -250px;
}
.btn1 {
  margin-top: 30px;
}
.result {
  position: fixed;
  width: 400px;
  margin-left: 300px;
  margin-top: -60px;
}
.result_main {
  position: relative;
}
</style>
