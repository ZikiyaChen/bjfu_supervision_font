<!--
 * @Description: In User Settings Edit
 * @Author: your name
 * @Date: 2019-09-21 10:05:09
 * @LastEditTime: 2019-10-09 18:40:00
 * @LastEditors: Please set LastEditors
 -->
<template>
  <div>
    <Form
      :model="value"
      :rules="rules"
      ref="lesson_info_form"
    >
      <Row :gutter="16">
        <Col :sm="24" :md="12" :lg="4">
          <FormItem label="章节目录" :required="true" prop="lesson.content">
            <Input v-model="value.lesson.content" class="inline-form-item" :disabled="disabled"></Input>
          </FormItem>
        </Col>
        <Col :sm="24" :md="12" :lg="4">
          <FormItem label="督导工号" :required="true" v-role="['管理员','大组长','小组长','督导']">
            <Select
              :value="value.guider"
              filterable
              clearable
              remote
              :label="value.guider"
              :loading="false"
              @on-query-change="onGuiderQueryChange"
              @on-change="onGuiderSelectChange"
              :disabled="guider_disable || disabled"
              class="inline-form-item"
            >
              <Option
                v-for="(item, key) in users"
                :value="item.username"
                :key="item.username + item.id"
              >{{"'" + item.username + "'"}}
              </Option>
            </Select>
          </FormItem>
        </Col>
        <Col :sm="24" :md="12" :lg="4">
          <FormItem label="督导姓名" v-role="['管理员','大组长','小组长','督导']">
            <Select
              :value="value.guider_name"
              :placement="'bottom'"
              :label="value.guider_name"
              @on-change="onGuiderSelectChange"
              @on-query-change="onGuiderQueryChange"
              class="inline-form-item"
              :disabled="disabled || guider_disable"
            >
              <Option
                v-for="(item, key) in users"
                :value="item.name"
                :key="item.name + item.id"
              >{{item.name}}
              </Option>
            </Select>
          </FormItem>
        </Col>
        <Col :sm="24" :md="12" :lg="4">
          <FormItem label="听课学期">
            <Select
              :placement="'bottom'"
              v-model="value.term"
              @on-change="onTermSelectChange"
              class="inline-form-item"
              :disabled="disabled || disabled_term"
            >
              <Option v-for="item in terms" :value="item.name" :key="item.name">{{ item.name }}</Option>
            </Select>
          </FormItem>
        </Col>
        <Col :sm="24" :md="12" :lg="4">
          <FormItem label="听课类型" prop="lesson.lesson_level">
            <Input v-model="value.lesson.lesson_level" disabled class="inline-form-item"></Input>
          </FormItem>
        </Col>
      </Row>

      <Row>
        <Col :sm="24" :md="8" :lg="4">
          <table class="table-border meta-table">
            <tbody>
            <tr>
              <th>课程名字</th>
            </tr>
            <tr>
              <td>
                <FormItem :required="true" class="table-form-item">
                  <Select
                    placeholder="请输入或选择课程"
                    v-model="value.lesson.lesson_id"
                    :remote="true"
                    @on-query-change="onLessonQueryChange"
                    @on-clear="onLessonClear"
                    clearable
                    :label="value.lesson.lesson_name"
                    @on-change="(v)=>{onSelectedLessonChange(v, undefined, true)}"
                    :disabled="disabled"
                    filterable
                  >
                    <Option
                      v-for="(item,index) in lessons"
                      :value="item.lesson_id"
                      :key="item.lesson_id + item.lesson_name + item.id"
                    >{{ "'"+item.lesson_name+'___' + item.lesson_teacher_name+ '___'+item.lesson_class+'___'+"'"}}
                    </Option>
                  </Select>
                </FormItem>
              </td>
            </tr>
            </tbody>
          </table>
        </Col>
        <Col :sm="24" :md="8" :lg="4">
          <table class="table-border meta-table">
            <tbody>
            <tr>
              <th>任课教师</th>
            </tr>
            <tr>
              <td>
                <FormItem :required="true" class="table-form-item">
                  <Input v-model="value.lesson.lesson_teacher_name" :disabled="disabled"></Input>
                </FormItem>
              </td>
            </tr>
            </tbody>
          </table>
        </Col>

        <Col :sm="24" :md="8" :lg="4">
          <table class="table-border meta-table">
            <tbody>
            <tr>
              <th>上课班级</th>
            </tr>
            <tr>
              <td>
                <FormItem :required="true" class="table-form-item" prop="lesson.lesson_class" >
                  <Input v-model="value.lesson.lesson_class" :disabled="disabled"></Input>
                </FormItem>
              </td>
            </tr>
            </tbody>
          </table>
        </Col>
        <Col :sm="24" :md="8" :lg="4">
          <table class="table-border meta-table">
            <tbody>
            <tr>
              <th>听课时间</th>
            </tr>
            <tr>
              <td>
                <FormItem :required="true" class="table-form-item" prop="lesson.lesson_date">
                  <DatePicker
                    width="100%"
                    type="date"
                    :value="value.lesson.lesson_date"
                    format="yyyy-MM-dd"
                    @on-change="(v)=>{onSelectedLessonCaseChange(v, true)}"
                    :options="getLessonDatePickerOption()"
                    :disabled="disabled"
                    placement="bottom"
                  ></DatePicker>
                </FormItem>
              </td>
            </tr>
            </tbody>
          </table>
        </Col>
        <Col :sm="24" :md="8" :lg="4">
          <table class="table-border meta-table">
            <tbody>
            <tr>
              <th>上课地点</th>
            </tr>
            <tr>
              <td>
                <FormItem :required="true" class="table-form-item" prop="lesson.lesson_room">
                  <Input v-model="value.lesson.lesson_room" :disabled="disabled"></Input>
                </FormItem>
              </td>
            </tr>
            </tbody>
          </table>
        </Col>
        <Col :sm="24" :md="8" :lg="4">
          <table class="table-border meta-table">
            <tbody>
            <tr>
              <th>上课节次</th>
            </tr>
            <tr>
              <td>
                <FormItem :required="true" class="table-form-item" prop="lesson.lesson_times">
                  <Select v-model="value.lesson.lesson_times" multiple :disabled="disabled" >
                    <OptionGroup label="建议选择">
                      <Option
                        v-for="item in lesson_times"
                        :value="item.value"
                        :key="item.value + item.key"
                      >{{ item.label }}
                      </Option>
                    </OptionGroup>
                    <OptionGroup label="其他可选">
                      <Option
                        v-for="item in ALL_CASES"
                        v-if="!lesson_times.map((i)=>{
                          return i.value
                        }).includes(item.value)"
                        :value="item.value"
                        :key="item.value + item.key"
                      >{{ item.label }}
                      </Option>
                    </OptionGroup>
                  </Select>
                </FormItem>
              </td>
            </tr>
            </tbody>
          </table>
        </Col>
      </Row>

    </Form>
  </div>
