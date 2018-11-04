<template>
  <div class="mpvue-picker">
    <div :class="{'mpvue-picker-mask':visible}" @click="cancel" catchtouchmove="true"></div>
    <div class="mpvue-picker-content" :class="{'mpvue-picker-view-show':visible}">
      <div class="mpvue-picker__hd" catchtouchmove="true">
        <div class="mpvue-picker__action" @click="cancel">取消</div>
        <div class="mpvue-picker__action" :style="{color: themeColor}" @click="confirm">确定</div>
      </div>
      <picker-view class="mpvue-picker-view" :style="{height: themeHeight}" :value="pickerValues" @change="change">
        <block>
          <picker-view-column v-for="(column, columnIndex) in pickerItems" :key="columnIndex" class="mpvue-picker-column">
            <div v-for="(item, index) in column" :key="index">{{item.label}}</div>
          </picker-view-column>
        </block>
      </picker-view>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      visible: false,
      pickerItems: [],
      pickerValues: this.values,
    };
  },
  props: {
    items: {
      type: Array,
      default: [],
    },
    values: {
      type: Array,
      default: [0],
    },
    linkage: {
      type: Boolean,
      default: false,
    },
    deep: {
      type: Number,
      default: 1,
    },
    themeColor: {
      type: String,
      default: '#1aad19',
    },
    themeHeight: {
      type: String,
      default: '240px',
    },
  },
  created() {
  },
  methods: {
    show() {
      this.pickerValues = [...this.values];
      this.setPickerItems();
      this.visible = true;
    },
    change(e) {
      if (this.linkage) {
        const eventValues = [...e.mp.detail.value];
        const tmpValues = [];
        let changeIndex = this.deep; // 列索引，记录是哪一列滚动
        for (let i = 0; i < this.deep; i += 1) {
          if (i > changeIndex) { // 滚动列以后的列，选项清零
            tmpValues.push(0);
          } else if (this.pickerValues[i] !== eventValues[i]) { // 记录滚动列
            tmpValues.push(eventValues[i]);
            changeIndex = i;
          } else { // 滚动列以前的列
            tmpValues.push(this.pickerValues[i]);
          }
        }
        this.pickerValues = tmpValues;
        this.setPickerItems();
      } else {
        this.pickerValues = e.mp.detail.value;
      }
      this.$emit('bind-change', this.pickerValues);
    },
    cancel() {
      this.visible = false;
      this.$emit('bind-cancel');
    },
    confirm() {
      const vo = this.getPickObject();
      this.visible = false;
      this.$emit('bind-confirm', vo);
    },
    setPickerItems() {
      if (this.linkage) {
        const tmpItems = [];
        let itemIndex = 0;
        let currentNodes = this.items;
        for (let i = 0; i < this.deep; i += 1) {
          let columnArray = [];
          if (currentNodes) {
            itemIndex = this.pickerValues[i];
            if (itemIndex >= currentNodes.length) {
              itemIndex = 0;
            }
            columnArray = currentNodes.map(itm => ({ value: itm.value, label: itm.label }));
            currentNodes = currentNodes[itemIndex].children;
          }
          tmpItems.push(columnArray);
        }
        this.pickerItems = tmpItems;
      } else {
        this.pickerItems = this.items;
      }
    },
    getPickObject() {
      const resultObj = {
        indexes: this.pickerValues,
        items: [],
        values: [],
        labels: [],
      };
      this.pickerItems.forEach((columnItems, columnIndex) => {
        const pickerValue = this.pickerValues[columnIndex];
        if (columnItems.length > this.pickerValues[columnIndex]) {
          resultObj.items.push(columnItems[pickerValue]);
          resultObj.values.push(columnItems[pickerValue].value);
          resultObj.labels.push(columnItems[pickerValue].label);
        }
      });
      return resultObj;
    },
  },
  computed: {
  },
};
</script>

<style lang="less" scoped>
  .mpvue-picker-mask {
    position: fixed;
    z-index: 1000;
    top: 0;
    right: 0;
    left: 0;
    bottom: 0;
    background: rgba(0, 0, 0, 0.6);
  }
  .mpvue-picker-content {
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
    transition: all 0.3s ease;
    transform: translateY(100%);
    z-index: 3000;
  }
  .mpvue-picker-view-show {
    transform: translateY(0);
  }
  .mpvue-picker__hd {
    display: flex;
    padding: 9px 15px;
    background-color: #fff;
    position: relative;
    text-align: center;
    font-size: 17px;
  }
  .mpvue-picker__hd:after {
    content: ' ';
    position: absolute;
    left: 0;
    bottom: 0;
    right: 0;
    height: 1px;
    border-bottom: 1px solid #e5e5e5;
    color: #e5e5e5;
    transform-origin: 0 100%;
    transform: scaleY(0.5);
  }
  .mpvue-picker__action {
    display: block;
    flex: 1;
    color: #1aad19;
  }
  .mpvue-picker__action:first-child {
    text-align: left;
    color: #888;
  }
  .mpvue-picker__action:last-child {
    text-align: right;
  }
  .mpvue-picker-view {
    position: relative;
    bottom: 0;
    left: 0;
    width: 100%;
    height: 200px;
    background-color: #FFFFFF;
  }
  .mpvue-picker-column {
    text-align: center;
    line-height: 32px;
    font-size: 16px;
  }
</style>
