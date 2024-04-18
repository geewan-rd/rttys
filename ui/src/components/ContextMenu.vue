<template>
  <div ref="content" class="content" :style="{top: axis.y + 'px', left: axis.x + 'px',opacity: visibility ? 1 : 0}">
    <a v-for="item in menus" :key="item.name" @click="onMenuClick(item.name)"
       :style="{'text-decoration': item.underline ? 'underline' : 'none'}">
      {{item.caption || item.name}}
    </a>
  </div>
</template>

<script>
export default {
  name: 'ContextMenu',
  props: {
    menus: Array
  },
  data() {
    return {
      visibility: false,
      axis: {x: 0, y: 0}
    }
  },
  watch: {
    visibility(val) {
      if (!val)
        document.removeEventListener('mousedown', this.close);
    }
  },
  methods: {
    close(e) {
      const el = this.$refs.content;

      if (e.clientX >= this.axis.x && e.clientX <= this.axis.x + el.clientWidth &&
        e.clientY >= this.axis.y && e.clientY <= this.axis.y + el.clientHeight) {
        return;
      }

      this.visibility = false;
    },
    updated() {
      if (this.$refs.content) {
        const bw = document.body.offsetWidth;
        const bh = document.body.offsetHeight;
        const element = this.$refs.content;
        const width = element.offsetWidth;
        const height = element.offsetHeight;

        if (this.axis.x + width >= bw)
          this.axis.x = bw - width;

        if (this.axis.y + height >= bh)
          this.axis.y = bh - height;
      }
    },
    show(e) {
      document.addEventListener('mousedown', this.close);
      const rect = document.body.getBoundingClientRect();
      let left = e.clientX;
      let top = e.clientY;
      if (left + this.$refs.content.offsetWidth > rect.right) {
        left = rect.right - this.$refs.content.offsetWidth;
      }
      if (top + (this.$refs.content.offsetHeight) > rect.bottom) {
        top = rect.bottom - this.$refs.content.offsetHeight;
      }
      this.axis = {x: left, y: top};
      this.visibility = true;
    },
    onMenuClick(name) {
      this.visibility = false;
      this.$emit('click', name);
    }
  }
}
</script>

<style scoped>
  .content {
    position: fixed;
    z-index: 9999;
    background-color: #f9f9f9;
    min-width: 160px;
    box-shadow: 0px 8px 16px 0px rgba(0, 0, 0, 0.2);
  }

  .content a {
    color: black;
    padding: 5px 16px;
    text-decoration: none;
    display: block;
  }

  .content a:hover {
    background-color: #90C8F6;
    cursor: default;
  }
</style>
