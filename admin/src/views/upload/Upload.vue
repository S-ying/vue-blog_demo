<template>
  <div class="container">
    <div class="layout-breadcrumb">
      <Breadcrumb>
        <Breadcrumb-item href="#/">首页</Breadcrumb-item>
        <Breadcrumb-item>相册模块</Breadcrumb-item>
      </Breadcrumb>
    </div>
    <Row>
      <Col span="3">
      <cate-tree :type="2" :selectChange="selectChange"></cate-tree>
      </Col>
      <Col span="16">
      <div class="btns">
        <Button type="error" @click="removes">删除数据</Button>
        <Input icon="search" v-model="filter.originalname" placeholder="请输入..." style="width: 200px"></Input>
        <Button type="ghost" @click="search">搜索数据</Button>
      </div>
  
      <Table border :context="self" :columns="columns" :data="list" @on-selection-change="handlerSelectionChange"></Table>
  
      <div style="margin: 10px;overflow: hidden">
        <div style="float: right;">
          <Page :total="filter.total" :current="filter.page" :page-size="5" @on-change="changePage"></Page>
        </div>
      </div>
      </Col>
      <Col span="5" style="padding-left:10px;">
      <h2>上传相册文件</h2>
      <Upload 
      ref="upload"
      type="drag" 
      multiple 
      :action="postUrl" 
      :data="postData"
      name="avatar" 
      :format="['jpg','jpeg','png']" 
      :max-size="2000" 
      :before-upload="handleBeforeUpload"
      :on-success="handleSuccess" 
      :on-error="handleError"
      :on-format-error="handleFormatError" 
      :on-exceeded-size="handleMaxSize">
        <div style="padding: 20px 0">
          <Icon type="ios-cloud-upload" size="52" style="color: #3399ff"></Icon>
          <p>点击或将文件拖拽到这里上传</p>
        </div>
      </Upload>
      </Col>
    </Row> 
  </div>
</template>

<script>
  import CateTree from '../../components/layout/Cate.vue';
  import Base from '../../common/Base.js'
  var moment = require('moment');
  
  export default {
    mixins: [Base],
    components: {
      CateTree
    },
  
    data() {
      return {
        module: 'uploads',
        filter:{
          originalname:null,
          cateId:null
        },
        postData:{
          _id: null
        },
        handleBeforeUpload(){
    
          
        },
        handleError(err){
          if(err){
             this.$Message.info('请选择分类');
          }
        },
        columns: [{
            type: 'selection',
            width: 60,
            align: 'center'
          },
          {
            title: '原始名称',
            width:90,
            key: 'originalname'
          },
          {
            title: '文件大小',
            width:85,
            key: 'size'
          },
          {
            title: '文件名称',
            key: 'filename',
            width: 150
          },
          {
            title: '文件名称',
            key: 'path',
            width: 85,
            render: (h, params) => {
              console.log(params.row)
                return h('img',{
                  attrs:{
                    src:`http://localhost:3000/${params.row.filename}`,
                    width:"50px",
                    height:"50px"
                  }
                })
            }
          },
          {
            title: '上传时间',
            key: 'date',
            width:100,
            render: (h, params) => {
                return h('div',`${ new Date(params.row.date).toLocaleString() }`)
            }

            // render (row) {
            //     return `${ new Date(row.date).toLocaleString() }`;
            // }
          },
          {
            title: '操作',
            key: 'action',
            width: 70,
            align: 'center',

            render: (h, params) => {
              return h('div', [
                  h('Button', {
                      props: {
                          type: 'error',
                          size: 'small'
                      },
                      style: {
                          marginRight: '5px'
                      },
                      on: {
                          click: () => {
                              this.remove(params.index)
                          }
                      }
                  }, '删除')
              ])
           }
          }
        ]
      };
    },
    methods: {
      selectChange(node) {
        this.postData._id = node[0]._id;
        this.filter.cateId = node[0]._id;
        this.getData()
      },
      handleSuccess() {
        this.getData()
        //this.$refs.upload.clearFiles();
      },
      handleFormatError(file) {
        this.$Notice.warning({
          title: '文件格式不正确',
          desc: '文件 ' + file.name + ' 格式不正确，请上传 jpg 或 png 格式的图片。'
        });
      },
      handleMaxSize(file) {
        this.$Notice.warning({
          title: '超出文件大小限制',
          desc: '文件 ' + file.name + ' 太大，不能超过 2M。'
        });
      }
    },
    computed:{
      postUrl(){
        return `http://localhost:3000/uploads/filedata/${this.postData._id}`
      }
    }
  
  }
</script>