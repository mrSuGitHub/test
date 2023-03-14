<template>
  <div class="CustomerRegistrationManagement">
    <PageTitle title="客户注册信息管理"/>
    <div class="card" style="margin-bottom: 32px;">
      <div>
        <p class="title">基本信息</p>
      </div>
      <div>
        <Form :data="formObj" ref="childComponentObj" :Change="Change" :submit="submitFormObj"/>
      </div>
    </div>
    <div>
      <div class="tip-box">
        <div style="display: flex;">
          <div class="tip" style="cursor: pointer;" v-for="(v, index) in tipData"
               :class="tipIndex == index ? 'tip-ac' : ''" @click="tabBtn(v, index)" :key="index">{{ v.text }}
          </div>
        </div>
      </div>
      <div class="card" style="margin-top: 0;border-radius: 0px 10px 10px 10px;">
        <div v-show="tipID == 0">
          <Form :data="formObj1" ref="childComponentObj1" :submit="submitFormObjObj1"/>
        </div>
        <div v-show="tipID == 1">
          <div class="d-flex justify-space-between" style="margin-bottom: 18px;">
            <div>
              <p style="margin-bottom: 0;" class="title">购买服务</p>
            </div>
            <div>
              <el-button type="primary" class="Search-btn" size="mini" @click="add"><i
                  class="el-icon-circle-plus-outline" style="margin-right: 5px;"></i>添加
              </el-button>
              <el-button type="primary" class="Filter-btn" size="mini" @click="del"><i class="el-icon-delete"
                                                                                       style="margin-right: 5px;"></i>删除
              </el-button>
            </div>
          </div>
          <div>
            <Tabel :Obj="tableObj" :handelSubmit="operationSubmit" :HandleSizeChange="HandleSizeChange"
                   :handleSelectionChangeCom="handleSelectionChangeCom"
                   :HandleCurrentChange="HandleCurrentChange">
              <template slot="integration" scope="{row}"><!--文字下划线样式控件插槽-->
                <p>{{ row.integration == 0 ? '否' : '是' }}</p></template>
            </Tabel>
          </div>
        </div>
        <div v-show="tipID != 0 && tipID != 1">
          <div>
            <div>
              <Form :data="formObj3" ref="childComponentformObj3" :Change="Change1" :submit="submitFormObj3"/>
            </div>
            <div>
              <div class="d-flex justify-space-between" style="margin-bottom: 18px;">
                <div>
                  <p style="margin-bottom: 0;" class="title">关联公司</p>
                </div>
                <div>
                  <el-button type="primary" class="Search-btn" size="mini" @click="addCom"><i
                      class="el-icon-circle-plus-outline" style="margin-right: 5px;"></i>添加
                  </el-button>
                  <el-button type="primary" class="Filter-btn" size="mini" @click="delCom"><i class="el-icon-delete"
                                                                                              style="margin-right: 5px;"></i>删除
                  </el-button>
                </div>
              </div>
              <Tabel :Obj="tableObjLCom" :handelSubmit="operationSubmit1"></Tabel>
            </div>
            <div>
              <div class="d-flex justify-space-between" style="margin-bottom: 18px;margin-top: 35px;">
                <div>
                  <p style="margin-bottom: 0;" class="title">关联用户</p>
                </div>
                <div>
                  <el-button type="primary" class="Search-btn" size="mini" @click="addUser"><i
                      class="el-icon-circle-plus-outline" style="margin-right: 5px;"></i>添加
                  </el-button>
                  <el-button type="primary" class="Filter-btn" size="mini" @click="delUser"><i class="el-icon-delete"
                                                                                               style="margin-right: 5px;"></i>删除
                  </el-button>
                </div>
              </div>
              <Tabel :Obj="tableObjLUser" :handelSubmit="operationSubmit2"></Tabel>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div style="display:flex;justify-content: space-between;margin-top: 32px;">
      <div></div>
      <div>
        <el-button @click="save" size="mini" class="Search-btn">保存</el-button>
      </div>
    </div>


    <el-dialog
        title="新增"
        :visible.sync="dialogVisible"
        width="70%"
        :before-close="handleClose">
      <div>
        <el-form :model="ruleForm2" :rules="rules" ref="ruleForm2" label-width="100px" class="demo-ruleForm">
          <div class="d-flex">
            <el-form-item label="服务名称" prop="corpCode" style="flex: 1;">
              <el-select style="width: 100%;"
                         v-model="ruleForm2.corpCode"
                         filterable
                         remote
                         reserve-keyword
                         placeholder="请输入关键词"
                         :remote-method="remoteMethod"
                         :loading="loading">
                <el-option
                    v-for="item in optionsSelect"
                    :key="item.code"
                    :label="item.name"
                    :value="item.code">
                </el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="版本名称" prop="corporationName" style="flex: 1">
              <el-input v-model="ruleForm2.corporationName"></el-input>
            </el-form-item>
          </div>
          <div class="d-flex">
            <el-form-item label="单价" prop="shortName" style="flex: 1;">
              <el-input v-model="ruleForm2.shortName"></el-input>
            </el-form-item>
            <el-form-item label="数量" prop="countryOrRegion" style="flex: 1">
              <el-input v-model="ruleForm2.countryOrRegion"></el-input>
            </el-form-item>
          </div>
          <div class="d-flex">
            <el-form-item label="开通注册公司" prop="provinceOrState" style="flex: 1;">
              <el-input v-model="ruleForm2.provinceOrState"></el-input>
            </el-form-item>
          </div>
        </el-form>
      </div>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取消</el-button>
        <el-button type="primary" @click="formObj2Save">保存</el-button>
      </div>
    </el-dialog>

    <!--关联公司-->
    <el-dialog
        :title="submitForm4Status == 'add' ? '新增' : ''"
        :visible.sync="dialogVisibleCom"
        width="70%"
        :before-close="handleClose">
      <div>
        <Form :data="formObj4" ref="childComponent4" :Change="Change4" :submit="submitForm4"/>
      </div>
      <span slot="footer" class="dialog-footer">
    <el-button @click="dialogVisible = false">取 消</el-button>
    <el-button type="primary" @click="saveFormObj4">确 定</el-button>
  </span>
    </el-dialog>

    <!--关联用户-->
    <el-dialog
        :title="submitForm5Status == 'add' ? '新增' : ''"
        :visible.sync="dialogVisibleUser"
        width="70%"
        :before-close="handleClose">
      <div>
        <Form :data="formObj5" ref="childComponent5" :submit="submitForm5"/>
      </div>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogVisibleUser = false">取 消</el-button>
        <el-button type="primary" @click="saveFormObj5">确 定</el-button>
      </div>
    </el-dialog>

  </div>
</template>

<script>
import PageTitle from "@/components/public/PageTitle";
import Form from "@/components/public/Form";
import {GetTreeAddresss, selectServicesByCondition} from '@/api/app'
import Tabel from "@/components/public/Tabel";
import {addRegister} from "@/api/pma/perspective";

