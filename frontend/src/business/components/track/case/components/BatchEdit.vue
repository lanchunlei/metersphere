<template>
  <div>
    <el-dialog
      :title="dialogTitle"
      :visible.sync="dialogVisible"
      width="25%"
      class="batch-edit-dialog"
      :destroy-on-close="true"
      @close="handleClose"
    >
      <el-form :model="form" label-position="right" label-width="150px" size="medium" ref="form" :rules="rules">
        <el-form-item :label="$t('test_track.case.batch_update', [size])" prop="type">
          <el-select v-model="form.type" style="width: 80%" @change="changeType">
            <el-option v-for="(type, index) in typeArr" :key="index" :value="type.id" :label="type.name"/>
          </el-select>
        </el-form-item>
        <el-form-item label="更新后属性值为" prop="value">
          <el-select v-model="form.value" style="width: 80%" :filterable="filterable">
            <el-option v-for="(option, index) in options" :key="index" :value="option.id" :label="option.name">
              <div v-if="option.email">
                <span>{{option.id}}({{option.name}})</span>
              </div>
            </el-option>
          </el-select>
        </el-form-item>
      </el-form>
      <template v-slot:footer>
        <ms-dialog-footer
          @cancel="dialogVisible = false"
          @confirm="submit('form')"/>
      </template>
    </el-dialog>
  </div>
</template>

<script>
  import MsDialogFooter from "../../../common/components/MsDialogFooter";
  import {listenGoBack, removeGoBackListener} from "../../../../../common/js/utils";

  export default {
    name: "BatchEdit",
    components: {
      MsDialogFooter
    },
    props: {
      typeArr: Array,
      valueArr: Object,
      dialogTitle: {
        type: String,
        default: "批量操作"
      }
    },
    data() {
      return {
        dialogVisible: false,
        form: {},
        size: 0,
        rules: {
          type: {required: true, message: "请选择属性", trigger: ['blur','change']},
          value: {required: true, message: "请选择属性对应的值", trigger: ['blur','change']}
        },
        options: [],
        filterable: false,
      }
    },
    methods: {
      submit(form) {
        this.$refs[form].validate((valid) => {
          if (valid) {
            this.$emit("batchEdit", this.form);
            this.dialogVisible = false;
          } else {
            return false;
          }
        });
      },
      open() {
        this.dialogVisible = true;
        this.size = this.$parent.selectRows.size;
        listenGoBack(this.handleClose);
      },
      handleClose() {
        this.form = {};
        this.options = [];
        removeGoBackListener(this.handleClose);
      },
      changeType(val) {
        this.$set(this.form, "value", "");
        this.filterable = val === "maintainer" || val === "executor";
        this.options = this.valueArr[val];
      }
    }
  }
</script>

<style scoped>

</style>
