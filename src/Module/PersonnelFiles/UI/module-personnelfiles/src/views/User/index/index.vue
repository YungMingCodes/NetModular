<template>
  <nm-container>
    <nm-list ref="list" v-bind="list">
      <!--查询条件-->
      <template v-slot:querybar>
        <el-form-item label="姓名：" prop="name">
          <el-input v-model="list.model.name" clearable/>
        </el-form-item>
        <el-form-item label="工号：" prop="number">
          <el-input v-model="list.model.number" clearable/>
        </el-form-item>
      </template>

      <!--按钮-->
      <template v-slot:querybar-buttons>
        <nm-button type="success" :text="buttons.add.text" :icon="buttons.add.icon" @click="add" v-nm-has="buttons.add"/>
      </template>

      <template v-slot:col-name="{row}">
        <nm-button type="text" :text="row.name" @click="details(row)"/>
      </template>

      <template v-slot:col-birthday="{row}">{{$dayjs(row.birthday).format('YYYY年MM月DD日')}}</template>

      <template v-slot:col-picture="{row}">
        <nm-button v-if="row.picture" type="text" text="查看" @click="pictureShow(row)"/>
        <span v-else>无</span>
      </template>

      <!--操作列-->
      <template v-slot:col-operation="{row}">
        <nm-button :text="buttons.edit.text" :icon="buttons.edit.icon" type="text" @click="edit(row)" v-nm-has="buttons.edit"/>
        <nm-button :text="buttons.workHistory.text" :icon="buttons.workHistory.icon" type="text" @click="workHistory(row)" v-nm-has="buttons.workHistory"/>
        <nm-button :text="buttons.educationHistory.text" :icon="buttons.educationHistory.icon" type="text" @click="educationHistory(row)" v-nm-has="buttons.educationHistory"/>
        <nm-button-delete :id="row.id" :action="removeAction" @success="refresh" v-nm-has="buttons.del"/>
      </template>
    </nm-list>

    <!--添加-->
    <add-page :visible.sync="dialog.add" @success="refresh"/>
    <!--编辑-->
    <edit-page :id="curr.id" :visible.sync="dialog.edit" @success="refresh"/>
    <!--详情页-->
    <details-page :user="curr" :visible.sync="dialog.details"/>
    <!--照片预览-->
    <picture-show :user="curr" :visible.sync="dialog.pictureShow"/>
    <!--工作经历-->
    <work-history :user="curr" :visible.sync="dialog.workHistory"/>
    <!--教育经历-->
    <education-history :user="curr" :visible.sync="dialog.educationHistory"/>
  </nm-container>
</template>
<script>
import api from '../../../api/User'
import page from './page'
import cols from './cols'
import AddPage from '../components/add'
import EditPage from '../components/edit'
import DetailsPage from '../components/details'
import PictureShow from '../components/picture-show'
import WorkHistory from '../../UserWorkHistory/index'
import EducationHistory from '../../UserEducationHistory/index'
export default {
  name: page.name,
  components: { AddPage, EditPage, DetailsPage, PictureShow, WorkHistory, EducationHistory },
  data() {
    return {
      curr: { id: '', name: '' },
      list: {
        title: page.title,
        icon: 'user',
        operationWidth: '280',
        cols,
        action: api.query,
        model: {
          /** 姓名 */
          name: '',
          /** 工号 */
          number: ''
        }
      },
      removeAction: api.remove,
      dialog: {
        add: false,
        edit: false,
        details: false,
        pictureShow: false,
        workHistory: false,
        educationHistory: false
      },
      buttons: page.buttons
    }
  },
  methods: {
    refresh() {
      this.$refs.list.refresh()
    },
    add() {
      this.dialog.add = true
    },
    edit(row) {
      this.curr = row
      this.dialog.edit = true
    },
    details(row) {
      this.curr = row
      this.dialog.details = true
    },
    pictureShow(row) {
      this.curr = row
      this.dialog.pictureShow = true
    },
    workHistory(row) {
      this.curr = row
      this.dialog.workHistory = true
    },
    educationHistory(row) {
      this.curr = row
      this.dialog.educationHistory = true
    }
  }
}
</script>
