<template>
  <div class="formPageWrapper">
    <van-form @submit="onSubmit" class="formContent">
      <div v-for="(item, itemIndex) in list" :key="item.registerType + itemIndex">
        <!-- 文本输入框 -->
        <van-field
          v-if="item.registerType === 'text'"
          v-model="formData[item.itemId]"
          :name="item.itemId"
          :label="item.registerName"
          placeholder="请输入"
          :rules="[{ required: item.isRequired, message: '请输入' }]"
        />
        <!-- 数字输入框 -->
        <van-field
          v-if="item.registerType === 'number'"
          type="number"
          v-model="formData[item.itemId]"
          :name="item.itemId"
          :label="item.registerName"
          placeholder="请输入"
          :rules="[{ required: item.isRequired, message: '请输入' }]"
        />
        <!-- 日期选择 -->
        <van-field
          v-if="item.registerType === 'date'"
          readonly
          clickable
          :name="item.itemId"
          :value="formData[item.itemId]"
          :label="item.registerName"
          placeholder="请选择"
          @click="openCalender(item.itemId)"
        />
        <!-- 时间选择 -->
        <van-field
          v-if="item.registerType === 'time'"
          readonly
          clickable
          :name="item.itemId"
          :value="formData[item.itemId]"
          :label="item.registerName"
          placeholder="请选择"
          @click="openDateTime(item.itemId)"
        />
        <!-- 下拉选择 -->
        <van-field
          v-if="item.registerType === 'select'"
          readonly
          clickable
          :name="item.itemId"
          :value="formData[item.itemId]"
          :label="item.registerName"
          placeholder="请选择"
          @click="openSelect(item, item.itemId)"
        />
      </div>
      <div style="margin: 16px;">
        <van-button round block type="info" native-type="submit">提交</van-button>
      </div>
    </van-form>
    <van-calendar v-model="showCalendar" @confirm="calenderConfirm" :min-date="minDate" :max-date="maxDate" />
    <van-popup v-model="showDateTime" position="bottom">
      <van-datetime-picker
        type="time"
        :min-hour="9"
        :max-hour="17"
        @confirm="dateTimeConfirm"
        @cancel="showDateTime = false"
      />
    </van-popup>
    <van-popup v-model="showSelect" position="bottom">
      <van-picker
        show-toolbar
        :columns="selectList"
        :default-index="selectIndex"
        @confirm="selectConfirm"
        @cancel="showSelect = false"
      />
    </van-popup>
  </div>
</template>

<script>
export default {
  data() {
    return {
      showCalendar: false,
      showDateTime: false,
      showSelect: false,
      selectList: [],
      selectIndex: 0,
      minDate: new Date(),
      maxDate: new Date(),
      formData: {},
      list: [
        {
          registerName: '文本输入',
          registerType: 'text',
          isRequired: true,
          originData: ''
        },
        {
          registerName: '数字输入',
          registerType: 'number',
          isRequired: false,
          originData: ''
        },
        {
          registerName: '日期选择',
          registerType: 'date',
          isRequired: false,
          originData: 'ALL'
        },
        {
          registerName: '时间选择',
          registerType: 'time',
          isRequired: false,
          originData: ''
        },
        {
          registerName: '下拉选择',
          registerType: 'select',
          isRequired: false,
          originData: [
            {
              text: '选择1',
              value: '选择1'
            },
            {
              text: '选择2',
              value: '选择2'
            }
          ]
        }
      ]
    }
  },
  mounted() {
    this.list = this.list.map((item, index) => {
      this.$set(item, 'itemId', item.registerType + index)
      return item
    })
    this.list.forEach(item => {
      this.$set(this.formData, item.itemId, '')
    })
    // todo 禁用的详细日期
    // 设置日期可选范围 当前之后30天
    this.maxDate = new Date(this.minDate.getFullYear(), this.minDate.getMonth(), 30)
    console.log('初始化this.list: ', this.list);
    console.log('初始化this.formData: ', this.formData);
  },
  methods: {
    openCalender(itemId) {
      this.currentItemId = itemId
      this.showCalendar = true
    },
    calenderConfirm(date) {
      this.formData[this.currentItemId] = this.dateFormat(date)
      this.showCalendar = false
    },
    // 时间格式化
    dateFormat(date) {
      let result = ''
      let month = date.getMonth() + 1
      if (month < 10) {
        month = '0' + month
      }
      let day = date.getDate()
      if (day < 10) {
        day = '0' + day
      }
      result = `${date.getFullYear()}-${month}-${day}`
      return result
    },
    openDateTime(itemId) {
      this.currentItemId = itemId
      this.showDateTime = true
    },
    dateTimeConfirm(val) {
      this.formData[this.currentItemId] = val
      this.showDateTime = false
    },
    openSelect(item, itemId) {
      this.currentItemId = itemId
      this.selectList = item.originData || []
      this.selectIndex = this.selectList.findIndex(item => item.value === this.formData[this.currentItemId]) || 0
      this.showSelect = true
    },
    selectConfirm(val) {
      this.formData[this.currentItemId] = val.value
      this.showSelect = false
    },
    onSubmit() {
      console.log('this.formData11111: ', this.formData)
    }
  }
}
</script>

<style lang="scss" scoped>
.formPageWrapper {
  padding: 16px 8px;
  background-color: #f5f5f5;
  .formContent {
    background-color: #fff;
    border-radius: 4px;
    padding: 8px;
  }
}
</style>
