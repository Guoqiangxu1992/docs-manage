<template>
  <b-container fluid class="mt-3">
    <b-row align-h="center">
      <b-col cols="4">
        <b-alert :variant="alert.variant" :show="alert.show" dismissible @dismissed="alert.show=null">
          <b>{{alert.msg}}</b>
        </b-alert>
        <b-button variant="light" to="/docs"><span class="fa fa-arrow-left"> 返回</span></b-button>
        <b-card class="mt-3">
          <b-form @submit="submitEdit">
            <b-form-group id="project-name"
                          label="项目名"
                          lable-for="project-name-input"
                          description="项目名称是唯一的，不允许重复。">
              <b-form-input id="project-name-input"
                            type="text"
                            v-model="project.name"
                            required
                            placeholder="输入项目名称"></b-form-input>
            </b-form-group>
            <b-form-group id="project-description"
                          label="项目描述"
                          lable-for="project-description-input">
              <b-form-textarea id="project-description-input"
                               v-model="project.description"
                               :rows="3"
                               :max-rows="6"
                               placeholder="输入项目描述">
              </b-form-textarea>
            </b-form-group>
            <b-form-group id="project-sort-code"
                          label="排序码"
                          lable-for="project-sort-code-input"
                          description="越小越靠前">
              <b-form-input id="project-sort-code-input"
                            type="number"
                            v-model="project.sortCode"
                            required
                            :state="sortCodeState"
                            placeholder="输入排序码"></b-form-input>
              <b-form-feedback>排序吗必须大于0</b-form-feedback>
            </b-form-group>
            <b-button type="submit" variant="success">提交</b-button>
            <b-button type="reset" variant="secondary">重置</b-button>
          </b-form>
        </b-card>
      </b-col>
    </b-row>
  </b-container>
</template>

<script>
  import request from '../../utils/request'
  export default {
    data() {
      return {
        alert: {variant: 'info', msg: '', show: null},
        project: {}
      }
    },
    computed: {
      sortCodeState() {
        return this.project.sortCode > 0 ? null : 'invalid';
      }
    },
    created() {
      this.fetchData();
    },
    methods: {
      fetchData: function () {
        const projectId = parseInt(this.$route.params.id);
        const instance = this;
        request.Api.get('/projects/' + projectId).then(function (result) {
          if (result.code !== 200) {
            instance.alert = {variant: 'warning', msg: result.msg, show: 5};
          } else {
            instance.project = result.data;
          }
        }).catch(function (error) {
          instance.alert = {variant: 'danger', msg: '获取项目信息出错', show: 5};
        });
      },
      submitEdit: function () {
        const instance = this;
        request.Api.put('/projects/' + this.project.id, this.project).then(function (result) {
          if (result.code !== 200) {
            instance.alert = {variant: 'warning', msg: result.msg, show: 5};
          } else {
            instance.$router.push('/docs');
          }
        }).catch(function (error) {
          instance.alert = {variant: 'danger', msg: '编辑项目出错', show: 5};
        });
      }
    }
  }
</script>

<style>

</style>
