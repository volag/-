<template>
    <div v-show="pdfInfoVisiable">
      <section class="files-container" :class="{ 'full-screen': isFullScreen }">
        <div class="file-container">
          <pdf
            :src="fileName"
            :page="currentPage"
            @num-pages="pageCount = $event"
            @page-loaded="currentPage = $event"
            @loaded="onLoaded"
            ref="pdf"
            class="file"
            :style="{ width: 100 * fileScale + '%' }"
          />
        </div>
        <div class="control-contiainer">
          <span @click="onFullScreen">{{ isFullScreen ? '窗口' : '全屏' }}</span>
          <span @click="scalePlus" class="btn" :class="{ hightlight: fileScale === 3 }">+</span>
          <span @click="scaleMinus" class="btn" :class="{ hightlight: fileScale === 1 }">-</span>
          <span class="scale-num">X{{ fileScale }}</span>
          <span @click="changePage(0)" class="btn" :class="{ hightlight: currentPage===1 }">上一页</span>
          {{ currentPage }} / {{ pageCount }}
          <span
            @click="changePage(1)"
            class="btn"
            :class="{ hightlight: currentPage===pageCount }"
          >下一页</span>
        </div>
      </section>
    </div>
</template>
<script>
import pdf from "vue-pdf";
import { mapState, mapMutations } from "vuex";

export default {
  name: "PdfInfo",
  components: { pdf},
  data() {
    return {
      currentPage: 0,
      pageCount: 0,
      fileScale: 1, // 文件预览大小比例
      isFullScreen: false // 是否全屏
    };
  },
  props: {
    pdfInfoVisiable: {
      require: true,
      default: false
    },
    fileName:{
      require:true
    }
  },
  computed: {
    ...mapState({
      multipage: state => state.setting.multipage,
      user: state => state.account.user
    }),
    resumename() {
      return `http://pqggd9642.bkt.clouddn.com/${this.user.resume}`;
    },
  },
  methods: {
    // 修复class为annotationLayer导致的大小不对，暂时不知这是不是BUG
    scaleFix() {
      setTimeout(() => {
        this.$refs.pdf.$refs.annotationLayer.style.width =
          this.$refs.pdf.$refs.canvas.offsetWidth + "px";
        this.$refs.pdf.$refs.annotationLayer.style.height =
          this.$refs.pdf.$refs.canvas.offsetHeight + "px";
        this.$refs.pdf.$refs.annotationLayer.style.transform = "initial";
      }, 800);
    },
    // 全屏窗口
    onFullScreen() {
      this.isFullScreen = !this.isFullScreen;
      this.scaleFix();
    },
    // 放大
    scalePlus() {
      if (this.fileScale >= 3) {
        return;
      }
      this.fileScale += 0.5;
      this.scaleFix();
    },
    // 缩小
    scaleMinus() {
      if (this.fileScale <= 1) {
        return;
      }
      this.fileScale -= 0.5;
      this.scaleFix();
    },
    // pdf加载完毕后
    onLoaded(event) {
      // 加载的时候先加载第一页
      this.currentPage = 1;
      this.scaleFix();
    },
    // 改变PDF页码
    changePage(val) {
      // val传过来区分上一页下一页的值，0上一页，1下一页
      if (val === 0 && this.currentPage > 1) {
        this.currentPage--;
      }
      if (val === 1 && this.currentPage < this.pageCount) {
        this.currentPage++;
      }
    }
  }
};
</script>
<style lang="less" scoped>
section.files-container {
  height: 500px;
  &.full-screen {
    position: fixed;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    z-index: 9999;
    background: #fff;
    width: auto;
    height: auto;
  }
  .file-container {
    height: 100%;
    overflow: scroll;
    -webkit-overflow-scrolling: touch;
    .file {
      height: 100%;
    }
  }
  .control-contiainer {
    position: absolute;
    top: 0;
    right: 0;
    left: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    background: rgba(255, 255, 255, 0.7);
    text-align: center;
    padding: 5px 0;
    font-size: 12px;
    color: rgba(255, 255, 255, 1);
    background: rgba(68, 154, 235, 0.7);
    > span {
      margin: 0 8px;
    }
    .scale-num {
      min-width: 26px;
    }
    .btn {
      &.hightlight {
        color: rgba(255, 255, 255, 0.3);
      }
    }
  }
}
</style>
