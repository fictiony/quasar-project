<template>
  <ZoomView
    ref="view"
    class="absolute-full"
    :class="`bg-grey-${$q.dark.isActive ? 9 : 4}`"
    :zoom.sync="viewZoom"
    :min-zoom="minViewZoom"
    :max-zoom="maxViewZoom"
    @mousedown.native.capture="onPress"
    @mousedown.native="$clearFocus"
    @mousemove.native="onMouseMove"
    @touchstart.native.capture="onPress"
    @touchstart.native="$clearFocus"
    v-touch-pan.mouse.preserveCursor="onDrag"
  >
    视图内容
  </ZoomView>
</template>

<script>
// 【主视图区】
import { mapGetters } from 'vuex'
import { mapStateRW } from 'boot/utils'
import key from 'keymaster'

export default {
  data: () => ({}),

  computed: {
    ...mapStateRW('main', ['viewZoom']),
    ...mapGetters('main', ['maxViewZoom', 'minViewZoom']),

    // 是否正在拖拽视图
    isDragging() {
      return !!(this.$refs.view && this.$refs.view.dragState)
    }
  },

  provide() {
    return {
      page: this
    }
  },

  methods: {
    // 按下处理
    onPress(e) {
      this.onMouseMove(e)
      if (key.isPressed(32)) return // 空格键按下时改为拖拽视图

      // 屏蔽视图拖拽
      this.$refs.view.$forceSet('interactive', false) // 若在模板中绑定该属性，会引发不必要的模板刷新
      setTimeout(() => {
        this.$refs.view.$forceSet('interactive', true)
      })

      // ...
    },

    // 拖拽处理
    onDrag(e) {
      this.onMouseMove(e.evt)
      // ...
    },

    // 鼠标移动处理
    onMouseMove(e) {
      e = e.touches ? e.touches[0] || e.changedTouches[0] : e // 触点放开时没有touches，因此要改用changedTouches
      // ...
    }
  },

  mounted() {
    if (process.env.DEBUGGING) {
      window.$m = this
    }

    // 绑定快捷键
    key.setScope('main')
  }
}
</script>
