<template>
  <div class="container">
    <div>{{pickerText}}</div>
    <button @click="handleShowSinglePicker">单列选择</button>
    <button @click="handleShowMultiPicker">多列选择</button>
    <button @click="handleShowLinkagePicker">联动选择</button>
    <mpvue-picker ref="genderPicker" :items="genders" :values="singleValues" themeColor="#FF0000" @bind-confirm="confirmSingle"></mpvue-picker>
    <mpvue-picker ref="multiPicker" :items="multiArray" :values="multiValues" themeColor="#FF0000" @bind-confirm="confirmMulti"></mpvue-picker>
    <mpvue-picker ref="linkagePicker" :items="linkageArray" :linkage="true" :deep="3" :values="linkageValues" themeColor="#FF0000" @bind-confirm="confirmLinkage"></mpvue-picker>
  </div>
</template>

<script>
  import MpvuePicker from '@/components/MpvuePicker';

  const regions = require('../../dicts/regions.json');

  export default {
    components: {
      MpvuePicker,
    },
    data() {
      return {
        singleValues: [0],
        multiValues: [0, 0, 0],
        linkageValues: [0, 0, 0],
        pickerText: '',
        genders: [
          [
            { value: 1, label: '男' },
            { value: 2, label: '女' },
          ],
        ],
        multiArray: [
          [
            { value: 1, label: '男' },
            { value: 2, label: '女' },
          ],
          [
            { value: 1, label: 'A' },
            { value: 2, label: 'B' },
            { value: 3, label: 'C' },
            { value: 4, label: 'D' },
            { value: 5, label: 'E' },
            { value: 6, label: 'F' },
          ],
          [
            { value: 1, label: '甲' },
            { value: 2, label: '乙' },
            { value: 2, label: '丙' },
            { value: 2, label: '丁' },
            { value: 2, label: '戊' },
            { value: 2, label: '己' },
            { value: 2, label: '庚' },
            { value: 2, label: '辛' },
          ],
        ],
        linkageArray: regions,
      };
    },
    onShow() {
      console.log(regions);
    },
    methods: {
      handleShowSinglePicker() {
        this.$refs.genderPicker.show();
      },
      handleShowMultiPicker() {
        this.$refs.multiPicker.show();
      },
      handleShowLinkagePicker() {
        this.$refs.linkagePicker.show();
      },
      confirmSingle(e) {
        console.log('picker confirm emit');
        console.dir(e);
        this.singleValues = e.indexes;
        console.log(this.singleValues);
        this.pickerText = e.labels.join(' - ');
      },
      confirmMulti(e) {
        console.log('picker confirm emit');
        console.dir(e);
        this.multiValues = e.indexes;
        console.log(this.multiValues);
        this.pickerText = e.labels.join(' - ');
      },
      confirmLinkage(e) {
        console.log('picker confirm emit');
        console.dir(e);
        this.linkageValues = e.indexes;
        console.log(this.linkageValues);
        this.pickerText = e.labels.join(' - ');
      },
    },
    created() {
    },
  };
</script>

<style scoped>

</style>
