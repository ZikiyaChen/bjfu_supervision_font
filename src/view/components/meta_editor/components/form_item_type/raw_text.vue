
<template>
  <div>
    <!--<p>题目：{{this.qsItem.item_name}}</p>-->
    <!--<p>选项:{{this.qsItem.payload}}</p>-->
    <!--<Button type="primary" @click="modal1 = true">文本题</Button>-->

      <Form :label-width="80" style="width: 400px">
        <FormItem label="题目标题:">
          <Row>
            <Col span="18">
              <Input v-model="qsItem.title" placeholder="将在问听课评价表上展示 可重复"></Input>
            </Col>
          </Row>
        </FormItem>
        <FormItem label="题目名称:">
          <Row>
            <Col span="18">
              <Input v-model="qsItem.item_name" placeholder="用于检索, 请保证全局唯一"></Input>

            </Col>
          </Row>
        </FormItem>
      </Form>
    <Rule v-model="rules" :types="['required', 'length']"></Rule>
  </div>
</template>
<script>
// import
import Rule from '../rules'

export default {
  name: 'raw_text.vue',
  components: { Rule },
  watch: {
    qsItem: {
      handler: function (val) {
        this.ok()
      },
      deep: true
    },
    rules: {
      deep: true,
      handler: function () {
        this.ok()
      }
    }
  },
  data () {
    return {
      qsItem: {
        item_name: '',
        title: '',
        type: 'form_item',
        item_type: 'raw_text'
      },
      rules: []
    }
  },
  props: {
    onInput: Function
  },
  methods: {
    ok () {
      this.$emit('onInput', { ...this.qsItem, payload: { rules: this.rules } })
    }
  }
}
</script>
