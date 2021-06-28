<template>
  <div class="my--tabs">
    <div class="my--tabs-head" :style="headStyle">
      <ul class="nav-switch">
        <!-- <li
          v-for="(item, index) in list"
          :key="item.name"
          :class="[prev_index === index ? active_dir : '', value === item.name ? 'active ' + active_dir : '']"
          :style="{
            'z-index': index,
            left: '-' + (index * 30) + 'px',
            'background': list.length > 2 ? `rgb(237, 243, ${254 - index*20})` : 'rgb(237, 243, 254)'
          }"
        >
          <span class="link" @click="switchClick(item.name, index)">{{ item.label }}</span>
        </li> -->
        <li
          v-for="(item, index) in list"
          :key="item.name"
          :class="[prev_index === index ? active_dir : '', value === item.name ? 'active ' + active_dir : '']"
        >
          <span class="link" @click="switchClick(item.name, index)">{{ item.label }}</span>
        </li>
      </ul>
    </div>
    <div class="my--tabs-content" v-if="isexistChildren">
        <div
          class="content-item"
          v-for="item in list"
          :key="item.name"
        >
          <transition :name="value === item.name ? transitionDir : ''">
            <div v-show="value === item.name">
              <slot :name="item.name" />
            </div>
          </transition>
        </div>
    </div>
  </div>
</template>

<script>
export default {
  model: {
    prop: 'value',
    event: 'input'
  },
  props: {
    value: String,
    list: {
      type: Array,
      default() {
        return []
      }
    },
    dir: {
      type: String,
      default: 'left'
    }
  },
  data() {
    return {
      prev_index: 0,
      active_index: 0,
      active_dir: 'left' // left right
    }
  },
  computed: {
    headStyle() {
      const justify = {
        left: 'flex-start',
        right: 'flex-end',
        center: 'center'
      }

      return {
        display: 'flex',
        'justify-content': justify[this.dir]
      }
    },
    isexistChildren() {
      return Object.keys(this.$slots).length > 0
    },
    transitionDir() {
      return this.prev_index > this.active_index ? 'slide-left' : 'slide-right'
    }
  },
  methods: {
    switchClick(name, index) {
      // console.log('name', name)
      if (this.active_index === index) return

      this.active_dir = index > this.active_index ? 'left' : 'right'
      this.prev_index = this.active_index
      this.active_index = index

      this.$emit('input', name)
      this.$emit('change', name)
    }
  }
}
</script>

<style lang="scss" scoped>
.nav-switch {
  font-size: 0;
  li {
    display: inline-block;
    width: 160px;
    line-height: 40px;
    height: 40px;
    border-radius: 20px;
    font-size: 14px;
    text-align: center;
    background-color: #EDF3FE;
    // box-shadow: 0 0 1px rgb(88 93 102 / 50%);
    color: blue;
    position: relative;
    overflow: hidden;
    margin: 0 10px;
    // &:first-child, &:last-child {
    //   box-shadow: none;
    // }
    &::before {
      content: '';
      display: block;
      position: absolute;
      top: 0;
      height: 100%;
      width: 0;
      transition: width .5s;
      background-color: blue;
      border-radius: 20px;
    }
    &.left {
      &::before {
        right: 0;
      }
    }
    &.right {
      &::before {
        left: 0;
      }
    }

    .link {
      display: block;
      color: blue;
      cursor: pointer;
      position: relative;
      z-index: 11;
      transition: color .5s;
    }
    &.active {
      // background-color: blue !important;
      z-index: 10 !important;
      &.left {
        &::before {
          right: auto;
          left: 0;
          width: 100%;
        }
      }
      &.right {
        &::before {
          left: auto;
          right: 0;
          width: 100%;
        }
      }
      .link {
        color: #fff;
      }
    }
  }
}
.my--tabs-content {
  padding: 30px 0;
  width: 100%;
  overflow: hidden;
}


/*路由切换 animation start */

.slide-left-enter-active,
.slide-left-leave-active {
  transition: 0.5s transform ease;
  backface-visibility: hidden;
  perspective: 1000;
}
.slide-left-enter {
  transform: translate3d(-100%, 0, 0);
}
.slide-left-enter-to,
.slide-left-leave {
  transform: translate3d(0%, 0, 0);
}
.slide-left-leave-to {
  transform: translate3d(100%, 0, 0);
}

.slide-right-enter-active,
.slide-right-leave-active {
  transition: 0.3s all ease-out;
  backface-visibility: hidden;
  perspective: 1000;
}
.slide-right-enter {
  transform: translate3d(100%, 0, 0);
}
.slide-right-enter-to,
.slide-right-leave {
  transform: translate3d(0, 0, 0);
}
.slide-right-leave-to {
  transform: translate3d(-100%, 0, 0);
}
</style>
