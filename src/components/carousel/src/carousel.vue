<template>
  <div
    class="layui-carousel"
    :lay-anim="anim"
    :lay-indicator="indicator"
    :lay-arrow="arrow"
    :style="{
      height: height + 'px',
      width: width + 'px'
    }"
    @mouseenter="handleEnter"
    @mouseleave="handleLeave"
  >
    <div carousel-item="">
      <slot></slot>
    </div>
    <div
      class="layui-carousel-ind"
      :style="anim == 'updown' ? 'margin-top: -46px;' : ''"
    >
      <ul>
        <li
          :key="index"
          v-for="(item, index) in items"
          :class="{ 'layui-this': item.isActive }"
        ></li>
      </ul>
    </div>
    <button
      class="layui-icon layui-carousel-arrow"
      @click="handleSub"
      lay-type="sub"
    >
      <i v-if="anim == 'updown'" class="layui-icon layui-icon-up"></i>
      <i v-else class="layui-icon layui-icon-left"></i>
    </button>
    <button
      class="layui-icon layui-carousel-arrow"
      @click="handleAdd"
      lay-type="add"
    >
      <i v-if="anim == 'updown'" class="layui-icon layui-icon-down"></i>
      <i v-else class="layui-icon layui-icon-right"></i>
    </button>
  </div>
</template>

<script>
export default {
  name: "LayCarousel",
  data() {
    return {
      activeItem: "",
      nextItem: "",
      prevItem: "",
      items: [],
      righting: false,
      lefting: false,
      timer: ""
    };
  },
  props: {
    arrow: {
      type: String,
      default: () => "always"
    },
    height: {
      type: Number,
      default: () => 280
    },
    width: {
      type: Number,
      default: () => 600
    },
    anim: {
      type: String,
      default: () => "default"
    },
    indicator: {
      type: String,
      default: () => "inside"
    },
    autoplay: {
      type: Boolean,
      default: () => true
    },
    interval: {
      type: Number,
      default: () => 1500
    }
  },
  mounted() {
    this.play();
  },
  methods: {
    handleEnter() {
      this.stop();
    },
    handleLeave() {
      this.play();
    },
    handleSub() {
      if (this.lefting) {
        return false;
      }
      this.lefting = true;
      this.updateItem();
      setTimeout(() => {
        this.lefting = false;
        const activeIndex = this.items.findIndex(o => o === this.activeItem);
        if (activeIndex == 0) {
          this.setActiveItem(this.items[this.items.length - 1]);
          return false;
        }
        this.setActiveItem(this.items[activeIndex - 1]);
      }, 300);
    },
    handleAdd() {
      if (this.righting) {
        return false;
      }
      this.righting = true;
      this.updateItem();
      setTimeout(() => {
        this.righting = false;
        const activeIndex = this.items.findIndex(o => o === this.activeItem);
        if (activeIndex == this.items.length - 1) {
          this.setActiveItem(this.items[0]);
          return false;
        }
        this.setActiveItem(this.items[activeIndex + 1]);
      }, 300);
    },
    updateItems() {
      this.items = this.$children.filter(
        child => child.$options.name === "LayCarouselItem"
      );
      this.setActiveItem(this.items[0]);
    },
    updateItem() {
      this.items.forEach(o => {
        o.isActive = o == this.activeItem;
        o.isPrev = o == this.prevItem;
        o.isNext = o == this.nextItem;
        o.isRighting = this.righting;
        o.isLefting = this.lefting;
      });
    },
    setActiveItem(item) {
      this.activeItem = item;
      const activeIndex = this.items.findIndex(o => o === this.activeItem);

      this.nextItem =
        activeIndex == this.items.length - 1
          ? this.items[0]
          : this.items[activeIndex + 1];
      this.prevItem =
        activeIndex == 0
          ? this.items[this.items.length - 1]
          : this.items[activeIndex - 1];

      this.updateItem();
    },
    play() {
      if (this.autoplay) {
        if (this.timer) clearInterval(this.timer);
        this.timer = setInterval(() => {
          this.handleAdd();
        }, this.interval);
      } else {
        this.stop();
      }
    },
    stop() {
      if (this.autoplay) {
        if (this.timer) clearInterval(this.timer);
      }
    }
  },
  watch: {
    autoplay() {
      if (this.timer) clearInterval(this.timer);
      this.play();
    },
    interval() {
      if (this.timer) clearInterval(this.timer);
      this.play();
    }
  }
};
</script>

<style scoped></style>