export default {
  name: "CustomerRegistrationManagement",
  components: {Tabel, Form, PageTitle},
  data() {
    return {
      obj: {}, /*基本信息*/
      obj1: {}, /*支付信息*/
      obj2: [], /*加工阶段*/
      table: {},  /*采购GI服务*/

      gObj: {
        essentialInformation: {/*基本信息*/},
        paymentInformation: {/*支付信息*/},
        procurementServices: {/*采购服务*/}
      },


      LformData: [],  /*加工阶段表单数据*/
      LtableDataCom: [], /*加工阶段表格数据公司*/
      LtableDataUser: [], /*加工阶段表格数据用户*/

      dialogVisibleCom: false,
      dialogVisibleUser: false,
      dialogVisible: false,
      ruleForm2: {
        /*        serviceName: '',
                versionName: '',
                unitPrice: '',
                quantity: '',
                company: ''*/
        discount: '',
        corpCode: '',
        corporationName: '',
        shortName: '',
        countryOrRegion: '',
        provinceOrState: ''
      },
      formObj3: {
        "title": "",
        "formproperties": {
          "inline": true
        },
        "formData": [{
          "id": 1,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "销售加工阶段",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": true,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "salesStage",
          "formStatus": true
        }, {
          "id": 2,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "公司名称",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "corporationName",
          "formStatus": true
        }, {
          "id": 14,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "公司简称",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "shortName",
          "formStatus": true
        }, {
          "id": 44,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "公司代码",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "corpCode",
          "formStatus": true
        }, {
          "id": 4,
          "span": 12,
          "assemblyname": "下拉框",
          "label": "国家/地区",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "Please select",
          "category": 1,
          "source": false,
          "apiUrl": "/tracing/api/Tracing/GetTreeAddresss",
          "key": "label",
          "val": "label",
          "check": false,
          "multiplechoice": false,
          "searchable": false,
          "formStatus": true,
          "options": [],
          "customParameters": "countryOrRegion"
        }, {
          "id": 5,
          "span": 12,
          "assemblyname": "下拉框",
          "label": "省（州）",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": true,
          "placeholder": "Please select",
          "category": 1,
          "source": true,
          "apiUrl": "",
          "key": "",
          "val": "",
          "check": false,
          "multiplechoice": false,
          "searchable": false,
          "formStatus": false,
          "options": [],
          "customParameters": "provinceOrState"
        }, {
          "id": 6,
          "span": 12,
          "assemblyname": "下拉框",
          "label": "市（区）",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": true,
          "placeholder": "Please select",
          "category": 1,
          "source": true,
          "apiUrl": "",
          "key": "",
          "val": "",
          "check": false,
          "multiplechoice": false,
          "searchable": false,
          "formStatus": false,
          "options": [],
          "customParameters": "cityOrDistrict"
        },
          {
            "id": 8,
            "span": 12,
            "assemblyname": "单行文本框",
            "label": "详细地址",
            "value": "",
            "type": "",
            "hidelabels": true,
            "classname": "",
            "message": "brandMessage",
            "disabled": false,
            "placeholder": "brandMessage",
            "category": 0,
            "check": false,
            "iconChekc": false,
            "customParameters": "corpCode",
            "formStatus": true
          }, {
            "id": 9,
            "span": 12,
            "assemblyname": "下拉框",
            "label": "联系人",
            "value": "",
            "type": "",
            "hidelabels": true,
            "classname": "",
            "message": "brandMessage",
            "disabled": false,
            "placeholder": "Please select",
            "category": 0,
            "source": true,
            "apiUrl": "",
            "key": "",
            "val": "",
            "check": false,
            "multiplechoice": false,
            "searchable": false,
            "formStatus": true,
            "options": [{"value": 1, "label": "Option 1", "disabled": true}, {
              "value": 2,
              "label": "Option 2",
              "disabled": true
            }],
            "customParameters": "name"
          }, {
            "id": 10,
            "span": 12,
            "assemblyname": "单行文本框",
            "label": "电话：",
            "value": "",
            "type": "number",
            "hidelabels": true,
            "classname": "",
            "message": "brandMessage",
            "disabled": false,
            "placeholder": "brandMessage",
            "category": 0,
            "check": false,
            "iconChekc": false,
            "customParameters": "contactNumber",
            "formStatus": true
          }, {
            "id": 11,
            "span": 12,
            "assemblyname": "单行文本框",
            "label": "是否关联合作公司",
            "value": "",
            "type": "",
            "hidelabels": true,
            "classname": "",
            "message": "brandMessage",
            "disabled": false,
            "placeholder": "brandMessage",
            "category": 1,
            "check": false,
            "iconChekc": false,
            "customParameters": "isAssociatedCompany",
            "formStatus": true,
            "options": [{"value": "0", "label": "是", "disabled": false}, {
              "value": "1",
              "label": "否",
              "disabled": false
            }]
          }, {
            "id": 12,
            "span": 12,
            "assemblyname": "单行文本框",
            "label": "销售业务加工阶段",
            "value": "",
            "type": "",
            "hidelabels": true,
            "classname": "",
            "message": "brandMessage",
            "disabled": false,
            "placeholder": "brandMessage",
            "category": 0,
            "check": false,
            "iconChekc": false,
            "customParameters": "saleProcessingStage",
            "formStatus": true
          }, {
            "id": 13,
            "span": 12,
            "assemblyname": "单行文本框",
            "label": "采购业务加工阶段",
            "value": "",
            "type": "",
            "hidelabels": true,
            "classname": "",
            "message": "brandMessage",
            "disabled": false,
            "placeholder": "brandMessage",
            "category": 0,
            "check": false,
            "iconChekc": false,
            "customParameters": "purchaseProcessingStage",
            "formStatus": true
          }]
      },
      rules: {},
      optionsSelect: [],
      options: [],
      value: [],
      list1: [],
      loading: false,
      states: ["Alabama", "Alaska", "Arizona",
        "Arkansas", "California", "Colorado",
        "Connecticut", "Delaware", "Florida",
        "Georgia", "Hawaii", "Idaho", "Illinois",
        "Indiana", "Iowa", "Kansas", "Kentucky",
        "Louisiana", "Maine", "Maryland",
        "Massachusetts", "Michigan", "Minnesota",
        "Mississippi", "Missouri", "Montana",
        "Nebraska", "Nevada", "New Hampshire",
        "New Jersey", "New Mexico", "New York",
        "North Carolina", "North Dakota", "Ohio",
        "Oklahoma", "Oregon", "Pennsylvania",
        "Rhode Island", "South Carolina",
        "South Dakota", "Tennessee", "Texas",
        "Utah", "Vermont", "Virginia",
        "Washington", "West Virginia", "Wisconsin",
        "Wyoming"],
      formObj: {
        "title": "",
        "formproperties": {"inline": true},
        "formData": [{
          "id": 1,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "公司代码",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "corpCode",
          "formStatus": true
        }, {
          "id": 2,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "公司名称",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "corporationName",
          "formStatus": true
        }, {
          "id": 3,
          "span": 12,
          "assemblyname": "下拉框",
          "label": "公司简称",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "Please select",
          "category": 0,
          "source": true,
          "apiUrl": "",
          "key": "",
          "val": "",
          "check": false,
          "multiplechoice": false,
          "searchable": false,
          "formStatus": true,
          "options": [{"value": 1, "label": "Option 1", "disabled": true}, {
            "value": 2,
            "label": "Option 2",
            "disabled": true
          }],
          "customParameters": "shortName"
        }, {
          "id": 4,
          "span": 12,
          "assemblyname": "下拉框",
          "label": "国家/地区",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "Please select",
          "category": 1,
          "source": false,
          "apiUrl": "/tracing/api/Tracing/GetTreeAddresss",
          "key": "label",
          "val": "label",
          "check": false,
          "multiplechoice": false,
          "searchable": false,
          "formStatus": true,
          "options": [],
          "customParameters": "countryOrRegion"
        }, {
          "id": 5,
          "span": 12,
          "assemblyname": "下拉框",
          "label": "省（州）",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": true,
          "placeholder": "Please select",
          "category": 1,
          "source": true,
          "apiUrl": "",
          "key": "",
          "val": "",
          "check": false,
          "multiplechoice": false,
          "searchable": false,
          "formStatus": false,
          "options": [],
          "customParameters": "provinceOrState"
        }, {
          "id": 6,
          "span": 12,
          "assemblyname": "下拉框",
          "label": "市（区）",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": true,
          "placeholder": "Please select",
          "category": 1,
          "source": true,
          "apiUrl": "",
          "key": "",
          "val": "",
          "check": false,
          "multiplechoice": false,
          "searchable": false,
          "formStatus": false,
          "options": [],
          "customParameters": "cityOrDistrict"
        }, {
          "id": 7,
          "span": 12,
          "assemblyname": "下拉框",
          "label": "详细地址",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "Please select",
          "category": 0,
          "source": true,
          "apiUrl": "",
          "key": "",
          "val": "",
          "check": false,
          "multiplechoice": false,
          "searchable": false,
          "formStatus": true,
          "options": [],
          "customParameters": "detailedAddress"
        }, {
          "id": 8,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "联系人",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "name",
          "formStatus": true
        }, {
          "id": 9,
          "span": 12,
          "assemblyname": "下拉框",
          "label": "性别",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "Please select",
          "category": 1,
          "source": true,
          "apiUrl": "",
          "key": "",
          "val": "",
          "check": false,
          "multiplechoice": false,
          "searchable": false,
          "formStatus": true,
          "options": [{"value": "0", "label": "Male  男", "disabled": false}, {
            "value": "1",
            "label": "Female 女",
            "disabled": false
          }, {"value": "2", "label": "Female Unspecified (X) 不明确", "disabled": false}, {
            "value": "3",
            "label": "Undisclosed (U) 未公开 ",
            "disabled": false
          }],
          "customParameters": "gender",
          "iconChekc": false
        }, {
          "id": 10,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "联系电话",
          "value": "",
          "type": "number",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "contactNumber",
          "formStatus": true
        }, {
          "id": 11,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "称谓",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 1,
          "check": false,
          "iconChekc": false,
          // 称谓 0：JR.   \r1：SR.   2：M.D.\r   3：PH.D
          "options": [{"value": "0", "label": "JR.", "disabled": false}, {
            "value": "1",
            "label": "SR.",
            "disabled": false
          }, {"value": "2", "label": "M.D.", "disabled": false}, {
            "value": "3",
            "label": "PH.D",
            "disabled": false
          }],
          "customParameters": "appellation",
          "formStatus": true
        }, {
          "id": 12,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "职位",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "position",
          "formStatus": true
        }, {
          "id": 13,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "邮件",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "mail",
          "formStatus": true
        }, {
          "id": 19,
          "span": 12,
          "assemblyname": "下拉框",
          "label": "域标识",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "Please select",
          "category": 1,
          "source": true,
          "apiUrl": "",
          "key": "",
          "val": "",
          "check": false,
          "multiplechoice": false,
          "searchable": false,
          "formStatus": true,
          "options": [{"value": "0", "label": "私域", "disabled": false}, {
            "value": "1",
            "label": "公域",
            "disabled": false
          }],
          "customParameters": "domainIdentification"
        }, {
          "id": 20,
          "span": 12,
          "assemblyname": "下拉框",
          "label": "品牌编号",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "Please select",
          "category": 0,
          "source": true,
          "apiUrl": "",
          "key": "",
          "val": "",
          "check": false,
          "multiplechoice": false,
          "searchable": false,
          "formStatus": true,
          "options": [{"value": 1, "label": "Option 1", "disabled": true}, {
            "value": 2,
            "label": "Option 2",
            "disabled": true
          }],
          "customParameters": "brandCode"
        }, {
          "id": 18,
          "span": 12,
          "assemblyname": "下拉框",
          "label": "加工阶段",
          "value": [],
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "Please select",
          "category": 1,
          "source": true,
          "apiUrl": "",
          "key": "",
          "val": "",
          "check": false,
          "multiplechoice": true,
          "searchable": false,
          "formStatus": true,
          "options": [{"value": 11, "label": "L1", "disabled": false}, {
            "value": 22,
            "label": "L2",
            "disabled": false
          }, {
            "value": 33,
            "label": "L3",
            "disabled": false
          }, {
            "value": 44,
            "label": "L4",
            "disabled": false
          }],
          "customParameters": "processingStage"
        }, {
          "id": 20,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "PVH PoC - Email",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "brandMail",
          "formStatus": true
        }, {
          "id": 15,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "PM",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "pmUserid",
          "formStatus": true
        }, {
          "id": 21,
          "span": 12,
          "assemblyname": "多行文本框",
          "label": "PVH Remarks",
          "value": "",
          "type": "textarea",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "brandRemarks",
          "formStatus": true
        }, {
          "id": 20,
          "span": 12,
          "assemblyname": "多行文本框",
          "label": "GI Remarks",
          "value": "",
          "type": "textarea",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "giRemarks",
          "formStatus": true
        }, {
          "id": 23,
          "span": 12,
          "assemblyname": "下拉框",
          "label": "培训状态",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "Please select",
          "category": 1,
          "source": true,
          "apiUrl": "",
          "key": "",
          "val": "",
          "check": false,
          "multiplechoice": false,
          "searchable": false,
          "formStatus": true,
          "options": [
            {
              "value": "100",
              "label": "pma.perspective.Contact established",
              "disabled": false
            },
            {
              "value": "101",
              "label": "pma.perspective.Contact Kick off",
              "disabled": false
            },
            {
              "value": "102",
              "label": "pma.perspective.research completed",
              "disabled": false
            },
            {
              "value": "110",
              "label": "pma.perspective.attended training",
              "disabled": false
            },
            {
              "value": "120",
              "label": "pma.perspective.call back completed",
              "disabled": false
            }
          ],
          "customParameters": "trainingStatus"
        }, {
          "id": 24,
          "span": 12,
          "assemblyname": "下拉框",
          "label": "付款状态",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "Please select",
          "category": 1,
          "source": true,
          "apiUrl": "",
          "key": "",
          "val": "",
          "check": false,
          "multiplechoice": false,
          "searchable": false,
          "formStatus": true,
          "options": [
            {
              "value": "201",
              "label": "pma.perspective.To be confirmed",
              "disabled": false
            },
            {
              "value": "202",
              "label": "pma.perspective.payment information registered",
              "disabled": false
            },
            {
              "value": "210",
              "label": "pma.perspective.Confirmed",
              "disabled": false
            },
            {
              "value": "220",
              "label": "pma.perspective.Completed",
              "disabled": false
            }
          ],
          "customParameters": "paymentStatus"
        }, {
          "id": 25,
          "span": 12,
          "assemblyname": "下拉框",
          "label": "账户开设状态",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "Please select",
          "category": 1,
          "source": true,
          "apiUrl": "",
          "key": "",
          "val": "",
          "check": false,
          "multiplechoice": false,
          "searchable": false,
          "formStatus": true,
          "options": [
            {
              "value": "301",
              "label": "pma.perspective.import template completed",
              "disabled": false
            },
            {
              "value": "302",
              "label": "pma.perspective.open training account",
              "disabled": false
            },
            {
              "value": "310",
              "label": "pma.perspective.Audit completed",
              "disabled": false
            },
            {
              "value": "320",
              "label": "pma.perspective.registration successful",
              "disabled": false
            }
          ],
          "customParameters": "accountStatus"
        },
          {
            "id": 26,
            "span": 12,
            "assemblyname": "下拉框",
            "label": "接口对接状态",
            "value": "",
            "type": "",
            "hidelabels": true,
            "classname": "",
            "message": "brandMessage",
            "disabled": false,
            "placeholder": "Please select",
            "category": 1,
            "source": true,
            "apiUrl": "",
            "key": "",
            "val": "",
            "check": false,
            "multiplechoice": false,
            "searchable": false,
            "formStatus": true,
            "options": [
              {
                "value": "400",
                "label": "pma.perspective.initial",
                "disabled": false
              },
              {
                "value": "401",
                "label": "pma.perspective.attended system training",
                "disabled": false
              },
              {
                "value": "402",
                "label": "pma.perspective.attended interface training",
                "disabled": false
              },
              {
                "value": "410",
                "label": "pma.perspective.completed develop plan",
                "disabled": false
              },
              {
                "value": "420",
                "label": "pma.perspective.Technical support intervention",
                "disabled": false
              },
              {
                "value": "421",
                "label": "pma.perspective.created token",
                "disabled": false
              },
              {
                "value": "422",
                "label": "pma.perspective.completed interface test",
                "disabled": false
              },
              {
                "value": "430",
                "label": "pma.perspective.completed interface develop",
                "disabled": false
              },
              {
                "value": "440",
                "label": "pma.perspective.completed interface test1",
                "disabled": false
              },
              {
                "value": "449",
                "label": "pma.perspective.Abort interface docking",
                "disabled": false
              }
            ],
            "customParameters": "interfaceStatus"
          },]
      },
      formObj1: {
        "title": "",
        "formproperties": {
          "width": "100%",
          "labelalignment": "top",
          "formlabelwidth": "120px",
          "classname": "",
          "span": 24,
          "inline": true
        },
        "formData": [{
          "id": 1,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "抬头",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "invoiceTitle",
          "formStatus": true
        }, {
          "id": 2,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "账号",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "paymentAccountNumber",
          "formStatus": true
        }, {
          "id": 3,
          "span": 12,
          "assemblyname": "下拉框",
          "label": "开票地址",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "Please select",
          "category": 0,
          "source": true,
          "apiUrl": "",
          "key": "",
          "val": "",
          "check": false,
          "multiplechoice": false,
          "searchable": false,
          "formStatus": true,
          "options": [],
          "customParameters": "invoiceAddress"
        }, {
          "id": 4,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "联系人",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "name",
          "formStatus": true
        }, {
          "id": 5,
          "span": 12,
          "assemblyname": "下拉框",
          "label": "联系电话",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "Please select",
          "category": 0,
          "source": true,
          "apiUrl": "",
          "key": "",
          "val": "",
          "check": false,
          "multiplechoice": false,
          "searchable": false,
          "formStatus": true,
          "options": [{"value": 1, "label": "Option 1", "disabled": true}, {
            "value": 2,
            "label": "Option 2",
            "disabled": true
          }],
          "customParameters": "invoiceNumber"
        }, {
          "id": 6,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "税号",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "taxNumber",
          "formStatus": true
        }, {
          "id": 7,
          "span": 24,
          "assemblyname": "单行文本框",
          "label": "邮寄地址",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "postAddress",
          "formStatus": true
        }, {
          "id": 8,
          "span": 12,
          "assemblyname": "下拉框",
          "label": "付款公司",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "Please select",
          "category": 0,
          "source": true,
          "apiUrl": "",
          "key": "",
          "val": "",
          "check": false,
          "multiplechoice": false,
          "searchable": false,
          "formStatus": true,
          "options": [{"value": 1, "label": "Option 1", "disabled": true}, {
            "value": 2,
            "label": "Option 2",
            "disabled": true
          }],
          "customParameters": "payment_corporation_name"
        }, {
          "id": 'invoiceCode',
          "span": 12,
          "assemblyname": "下拉框",
          "label": "编号",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "Please select",
          "category": 0,
          "source": true,
          "apiUrl": "",
          "key": "",
          "val": "",
          "check": false,
          "multiplechoice": false,
          "searchable": false,
          "formStatus": true,
          "options": [{"value": 1, "label": "Option 1", "disabled": true}, {
            "value": 2,
            "label": "Option 2",
            "disabled": true
          }],
          "customParameters": "invoiceCode"
        }, {
          "id": 9,
          "span": 12,
          "assemblyname": "多行文本框",
          "label": "付款所属地",
          "value": "",
          "type": "textarea",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "paymentCompanyLocated",
          "formStatus": true
        }, {
          "id": 10,
          "span": 12,
          "assemblyname": "多行文本框",
          "label": "备注",
          "value": "",
          "type": "textarea",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "remarks",
          "formStatus": true
        }]
      },
      formObj2: {
        "title": "",
        "formproperties": {
          "width": "100%",
          "labelalignment": "top",
          "formlabelwidth": "120px",
          "classname": "",
          "span": 24,
          "inline": true
        },
        "formData": [{
          "id": 6,
          "span": 12,
          "assemblyname": "下拉框",
          "label": "服务名称",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "Please select",
          "category": 1,
          "source": false,
          "apiUrl": "/authority/services/selectServicesByCondition",
          "key": "name",
          "val": "id",
          "check": false,
          "multiplechoice": false,
          "searchable": false,
          "formStatus": true,
          "options": [],
          "customParameters": ""
        }, {
          "id": 2,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "版本名称",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "",
          "formStatus": true
        }, {
          "id": 3,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "单价",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "",
          "formStatus": true
        }, {
          "id": 4,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "数量",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "",
          "formStatus": true
        }, {
          "id": 5,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "开通注册公司",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "",
          "formStatus": true
        }]
      },
      formObj4: {
        "title": "",
        "formproperties": {
          "inline": true
        },
        "formData": [{
          "id": 1,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "公司名称",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "corporationName",
          "formStatus": true
        }, {
          "id": 2,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "公司简称",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "shortName",
          "formStatus": true
        }, {
          "id": 3,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "公司代码",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "corpCode",
          "formStatus": true
        },
          {
            "id": 4,
            "span": 12,
            "assemblyname": "下拉框",
            "label": "国家/地区",
            "value": "",
            "type": "",
            "hidelabels": true,
            "classname": "",
            "message": "brandMessage",
            "disabled": false,
            "placeholder": "Please select",
            "category": 1,
            "source": false,
            "apiUrl": "/tracing/api/Tracing/GetTreeAddresss",
            "key": "label",
            "val": "label",
            "check": false,
            "multiplechoice": false,
            "searchable": false,
            "formStatus": true,
            "options": [],
            "customParameters": "countryOrRegion"
          }, {
            "id": 5,
            "span": 12,
            "assemblyname": "下拉框",
            "label": "省（州）",
            "value": "",
            "type": "",
            "hidelabels": true,
            "classname": "",
            "message": "brandMessage",
            "disabled": true,
            "placeholder": "Please select",
            "category": 1,
            "source": true,
            "apiUrl": "",
            "key": "",
            "val": "",
            "check": false,
            "multiplechoice": false,
            "searchable": false,
            "formStatus": false,
            "options": [],
            "customParameters": "provinceOrState"
          }, {
            "id": 6,
            "span": 12,
            "assemblyname": "下拉框",
            "label": "市（区）",
            "value": "",
            "type": "",
            "hidelabels": true,
            "classname": "",
            "message": "brandMessage",
            "disabled": true,
            "placeholder": "Please select",
            "category": 1,
            "source": true,
            "apiUrl": "",
            "key": "",
            "val": "",
            "check": false,
            "multiplechoice": false,
            "searchable": false,
            "formStatus": false,
            "options": [],
            "customParameters": "cityOrDistrict"
          },
          {
            "id": 7,
            "span": 12,
            "assemblyname": "单行文本框",
            "label": "详细地址",
            "value": "",
            "type": "",
            "hidelabels": true,
            "classname": "",
            "message": "brandMessage",
            "disabled": false,
            "placeholder": "brandMessage",
            "category": 0,
            "check": false,
            "iconChekc": false,
            "customParameters": "detailedAddress",
            "formStatus": true
          }, {
            "id": 8,
            "span": 12,
            "assemblyname": "下拉框",
            "label": "是否是开票公司",
            "value": "",
            "type": "",
            "hidelabels": true,
            "classname": "",
            "message": "brandMessage",
            "disabled": false,
            "placeholder": "Please select",
            "category": 1,
            "source": true,
            "apiUrl": "",
            "key": "",
            "val": "",
            "check": false,
            "multiplechoice": false,
            "searchable": false,
            "formStatus": true,
            "options": [{"value": true, "label": "是", "disabled": false}, {
              "value": false,
              "label": "否",
              "disabled": false
            }],
            "customParameters": "isTrading"
          }, {
            "id": 9,
            "span": 12,
            "assemblyname": "下拉框",
            "label": "是否加工工厂",
            "value": "",
            "type": "",
            "hidelabels": true,
            "classname": "",
            "message": "brandMessage",
            "disabled": false,
            "placeholder": "Please select",
            "category": 1,
            "source": true,
            "apiUrl": "",
            "key": "",
            "val": "",
            "check": false,
            "multiplechoice": false,
            "searchable": false,
            "formStatus": true,
            "options": [{"value": true, "label": "是", "disabled": false}, {
              "value": false,
              "label": "否",
              "disabled": false
            }],
            "customParameters": "isFacility"
          }, {
            "id": 10,
            "span": 12,
            "assemblyname": "下拉框",
            "label": "加工阶段",
            "value": "",
            "type": "",
            "hidelabels": true,
            "classname": "",
            "message": "brandMessage",
            "disabled": false,
            "placeholder": "Please select",
            "category": 1,
            "source": true,
            "apiUrl": "",
            "key": "",
            "val": "",
            "check": false,
            "multiplechoice": false,
            "searchable": false,
            "formStatus": true,
            "options": [{"value": 1, "label": "Option 1", "disabled": true}, {
              "value": 2,
              "label": "Option 2",
              "disabled": true
            }],
            "customParameters": "vendorSectionCode"
          }, {
            "id": 12,
            "span": 12,
            "assemblyname": "多行文本框",
            "label": "类别备注",
            "value": "",
            "type": "textarea",
            "hidelabels": true,
            "classname": "",
            "message": "brandMessage",
            "disabled": false,
            "placeholder": "brandMessage",
            "category": 0,
            "check": false,
            "iconChekc": false,
            "customParameters": "remark",
            "formStatus": true
          }]
      },
      formObj5: {
        "title": "",
        "formproperties": {
          "width": "100%",
          "labelalignment": "top",
          "formlabelwidth": "120px",
          "classname": "",
          "span": 24,
          "inline": true
        },
        "formData": [{
          "id": 1,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "邮箱",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "mail",
          "formStatus": true
        }, {
          "id": 2,
          "span": 12,
          "assemblyname": "单行文本框",
          "label": "名称",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "brandMessage",
          "category": 0,
          "check": false,
          "iconChekc": false,
          "customParameters": "name",
          "formStatus": true
        }, {
          "id": 3,
          "span": 12,
          "assemblyname": "下拉框",
          "label": "是否是管理员",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "Please select",
          "category": 1,
          "source": true,
          "apiUrl": "",
          "key": "",
          "val": "",
          "check": false,
          "multiplechoice": false,
          "searchable": false,
          "formStatus": true,
          "options": [{"value": 0, "label": "是", "disabled": false}, {
            "value": 1,
            "label": "否",
            "disabled": false
          }],
          "customParameters": "is_administrator"
        }, {
          "id": 4,
          "span": 12,
          "assemblyname": "下拉框",
          "label": "是否是API",
          "value": "",
          "type": "",
          "hidelabels": true,
          "classname": "",
          "message": "brandMessage",
          "disabled": false,
          "placeholder": "Please select",
          "category": 1,
          "source": true,
          "apiUrl": "",
          "key": "",
          "val": "",
          "check": false,
          "multiplechoice": false,
          "searchable": false,
          "formStatus": true,
          "options": [{"value": 0, "label": "是", "disabled": false}, {
            "value": 1,
            "label": "否",
            "disabled": false
          }],
          "customParameters": "is_API"
        }]
      },
      GetTreeAddresssData: [],
      tipData: [
        {
          text: '支付信息',
          id: 0
        }, {
          text: 'L1',
          id: 11
        }, {
          text: 'L2',
          id: 22
        },/* {
          text: 'L3',
          id: 33
        }, {
          text: 'L4',
          id: 44
        },*/ {
          text: '采购GI的服务',
          id: 1
        }],
      tipIndex: 0,
      tipID: 0,
      tableObj: {
        son: false, /*是否有子级表单*/
        operation: true,  /*是否展示操作按钮功能*/
        childrenOperation: true, /*是否展示子表操作按钮功能*/
        operationText: 'operation', /*操作栏标题*/
        selectionStatus: false,  /*是否需要复选框*/
        childrenOperationText: 'operation', /*子表操作栏标题*/
        paginationStatus: true, /*是否启用分页组件*/
        operationWidth: '200',
        total: 0, /*总条数 通过 this.tableObj.total = 接口返回的总条数字段 api 请求*/
        page: 1,
        pageSize: 10,
        head: [ /*表头数据*/
          {
            label: '服务名称',  /*标题*/
            prop: 'corpCode',/*绑定数据源obj展示字段*/
            fixed: 'left',  /*表头固定，参数：left / right / ''*/
            width: '180', /*表头宽度*/
            // slot: false,  /*是否需要插槽*/
          }, {
            label: '折扣',  /*标题*/
            prop: 'discount',/*绑定数据源obj展示字段*/
            fixed: 'left',  /*表头固定，参数：left / right / ''*/
            width: '80', /*表头宽度*/
            // slot: false,  /*是否需要插槽*/
          }, {
            label: '版本名称',/*标题*/
            prop: 'corporationName',/*绑定数据源obj展示字段*/
            width: ''/*表头固定，参数：left / right / ''*/
          }, {
            label: '单价',/*标题*/
            prop: 'shortName',/*绑定数据源obj展示字段*/
            width: '100'/*表头固定，参数：left / right / ''*/
          }, {
            label: '数量',/*标题*/
            prop: 'countryOrRegion',/*绑定数据源obj展示字段*/
            width: '100',/*表头固定，参数：left / right / ''*/
          }, {
            label: '开通注册公司',/*标题*/
            prop: 'provinceOrState',/*绑定数据源obj展示字段*/
          }
        ],
        childrenHead: [/*子表头数组*/],
        operationData: [
          {
            "id": 'edit', /*按钮ID*/
            "value": "",  /*按钮内容*/
            "classname": "",  /*自定义class*/
            "disabled": false,  /*是否被禁用*/
            "type": "text", /*按钮类型 primary / success / warning / danger / info / text*/
            "size": "mini", /*按钮大小 medium / small / mini*/
            "icon": "el-icon-edit-outline", /*按钮icon*/
          }, {
            "id": 'delete',/*按钮ID*/
            "value": "",/*按钮内容*/
            "classname": "",/*自定义class*/
            "disabled": false,/*是否被禁用*/
            "type": "text",/*按钮类型 primary / success / warning / danger / info / text*/
            "size": "mini",/*按钮大小 medium / small / mini*/
            "icon": "el-icon-delete",/*按钮icon*/
          }
        ],
        childrenOperationData: [/*字表操作栏*/],
        tableData: [/*表格数据*/]
      },
      tableObj5: {
        son: false, /*是否有子级表单*/
        operation: true,  /*是否展示操作按钮功能*/
        childrenOperation: true, /*是否展示子表操作按钮功能*/
        operationText: 'operation', /*操作栏标题*/
        selectionStatus: false,  /*是否需要复选框*/
        childrenOperationText: 'operation', /*子表操作栏标题*/
        paginationStatus: true, /*是否启用分页组件*/
        operationWidth: '200',
        total: 0, /*总条数 通过 this.tableObj.total = 接口返回的总条数字段 api 请求*/
        page: 1,
        pageSize: 10,
        head: [ /*表头数据*/
          {
            label: '服务名称',  /*标题*/
            prop: 'corpCode',/*绑定数据源obj展示字段*/
            fixed: 'left',  /*表头固定，参数：left / right / ''*/
            width: '180', /*表头宽度*/
            // slot: false,  /*是否需要插槽*/
          }, {
            label: '折扣',  /*标题*/
            prop: 'discount',/*绑定数据源obj展示字段*/
            fixed: 'left',  /*表头固定，参数：left / right / ''*/
            width: '80', /*表头宽度*/
            // slot: false,  /*是否需要插槽*/
          }, {
            label: '版本名称',/*标题*/
            prop: 'corporationName',/*绑定数据源obj展示字段*/
            width: ''/*表头固定，参数：left / right / ''*/
          }, {
            label: '单价',/*标题*/
            prop: 'shortName',/*绑定数据源obj展示字段*/
            width: '100'/*表头固定，参数：left / right / ''*/
          }, {
            label: '数量',/*标题*/
            prop: 'countryOrRegion',/*绑定数据源obj展示字段*/
            width: '100',/*表头固定，参数：left / right / ''*/
          }, {
            label: '开通注册公司',/*标题*/
            prop: 'provinceOrState',/*绑定数据源obj展示字段*/
          }
        ],
        childrenHead: [/*子表头数组*/],
        operationData: [
          {
            "id": 'edit', /*按钮ID*/
            "value": "",  /*按钮内容*/
            "classname": "",  /*自定义class*/
            "disabled": false,  /*是否被禁用*/
            "type": "text", /*按钮类型 primary / success / warning / danger / info / text*/
            "size": "mini", /*按钮大小 medium / small / mini*/
            "icon": "el-icon-edit-outline", /*按钮icon*/
          }, {
            "id": 'delete',/*按钮ID*/
            "value": "",/*按钮内容*/
            "classname": "",/*自定义class*/
            "disabled": false,/*是否被禁用*/
            "type": "text",/*按钮类型 primary / success / warning / danger / info / text*/
            "size": "mini",/*按钮大小 medium / small / mini*/
            "icon": "el-icon-delete",/*按钮icon*/
          }
        ],
        childrenOperationData: [/*字表操作栏*/],
        tableData: [/*表格数据*/]
      },
      tableObjLUser: {
        son: false, /*是否有子级表单*/
        operation: true,  /*是否展示操作按钮功能*/
        childrenOperation: true, /*是否展示子表操作按钮功能*/
        operationText: 'operation', /*操作栏标题*/
        selectionStatus: false,  /*是否需要复选框*/
        childrenOperationText: 'operation', /*子表操作栏标题*/
        paginationStatus: true, /*是否启用分页组件*/
        operationWidth: '200',
        total: 0, /*总条数 通过 this.tableObj.total = 接口返回的总条数字段 api 请求*/
        page: 1,
        pageSize: 10,
        head: [ /*表头数据*/
          {
            label: '邮箱',
            prop: 'mail',
            width: '',
          }, {
            label: '名称',
            prop: 'name',
            width: '',
          }, {
            label: '是否是管理员',
            prop: 'is_administrator',
            width: '',
          }, {
            label: '是否是API',
            prop: 'is_API',
            width: '',
          },
        ],
        childrenHead: [/*子表头数组*/],
        operationData: [
          {
            "id": 'edit', /*按钮ID*/
            "value": "",  /*按钮内容*/
            "classname": "",  /*自定义class*/
            "disabled": false,  /*是否被禁用*/
            "type": "text", /*按钮类型 primary / success / warning / danger / info / text*/
            "size": "mini", /*按钮大小 medium / small / mini*/
            "icon": "el-icon-edit-outline", /*按钮icon*/
          }, {
            "id": 'delete',/*按钮ID*/
            "value": "",/*按钮内容*/
            "classname": "",/*自定义class*/
            "disabled": false,/*是否被禁用*/
            "type": "text",/*按钮类型 primary / success / warning / danger / info / text*/
            "size": "mini",/*按钮大小 medium / small / mini*/
            "icon": "el-icon-delete",/*按钮icon*/
          }
        ],
        childrenOperationData: [/*字表操作栏*/],
        tableData: [/*表格数据*/]
      },
      tableObjLCom: {
        son: false, /*是否有子级表单*/
        operation: true,  /*是否展示操作按钮功能*/
        childrenOperation: true, /*是否展示子表操作按钮功能*/
        operationText: 'operation', /*操作栏标题*/
        selectionStatus: false,  /*是否需要复选框*/
        childrenOperationText: 'operation', /*子表操作栏标题*/
        paginationStatus: true, /*是否启用分页组件*/
        operationWidth: '200',
        total: 0, /*总条数 通过 this.tableObj.total = 接口返回的总条数字段 api 请求*/
        page: 1,
        pageSize: 10,
        head: [ /*表头数据*/
          {
            label: '服务名称',  /*标题*/
            prop: 'corpCode',/*绑定数据源obj展示字段*/
            fixed: 'left',  /*表头固定，参数：left / right / ''*/
            width: '180', /*表头宽度*/
            // slot: false,  /*是否需要插槽*/
          }, {
            label: '折扣',  /*标题*/
            prop: 'discount',/*绑定数据源obj展示字段*/
            fixed: 'left',  /*表头固定，参数：left / right / ''*/
            width: '80', /*表头宽度*/
            // slot: false,  /*是否需要插槽*/
          }, {
            label: '版本名称',/*标题*/
            prop: 'corporationName',/*绑定数据源obj展示字段*/
            width: ''/*表头固定，参数：left / right / ''*/
          }, {
            label: '单价',/*标题*/
            prop: 'shortName',/*绑定数据源obj展示字段*/
            width: '100'/*表头固定，参数：left / right / ''*/
          }, {
            label: '数量',/*标题*/
            prop: 'countryOrRegion',/*绑定数据源obj展示字段*/
            width: '100',/*表头固定，参数：left / right / ''*/
          }, {
            label: '开通注册公司',/*标题*/
            prop: 'provinceOrState',/*绑定数据源obj展示字段*/
          }
        ],
        childrenHead: [/*子表头数组*/],
        operationData: [
          {
            "id": 'edit', /*按钮ID*/
            "value": "",  /*按钮内容*/
            "classname": "",  /*自定义class*/
            "disabled": false,  /*是否被禁用*/
            "type": "text", /*按钮类型 primary / success / warning / danger / info / text*/
            "size": "mini", /*按钮大小 medium / small / mini*/
            "icon": "el-icon-edit-outline", /*按钮icon*/
          }, {
            "id": 'delete',/*按钮ID*/
            "value": "",/*按钮内容*/
            "classname": "",/*自定义class*/
            "disabled": false,/*是否被禁用*/
            "type": "text",/*按钮类型 primary / success / warning / danger / info / text*/
            "size": "mini",/*按钮大小 medium / small / mini*/
            "icon": "el-icon-delete",/*按钮icon*/
          }
        ],
        childrenOperationData: [/*字表操作栏*/],
        tableData: [/*表格数据*/]
      },
      opList: [],
      oldObjData: [],
      ChangeStatus: true,
      submitForm4Status: 'add', /*edit*/
      submitForm5Status: 'add', /*edit*/
      tabStatus: false,

      lid: '',
      oldId: '',
    }
  },
  created() {
    GetTreeAddresss({}).then((response) => {
      // this.GetTreeAddresssData = response;

      this.GetTreeAddresssData = handleTreeData(response);

      function handleTreeData(data) {
        if (!data || !Array.isArray(data)) {
          return [];
        }

        return data.map(item => {
          if (item.children && item.children.length > 0) {
            item.value = item.label;
            item.children = handleTreeData(item.children);
          }
          return item;
        });
      }

      /*      let price = 100;
            let quantity = 6;
            let maxDiscount = 0.825;
            let batchDiscount = '0;0.5;0.75;0.825;-';
            let longDiscount = 0.85;
            let discount = 0;
            let totalPrice = this.calculatePrice(price, quantity, maxDiscount, batchDiscount, longDiscount, discount);
            console.debug('总价为：', totalPrice);*/


    });

    // this.addRegisterMet();


    this.initTest();


  },
  methods: {
    initTest() {
      this.formObj.formData.forEach((f) => {
        f.value = '1'
      })
      this.formObj1.formData.forEach((f) => {
        f.value = '1'
      })
    },
    addRegisterMet(obj) {
      addRegister(obj).then((response) => {
        console.debug(response)
        if (response.code == "BUSINESS.0002") {
          this.$message({message: response.msg, type: 'error'})
        } else {
          this.$message({message: response.msg, type: 'success'})
        }
      })
    },
    save() {/*保存*/
      this.$refs.childComponentObj.submitForm('dynamicValidateForm', 1);
      this.$refs.childComponentObj1.submitForm('dynamicValidateForm', 1);
      /*essentialInformation: {/!*基本信息*!/},
      paymentInformation: {/!*支付信息*!/},
      procurementServices: {/!*采购服务*!/}*/
      this.gObj.procurementServices = this.tableObj.tableData

      let obj = {
        registerCorporationRequestList: [],
        registerCorporationServiceRequestList: this.gObj.procurementServices,
        invoiceInformationRequest: this.paymentInformation,
      }
      obj = Object.assign(obj, this.gObj.essentialInformation);
      let LformData = JSON.stringify(this.LformData);
      LformData = JSON.parse(LformData)
      let newData = []
      LformData.forEach((f) => {
        f.data = JSON.parse(f.data);
        let ftobj = convertArray(f.data);
        f.tableDataCom.forEach((f1) => {
          if (f.id == f1.id) {
            f.comTable = f1.data
          }
        });
        f.tableDataUser.forEach((f1) => {
          if (f.id == f1.id) {
            f.userTable = f1.data
          }
        });
        ftobj.registerCorporationSubsidiaryRequestList = f.comTable;
        ftobj.registerCorporationUserRequestList = f.userTable;
        newData.push(ftobj)
      })
      obj.registerCorporationRequestList = newData

      console.debug(JSON.stringify(obj), '保存')


      function convertArray(arr) {
        const result = {};
        for (let i = 0; i < arr.length; i++) {
          result[arr[i].customParameters] = arr[i].value.toLowerCase();
        }
        return result;
      }
    },
    submitFormObj(data, status, obj) {
      this.gObj.essentialInformation = obj;
    },
    submitFormObjObj1(data, status, obj) {
      console.debug('invoiceInformationRequest12321', obj)
      this.gObj.paymentInformation = obj;
    },
    operationSubmit1(v, index, row) {
      console.log(v, index, row, this.formObj4.formData);
      if (v.id == 'edit') {
        this.dialogVisibleCom = true;
        this.submitForm4Status = index;

        /* eslint-disable */
        const originalObj = row;

        const arrayObj = this.formObj4.formData


        for (let i = 0; i < arrayObj.length; i++) {
          const customParameter = arrayObj[i].customParameters;
          if (originalObj.hasOwnProperty(customParameter)) {
            arrayObj[i].value = originalObj[customParameter];
          }
        }

        this.formObj4.formData = arrayObj;

      } else if (v.id == 'delete') {
        this.submitForm4Status = 'add';
        this.$confirm('此操作将永久删除该记录, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$message({
            type: 'success',
            message: '删除成功!'
          });
          this.tableObjLCom.tableData.splice(index, 1);
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          });
        });
      }
    },
    operationSubmit2(v, index, row) {
      console.log(v, index, row, this.formObj4.formData);
      if (v.id == 'edit') {
        this.dialogVisibleUser = true;
        this.submitForm5Status = index;

        /* eslint-disable */
        const originalObj = row;

        const arrayObj = this.formObj5.formData


        for (let i = 0; i < arrayObj.length; i++) {
          const customParameter = arrayObj[i].customParameters;
          if (originalObj.hasOwnProperty(customParameter)) {
            arrayObj[i].value = originalObj[customParameter];
          }
        }

        this.formObj5.formData = arrayObj;

      } else if (v.id == 'delete') {
        this.submitForm5Status = 'add';
        this.$confirm('此操作将永久删除该记录, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$message({
            type: 'success',
            message: '删除成功!'
          });
          this.tableObjLUser.tableData.splice(index, 1);
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          });
        });
      }
    },
    submitFormObj4(v, index, row) {/*加工阶段表单*/
      console.debug(v, index, row, '--------- submitFormObj4')
    },
    submitFormObj5L(v, index, row) {/*加工阶段表单*/
      console.debug(v, index, row, '--------- submitFormObj5L')
    },
    submitFormObj6L(v, index, row) {/*加工阶段表单*/
      console.debug(v, index, row, '--------- submitFormObj6L')
    },
    saveFormObj4() {
      this.$refs.childComponent4.submitForm('dynamicValidateForm', 1);

    },
    submitForm4(data, status, obj) {
      console.debug(this.tipID) /*公司表新增方法*/
      console.debug(data, status, obj)
      console.log(this.submitForm4Status, 'submitForm4Status')
      let tableData = JSON.stringify(this.tableObjLCom.tableData);
      tableData = JSON.parse(tableData);
      if (this.submitForm4Status == 'add') {
        tableData.push(obj)
      } else {
        tableData[this.submitForm4Status] = obj;
      }

      this.tableObjLCom.tableData = tableData;

      this.dialogVisibleCom = false;
      // this.$refs.childComponent4.resetForm('dynamicValidateForm');
    },
    saveFormObj5() {
      this.$refs.childComponent5.submitForm('dynamicValidateForm', 1);
    },
    submitForm5(data, status, obj) {
      console.debug(data, status, obj);
      /*表格数据源 tableObj2 */
      let tableData = JSON.stringify(this.tableObjLUser.tableData);
      tableData = JSON.parse(tableData);
      if (this.submitForm5Status == 'add') {
        tableData.push(obj)
      } else {
        tableData[this.submitForm5Status] = obj;
      }
      this.tableObjLUser.tableData = tableData;
      console.debug(tableData)
      this.dialogVisibleUser = false;
      this.$refs.childComponent5.resetForm('dynamicValidateForm');


    },

    tabBtn(v, index) {
      if (index == this.tipIndex) {
        return;
      }
      this.tipIndex = index;
      this.tipID = v.id;


    },

    addCom() {
      console.debug('关联公司新增')
      this.dialogVisibleCom = true;
      this.submitForm4Status = 'add';
      // this.tipData[this.tipIndex].text
      this.formObj4.formData.forEach((f) => {
        if (f.id == 10) {
          f.value = this.tipData[this.tipIndex].text;
          f.disabled = true;
        }
      })
    },
    delCom() {
      console.debug('关联公司删除')
    },
    addUser() {
      console.debug('关联用户新增');
      this.dialogVisibleUser = true;
      this.submitForm5Status = 'add';
    },
    delUser() {
      console.debug('关联用户删除')
    },
    calculatePrice(price, quantity, maxDiscount, batchDiscount, longDiscount, discount) {
      let batchDiscountArr = batchDiscount.split(';').map(d => parseFloat(d));
      let batchDiscountLen = batchDiscountArr.length;
      let discountArr = new Array(batchDiscountLen).fill(longDiscount).concat([discount]);
      let discountLen = discountArr.length;
      console.log(discountLen)
      let batchQuantityArr = new Array(batchDiscountLen).fill(1);
      if (batchDiscount.endsWith('-')) {
        batchQuantityArr[batchDiscountLen - 1] = quantity - batchDiscountLen + 1;
      } else if (batchDiscount.endsWith(';')) {
        batchQuantityArr[batchDiscountLen - 1] = Math.floor(quantity / batchDiscountLen);
      } else {
        batchQuantityArr = batchDiscountArr.map((d, i) => i < batchDiscountLen - 1 ? Math.floor(quantity * d) : quantity - batchQuantityArr.slice(0, batchDiscountLen - 1).reduce((a, b) => a + b, 0));
      }
      let batchTotalPrice = batchQuantityArr.reduce((total, q, i) => total + price * q * discountArr[i], 0);
      let longTotalPrice = quantity > batchQuantityArr[0] ? (price * (1 - longDiscount) * (quantity - batchQuantityArr[0])) : 0;
      let totalPrice = batchTotalPrice + longTotalPrice;
      if (maxDiscount > 0 && totalPrice / quantity > maxDiscount) {
        totalPrice = maxDiscount * quantity;
      }
      return totalPrice;
    },
    remoteMethod(query) {
      if (query !== '') {
        this.loading = true;

        selectServicesByCondition({
          "condition": query
        }).then((response) => {
          this.loading = false;
          console.debug(response.data)
          let data = response.data;
          this.optionsSelect = data;
        });

      } else {
        this.options = [];
      }
    },
    submitForm(data, status, obj) {
      console.log(data, status, obj)
    },
    formObj2Save() {
      // this.dialogVisible = false;
      let ruleForm2 = JSON.stringify(this.ruleForm2);
      ruleForm2 = JSON.parse(ruleForm2);
      let tableData = JSON.stringify(this.tableObj.tableData);
      tableData = JSON.parse(tableData);
      console.debug(ruleForm2.id, typeof ruleForm2.id);
      if (typeof ruleForm2.id == "number") {
        for (let i = 0; i < tableData.length; i++) {
          if (tableData[i].id == ruleForm2.id) {
            tableData[i] = ruleForm2;
          }
        }
      } else {
        ruleForm2.id = this.tableObj.tableData.length == 0 ? 0 : this.tableObj.tableData[this.tableObj.tableData.length - 1].id + 1;
        // this.tableObj.tableData.push(ruleForm2);
        tableData.push(ruleForm2)
      }
      this.tableObj.tableData = tableData;
      this.dialogVisible = false;
      this.ruleForm2 = {
        id: '',
        corpCode: '',
        discount: '',
        corporationName: '',
        shortName: '',
        countryOrRegion: '',
        provinceOrState: '',
      }

    },
    handleClose() {
      this.dialogVisible = false;
      this.dialogVisibleCom = false;
    },
    add() {
      this.dialogVisible = true;
    },
    del() {
      // this.tableObj.tableData.splice();
    },
    Change(v) {
      // console.debug(v);
      if (v.id == 4) {
        this.formObj.formData.forEach((f) => {
          if (f.id == 5) {
            this.GetTreeAddresssData.forEach((g) => {
              if (g.label == v.value) {
                f.disabled = false;
                f.options = g.children;
              }
            })
          }
        });
        return '';
      }
      if (v.id == 5) {
        this.formObj.formData.forEach((f) => {
          if (f.id == 6) {
            v.options.forEach((g) => {
              if (g.label == v.value) {
                console.log(g)
                f.disabled = false;
                console.debug(g)
                f.options = g.children;
              }
            })
          }
        });
        return '';
      }
      if (v.id == 18) {
        let valueData = v.value
        let objData = [];
        if (this.ChangeStatus) {
          this.ChangeStatus = false;
          setTimeout(() => {

            this.ChangeStatus = true;
            // let obj = {};

            valueData.forEach((f) => {
              v.options.forEach((g) => {
                if (g.value == f) {
                  objData.push({
                    text: g.label,
                    id: g.value
                  })
                }
              })
            })
            this.oldObjData = objData;
            objData.unshift({
              text: '支付信息',
              id: 0
            })
            objData.push({
              text: '采购GI的服务',
              id: 1
            })
            this.tipData = objData;
          }, 500)
          return '';
        }
      }
    },
    Change1(v) {
      // console.debug(v);
      if (v.id == 4) {
        this.formObj3.formData.forEach((f) => {
          if (f.id == 5) {
            this.GetTreeAddresssData.forEach((g) => {
              if (g.label == v.value) {
                f.disabled = false;
                f.options = g.children;
              }
            })
          }
        });
        return '';
      }
      if (v.id == 5) {
        this.formObj3.formData.forEach((f) => {
          if (f.id == 6) {
            v.options.forEach((g) => {
              console.debug(g.label, v.value)
              if (g.label == v.value) {
                console.log(g)
                f.disabled = false;
                console.debug(g)
                f.options = g.children;
              }
            })
          }
        });
        return '';
      }
      if (v.id == 18) {
        let valueData = v.value
        let objData = [];
        if (this.ChangeStatus) {
          this.ChangeStatus = false;
          setTimeout(() => {

            this.ChangeStatus = true;
            // let obj = {};

            console.debug(valueData, 'valueData')
            valueData.forEach((f) => {
              v.options.forEach((g) => {
                if (g.value == f) {
                  objData.push({
                    text: g.label,
                    id: g.value
                  })
                }
              })
            })

            /*let obj = {
              text: '支付信息',
              id: 0
            }*/
            objData.unshift({
              text: '支付信息',
              id: 0
            })
            objData.push({
              text: '采购GI的服务',
              id: 1
            })
            console.debug(objData, '12312312')
            /*objData.push({
              text: '支付信息',
              id: 0
            },{
              text: '采购GI的服务',
              id: 1
            });*/
            // objData
            this.tipData = objData;
            // tipData

          }, 500)
          return '';
        }
      }
    },
    Change4L(v) {
      // console.debug(v);
      if (v.id == 4) {
        this.formObj4L.formData.forEach((f) => {
          if (f.id == 5) {
            this.GetTreeAddresssData.forEach((g) => {
              if (g.label == v.value) {
                f.disabled = false;
                f.options = g.children;
              }
            })
          }
        });
        return '';
      }
      if (v.id == 5) {
        this.formObj4L.formData.forEach((f) => {
          if (f.id == 6) {
            v.options.forEach((g) => {
              console.debug(g.label, v.value)
              if (g.label == v.value) {
                console.log(g)
                f.disabled = false;
                console.debug(g)
                f.options = g.children;
              }
            })
          }
        });
        return '';
      }
      if (v.id == 18) {
        let valueData = v.value
        let objData = [];
        if (this.ChangeStatus) {
          this.ChangeStatus = false;
          setTimeout(() => {

            this.ChangeStatus = true;
            // let obj = {};

            console.debug(valueData, 'valueData')
            valueData.forEach((f) => {
              v.options.forEach((g) => {
                if (g.value == f) {
                  objData.push({
                    text: g.label,
                    id: g.value
                  })
                }
              })
            })

            /*let obj = {
              text: '支付信息',
              id: 0
            }*/
            objData.unshift({
              text: '支付信息',
              id: 0
            })
            objData.push({
              text: '采购GI的服务',
              id: 1
            })
            console.debug(objData, '12312312')
            /*objData.push({
              text: '支付信息',
              id: 0
            },{
              text: '采购GI的服务',
              id: 1
            });*/
            // objData
            this.tipData = objData;
            // tipData

          }, 500)
          return '';
        }
      }
    },
    Change5L(v) {
      // console.debug(v);
      if (v.id == 4) {
        this.formObj5L.formData.forEach((f) => {
          if (f.id == 5) {
            this.GetTreeAddresssData.forEach((g) => {
              if (g.label == v.value) {
                f.disabled = false;
                f.options = g.children;
              }
            })
          }
        });
        return '';
      }
      if (v.id == 5) {
        this.formObj5L.formData.forEach((f) => {
          if (f.id == 6) {
            v.options.forEach((g) => {
              console.debug(g.label, v.value)
              if (g.label == v.value) {
                console.log(g)
                f.disabled = false;
                console.debug(g)
                f.options = g.children;
              }
            })
          }
        });
        return '';
      }
    },
    Change6L(v) {
      // console.debug(v);
      if (v.id == 4) {
        this.formObj6L.formData.forEach((f) => {
          if (f.id == 5) {
            this.GetTreeAddresssData.forEach((g) => {
              if (g.label == v.value) {
                f.disabled = false;
                f.options = g.children;
              }
            })
          }
        });
        return '';
      }
      if (v.id == 5) {
        this.formObj6L.formData.forEach((f) => {
          if (f.id == 6) {
            v.options.forEach((g) => {
              console.debug(g.label, v.value)
              if (g.label == v.value) {
                console.log(g)
                f.disabled = false;
                console.debug(g)
                f.options = g.children;
              }
            })
          }
        });
        return '';
      }
    },
    Change4(v) {
      // console.debug(v);
      if (v.id == 4) {
        this.formObj4.formData.forEach((f) => {
          if (f.id == 5) {
            this.GetTreeAddresssData.forEach((g) => {
              if (g.label == v.value) {
                f.disabled = false;
                f.options = g.children;
              }
            })
          }
        });
        return '';
      }
      if (v.id == 5) {
        this.formObj4.formData.forEach((f) => {
          if (f.id == 6) {
            v.options.forEach((g) => {
              console.debug(g.label, v.value)
              if (g.label == v.value) {
                console.log(g)
                f.disabled = false;
                console.debug(g)
                f.options = g.children;
              }
            })
          }
        });
        return '';
      }
    },
    operationSubmit(v, index, row) {
      /*
      * v：当前点击按钮内容
      * index：当前点击行数索引
      * row：当前点击行数对象*/
      console.debug(v, index, row)
      let tabobj = JSON.stringify(row);
      tabobj = JSON.parse(tabobj)
      if (v.id == 'edit') {
        this.dialogVisible = true;
        this.ruleForm2 = tabobj;
        return ''
      }

      if (v.id == 'delete') {
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.tableObj.tableData.splice(index, 1);
          this.$message({
            type: 'success',
            message: '删除成功!'
          });
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          });
        });
      }


    },
    HandleSizeChange(val) { /*每页多少条*/
      console.debug(val)
      this.tableObj.page = 1;
      this.tableObj.pageSize = val;
      this.list();
    },
    HandleCurrentChange(val) {  /*当前页*/
      console.debug(val)
      this.tableObj.page = val;
      this.list();
    },
    handleSelectionChangeCom(val) { /*复选框选中事件*/
      console.debug(val)
    },
    submitFormObj3(v) {/*加工阶段表单*/
      let data = JSON.stringify(v);
      let obj = {
        id: this.oldId,
        data: data,
        tableDataCom: [],
        tableDataUser: [],
      };


      this.$refs.childComponentformObj3.resetForm('dynamicValidateForm', 1);

      this.submitTableObj3({
        call: ((response) => {
          obj.tableDataCom = response
        })
      });
      this.submitTableObj3User({
        call: ((response) => {
          obj.tableDataUser = response
        })
      });
      // console.log(JSON.stringify(this.LformData))
      this.LformData = updateArray(this.LformData, obj)

      function updateArray(array, newItem) {
        const id = newItem.id;
        const index = array.findIndex(item => item.id === id);

        if (index >= 0) {
          array.splice(index, 1, newItem); // 用新元素替换旧元素
        } else {
          array.push(newItem); // 添加新元素
        }

        return array;
      }

      /* this.LformData.forEach((f) => {
         console.debug(JSON.parse(f.data), '------------', f.id)
       })*/

    },
    submitTableObj3(params) {
      let newObj = {
        id: this.oldId,
        data: this.tableObjLCom.tableData
      }

      let arr = this.LtableDataCom;

      let index = -1;
      for (let i = 0; i < arr.length; i++) {
        if (arr[i].id === newObj.id) {
          index = i;
        }
      }

      if (index !== -1) {
        // 如果存在相同 id 的元素，找到最后一个并删除
        for (let i = arr.length - 1; i >= 0; i--) {
          if (arr[i].id === newObj.id) {
            arr.splice(i, 1);
            break;
          }
        }
      }

      arr.push(newObj);
      console.log('清空')
      this.tableObjLCom.tableData = [];
      this.LtableDataCom = arr;
      params.call(arr)
    },
    submitTableObj3User(params) {
      let newObj = {
        id: this.oldId,
        data: this.tableObjLUser.tableData
      }

      let arr = this.LtableDataUser;

      let index = -1;
      for (let i = 0; i < arr.length; i++) {
        if (arr[i].id === newObj.id) {
          index = i;
        }
      }

      if (index !== -1) {
        // 如果存在相同 id 的元素，找到最后一个并删除
        for (let i = arr.length - 1; i >= 0; i--) {
          if (arr[i].id === newObj.id) {
            arr.splice(i, 1);
            break;
          }
        }
      }

      arr.push(newObj);
      console.log('清空')
      this.tableObjLUser.tableData = [];
      this.LtableDataUser = arr;
      params.call(arr)
    },
  },
  watch: {
    tipID(New, Old) {
      // LtableDataCom
      // LtableDataUser

      // tableObjLCom
      let tableObjLCom = this.LtableDataCom;
      let tableObjLUser = this.LtableDataUser;

      if (New != 0 && New != 1) {
        if (Old == 0 || Old == 1) {
          this.oldId = New
        } else {
          this.oldId = Old
        }
        //LtableData
        this.$refs.childComponentformObj3.submitForm('dynamicValidateForm', 1);
        /*this.submitTableObj3();
        this.submitTableObj3User();*/
      }
      if (New != 0 && New != 1) {
        console.log(this.LformData, 'this.LformData')
        this.LformData.forEach((f) => {
          if (f.id == New) {
            this.formObj3.formData = JSON.parse(f.data);

          }
        });
        tableObjLCom.forEach((f) => {
          if (f.id == New) {
            this.tableObjLCom.tableData = f.data;
          }
        })
        tableObjLUser.forEach((f) => {
          if (f.id == New) {
            this.tableObjLUser.tableData = f.data;
          }
        })
      }
    }
  }
}
</script>

<style scoped lang="scss">
.card {
  box-shadow: 0px 0px 4px 2px rgba(1, 51, 122, 0.09);
  padding: 11px 18px;
  margin-top: 29px;
  border-radius: 10px;

  .title {
    font-size: 18px;
    margin-bottom: 28px;
  }
}

.tip-box {
  background: #FFFFFF;
  border-radius: 8px 8px 0px 0px;
  border: 1px solid #01337A;
  display: inline-block;

  .tip {
    padding: 5px 12px;
    color: rgba(1, 51, 122, 1);
    border-right: 1px solid rgba(1, 51, 122, 1);
    font-size: 16px;
  }

  .tip:last-child {
    border-right: 0;
  }

  .tip-ac {
    color: #fff;
    background: rgba(1, 51, 122, 1);
  }
}
</style>