</template>
<script>
import { queryLessons, getLesson, queryLessonCase } from '@/service/api/lesson'
import { querySupervisors } from '@/service/api/user'
import { dateToString } from 'Libs/tools'
import { queryTerms, getCurrentTerms } from '@/service/api/term'
import { transTimeToSelectedData } from 'Libs/tools'

export default {
  props: {
    value: {
      default: { lesson: {} }
    },
    input: Function,
    disabled: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      ALL_CASE_STR: '01020304050607080910111213',
      lessons: {},
      users: {},
      lesson_times: [],
      allow_select_data: [],
      terms: [],
      selected_lesson: { lesson_cases: [] },
      select_guider: {},
      selected_lesson_case: {},
      lesson_disabled: false, // 禁用课程的表单,
      guider_disable: false,
      guider_name_like: '',
      user_name_like: '',
      disabled_term: true,
      rules: {
        'lesson.content': [
          { required: true, message: '请输入章节名称', trigger: 'blur' }
        ],
        'lesson.lesson_class': [
          { required: true, message: '班级不能为空', trigger: 'blur' }
        ],
        'lesson.lesson_date': [
          { required: true, message: '日期不能为空', trigger: 'blur' }
        ],
        'lesson.lesson_times': [
          {
            validator (rule, value, callback) {
              if (value.length <= 0) {
                return callback(new Error('课程节次不能为空'))
              } else {
                callback()
              }
            }
          }
        ]
      }
    }
  },
  computed: {
    currentUser: function () {
      return this.$store.getters.userInfo
    },
    lessonInfoForm: function () {
      return this.$refs.lesson_info_form
    },
    ALL_CASES: function () {
      return transTimeToSelectedData(this.ALL_CASE_STR)
    }
  },
  watch: {
    value: {
      deep: true,
      handler: function (val, oldVal) {
        this.$emit('input', this.value)
      }
    },
    'value.lesson.lesson_id': {
      handler: function (val, oldVal) {
        if (this.value.lesson.lesson_id) {
          getLesson(this.value.lesson.lesson_id).then(resp => {
            // 读取课程
            let selected_lesson = resp.data.lesson
            // 处理case
            this.lessons[selected_lesson.lesson_id] = selected_lesson
            this.onSelectedLessonChange(selected_lesson.lesson_id, this.value.lesson.lesson_date, false)
            // 处理表的附加值
          })
        }
      }
    }
  },
  mounted () {
    // 处理当前用户
    if (!this.currentUser.role_names.includes('管理员')) {
      this.disabled_term = true
      this.guider_disable = true
    }
    queryTerms()
      .then(resp => {
        this.terms = resp.data.terms
      })
      .then(() => {
        let lesson_id = this.$route.query.lesson_id
        getCurrentTerms().then(resp => {
          this.value.term = resp.data.term.name
          if (lesson_id) {
            this.value.lesson.lesson_id = lesson_id
            getLesson(this.value.lesson.lesson_id).then(resp => {
              // 读取课程
              let selected_lesson = resp.data.lesson
              // 处理case
              this.lessons[selected_lesson.lesson_id] = selected_lesson
              this.onSelectedLessonChange(selected_lesson.lesson_id, this.value.lesson.lesson_date, false)
              // 处理表的附加值
            })
          } else {
            this.fetchLesson()
          }
          if (this.guider_disable || this.disabled) {
            this.$set(this.users, this.value.guider, {
              username: this.value.guider,
              name: this.value.guider_name
            })
          } else {
            this.fetchUser()
          }
        })
      })
    if (
      this.currentUser.guider &&
        !this.currentUser.role_names.includes('管理员')
    ) {
      this.value.guider = this.currentUser.username
      this.value.guider_name = this.currentUser.name
      this.value.guider_group = this.currentUser.guider.group_name
    }
  },
  methods: {
    // 获取所有用户
    validate: function (f) {
      return this.lessonInfoForm.validate(f)
    },
    fetchUser: function () {
      this.users = {}
      return querySupervisors({
        term: this.value.term,
        username_like: this.guider_name_like
      }).then(resp => {
        resp.data.supervisors.map(item => {
          this.$set(this.users, item.username, item)
        })
      })
    },

    // 获取所有课程
    fetchLesson: function () {
      this.lessons = {}
      return queryLessons({
        term: this.value.term,
        lesson_or_teacher_name_or: this.lesson_name_like
      }).then(resp => {
        resp.data.lessons.map(item => {
          this.$set(this.lessons, item.lesson_id, item)
        })
      })
    },

    restValue: function () {
      this.value.lesson = {}
    },

    onGuiderQueryChange: function (value) {
      if (!(value.startsWith("'") && value.endsWith("'"))) {
        this.guider_name_like = value
        this.fetchUser()
      }
    },

    onLessonQueryChange: function (value) {
      if (!(value.startsWith("'") && value.endsWith("'"))) {
        this.lesson_name_like = value
        this.fetchLesson()
      }
    },

    onGuiderSelectChange: function (value) {
      this.value.guider = value
      if (value) {
        this.select_guider = this.users[value]
        if (this.select_guider) {
          this.value.guider_name = this.select_guider.user.name
          this.value.guider_group = this.select_guider.group_name
        }
      } else {
        this.value.guider_name = undefined
        this.value.guider_group = undefined
      }
    },

    onTermSelectChange: function (value) {
      this.fetchLesson().then(() => {
        this.restValue()
      })
      this.fetchUser()
    },

    onSelectedLessonChange: function (lesson_id, assign_case_date, clear_case) {
      /* 选择的课程发生变化 assing_case_date*/
      if (lesson_id) {
        this.selected_lesson = this.lessons[lesson_id]
        this.selected_lesson.lesson_cases = []
        queryLessonCase({ lesson_id: this.selected_lesson.id ,term:this.value.term}).then(resp => {
          // 拿到所有case
          this.selected_lesson.lesson_cases = resp.data.lesson_cases
          this.allow_select_data = this.selected_lesson.lesson_cases.map(
            item => {
              return item.lesson_date
            }
          )


          // 判断是否有指定的日期
          if (!assign_case_date) {
            this.value.lesson.lesson_times = []
            if (this.allow_select_data.length > 0) {
              this.onSelectedLessonCaseChange(this.allow_select_data[0], clear_case)
            } else {
              this.onSelectedLessonCaseChange(undefined, clear_case)
            }
          } else {
            // 如果有，不清空
            this.onSelectedLessonCaseChange(assign_case_date, clear_case)
          }
        })
      } else {
        this.selected_lesson = { lesson_cases: [] }
      } // 查看选的那个
      this.value.lesson = {
        ...this.value.lesson,
        lesson_id: this.selected_lesson.lesson_id,
        raw_lesson_id: this.selected_lesson.raw_lesson_id,
        lesson_name: this.selected_lesson.lesson_name,
        lesson_teacher_name: this.selected_lesson.lesson_teacher_name,
        lesson_class: this.selected_lesson.lesson_class,
        lesson_teacher_unit: this.selected_lesson.lesson_teacher_unit,
        lesson_unit: this.selected_lesson.lesson_unit,
        lesson_attribute: this.selected_lesson.lesson_attribute,
        group_name: this.selected_lesson.group_name,
        lesson_year: this.selected_lesson.lesson_year,
        lesson_semester: this.selected_lesson.lesson_semester,
        lesson_level: this.selected_lesson.lesson_level,
        lesson_model: this.selected_lesson.lesson_model,
        is_lock: this.selected_lesson.is_lock,
        guiders: this.selected_lesson.guiders
      }
    },
    onLessonClear: function () {
      this.selected_lesson = { lesson_cases: [] }
      this.value.lesson = { lesson_times: [] }
    },
    onSelectedLessonCaseChange: function (value, clear) {
      if (!clear) {
        // 如果不清 什么都不做
      } else {
        // 如果清除 ，按逻辑来
        this.value.lesson.lesson_times = []
        if (value) {
          /* 选择的课程case变化 根据时间 */
          this.value.lesson.lesson_date = value
          let flag = this.selected_lesson.lesson_cases.findIndex(item => {
            return item.lesson_date === value
          })
          if (flag !== -1) {
            // 上课时间选中
            this.lesson_times = transTimeToSelectedData(
              this.selected_lesson.lesson_cases[flag].lesson_time
            )
            this.selected_lesson_case = this.selected_lesson.lesson_cases[flag]
          } else {
            this.lesson_times = []
            this.selected_lesson_case = {}
          }
          this.value.lesson = {
            ...this.value.lesson,
            lesson_date: value,
            lesson_room: this.selected_lesson_case.lesson_room,
            lesson_times: this.value.lesson.lesson_times
              ? this.value.lesson.lesson_times
              : []
          }
        } else {
          // 没找到不选
          this.lesson_times = []
          this.selected_lesson_case = {}
          this.value.lesson = {
            ...this.value.lesson,
            lesson_date: undefined,
            lesson_room: undefined,
            lesson_times: []
          }
        }
      }
    },

    getLessonDatePickerOption: function () {
      let self = this
      return {
        disabledDate (value) {
          if (self.allow_select_data.length) {
            let d = dateToString(value, 'yyyy-MM-dd')
            let flag = self.allow_select_data.findIndex(item => {
              return item === d
            })
            return flag === -1
          } else {
            return false
          }
        }
      }
    }
  },
  created () {
  }
}
</script>

<style scoped lang="less">
  .table-border {
    border: 1px solid #ddd;
    border-collapse: collapse;
    border-spacing: 0;
    tbody > tr {
      text-align: left;
      border: 1px solid #ddd;
    }
    tbody > tr > th {
      padding: 8px;
      border: 1px solid #ddd;
    }
    tbody > tr > td {
      padding: 8px;
      border: 1px solid #ddd;
      height: 30px;
    }
    .table-form-item {
      width: 160px;
      vertical-align: middle;
      margin-bottom: 3px;
    }
  }
  .ivu-form-item-content{
    margin-right: -20px
  }
  .meta-table {
    width: 100%;
    td {
      height: 30px;
    }
  }

  .inline-form-item {
    width: 184px;
  }
</style>
