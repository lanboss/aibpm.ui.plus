<template>
  <div class="ai-ou-select">
    <div class="tags">
      
  <el-button  icon="el-icon-plus" v-if="buttonType === 'button'" 
  v-show="selectedData.length===0"
  @click="show = true"
        type="primary"
    :circle="iconOnly">
          {{iconOnly?'':'添加'+title}}</el-button>
      
      
      <div class="input-box" :class="{'as-input': buttonType === 'input'}" v-if="showTags"  @click="show = true">
          <el-tag
            v-bind="tagConfig"
            class="org-tag"
            v-for="item in selectedData"
            :key="item.key"
            @close="onClose(item)">
            {{item.label}}
          </el-tag>
      </div>
      
    </div>
    <fc-org-transfer
      ref="transfer"
      :value="innerValue" 
      :title="title" 
      :searchable="searchable" 
      :maxNum="maxNum"
      :tabList="tabList"
      v-model:show="show"
      @confirm="onConfirm" />
  </div>
</template>
<script>
export default {
  model:{
    prop: 'value',
    event: 'input'
  },
  name:'ai-ou-select',
  props:{
    showTags:{type:Boolean,default:true},
    value:{
      type: Object | null,
      required: true
    },
    tabList: {
      type: Array,
      default: ()=>(['org'])
    },
    iconOnly:{type:Boolean,default:false},
    title: {
      type: String,
      default: '组织机构'
    },
    buttonType: {
      type: String,
      default: 'input'
    }, // or input
    searchable: {
      type: Boolean,
      default: true
    },
    maxNum: {
      type: Number,
      default: 99
    },
    tagConfig:{
      type: Object,
      default: () => ({
        type: 'info',
        closable: true,
        'disable-transitions': false,
        hit: false,
        color: undefined,
        size: 'small',
        effect: 'light'
      })
    },
  },
  data(){
    return {
      tabKeys:[],
      show: false,
      innerValue: null,
      selectedData: []
    }
  },
  watch:{
    value: {
      handler:function (val) {
        if(!val) return
        this.reloadCmpData()
      },
      immediate: true,
      deep: true
    },
    tabList: {
      handler:function (val) {
        this.tabKeys = []
        val.forEach(t => this.tabKeys.push(typeof t === 'string' ? t : t.key) )
        this.reloadCmpData()
      },
      immediate: true,
    }
  },
  computed:{
    selectedLabels(){
      return this.selectedData.map(t => t.label).join(',')
    }
  },
  methods:{
    reloadCmpData(){
      this.innerValue = {}
      this.tabKeys.forEach(key => {
        this.innerValue[key] = this.value && this.value[key] ? JSON.parse(JSON.stringify(this.value[key])) : []
      })
      // transfer 可能还未加载成功
      this.$nextTick(() => {
        this.initSelectedData()
      })
    },

    initSelectedData(){
      this.selectedData = this.tabKeys.reduce((res, key) => {
        return res.concat(this.innerValue[key].map(t => ({
          tabKey: key,
          key: this.getKey(t, key),
          label: this.getLabel(t, key)
        })))
      }, [])
    },

    getPropByKey(data, tabKey, key){
      const transfer = this.$refs['transfer']
      if(transfer){
        return transfer.getNodeProp(data, key, tabKey)
      }else{
        return ''
      }
    },

    getKey(data, tabKey){
      return this.getPropByKey(data, tabKey, 'nodeId')
    },

    getLabel(data, tabKey){
      return this.getPropByKey(data, tabKey, 'label')
    },

    onClose (item) {
      const list = this.innerValue[item.tabKey]
      const index = list.findIndex(t => this.getKey(t, item.tabKey) === item.key)
      index > -1 && list.splice(index, 1)
      this.initSelectedData()
      this.$emit('input', this.innerValue)
      this.$emit('confirm', this.innerValue)
    },
    
    onConfirm (data) {
      this.innerValue = data
      this.initSelectedData()
      this.$emit('input', this.innerValue)
      this.$emit('confirm', this.innerValue)
    }
  }
}
</script>
<style lang="scss" scoped>
.tags {
  .input-box.as-input{
    border: 1px solid #DCDFE6;
    padding-left: 6px;
    font-size: 12px;
    min-height: 27px;
    line-height: 25px;
    border-radius: 4px;
    background: white;
    color:  #606266;
    cursor:  pointer;
  }
  .org-tag{
    margin-right: 6px;
    max-width: 6rem;  
    overflow: hidden;
    text-overflow:  ellipsis;
    position : relative;
    padding-right:  1rem;
    vertical-align:  middle;

    :deep(.el-tag__close){
      position: absolute;
      right: 2px;
      top: 50%;
      margin-top: -7px;
    }
  }
}
</style>