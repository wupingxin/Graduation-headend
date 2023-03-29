<template>
  <div>
    <el-card>
      <div class="logo">
        <img src="../assets/img/1.jpg" alt="logo" />
        <div class="mes-rut">
          <div class="dva rut-tit1">通知</div>
          <div class="dva rut-line"></div>
          <div class="dva rut-tit2 not-tit1">
            <div class="inner-container rut-tit3">
              xx月xx日校领导来审查，请各位同学注意寝室卫生！
            </div>
          </div>
        </div>
      </div>
      <!-- 轮播图 -->
      <el-carousel height="300px" type="card" :interval="2000">
        <el-carousel-item>
          <img src="../assets/img/qs1.png" alt />
        </el-carousel-item>
        <el-carousel-item>
          <img src="../assets/img/qs2.png" alt />
        </el-carousel-item>
        <el-carousel-item>
          <img src="../assets/img/qs3.jpg" alt />
        </el-carousel-item>
        <el-carousel-item>
          <img src="../assets/img/qs4.png" alt />
        </el-carousel-item>
        <el-carousel-item>
          <img src="../assets/img/qs5.jpg" alt />
        </el-carousel-item>
      </el-carousel>
      <h2 class="welcome">欢迎使用学生宿舍管理系统！</h2>
    </el-card>
  </div>
</template>

<script>
export default {
  data() {
    return {};
  },
  methods: {},
};
</script>

<style lang="css" scoped>

.mes-rut {
  position: absolute;
  /* left: 150px; */
  width: 45rem;
  height: 3rem;
  /* line-height: 1rem; */
  /* background-color: #d4e5f8; */
  z-index: 99;
}
.dva {
  display: inline-block;
  /* vertical-align: middle; */
}
.rut-tit1 {
  position: absolute;
  left: 30px;
  height: 100%;
  font-family: PingFangSC-Regular;
  font-size: 30px;
  color: #5991d1;
}
.rut-line {
  position: absolute;
  left: 100px;
  top: 12px;
  color: #5e66c1;
  height: 1.2rem;
  width: 1.5px;
  background: #86c5d6;
}
/* 重点实现样式 */
.not-tit1 {
  position: absolute;
  left: 7.5rem;
  top: 5px;
  /* width: 80%; */
  height: 100%;
  overflow: hidden;
}
.inner-container {
  height: 100%;
  font-family: PingFangSC-Regular;
  font-size: 25px;
  color: #5991d1;
  animation: myMove 10s linear infinite;
}
/*文字无缝滚动*/
@keyframes myMove {
  0% {
    transform: translateX(0);
  }
  100% {
    transform: translateX(-300px);
  }
}
.rut-tit3 {
  white-space: nowrap;
}
/*以上*/
.el-carousel__item img {
  height: 100%;
  width: 100%;
}
.welcome {
  text-align: center;
  margin: 40px 0px;
}
.logo {
  height: 60px;
  display: flex;
  align-items: center;
  margin-bottom: 20px;
  text-align: center;
}
.logo img {
  height: 100%;
}
.logo .mes-rut {
  margin-left: 120px;
}
</style>
