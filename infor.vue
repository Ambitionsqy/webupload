<template>
    <section>
        <el-form ref="form" :model="givelist" label-width="120px" class="give-infor" v-loading="listLoading">
            <el-form-item label="公司名称">
                <el-input v-model="givelist.fullName" disabled class="infor-input"></el-input>
            </el-form-item>
            <el-form-item label="公司简称">
                <el-input v-model="givelist.shortName" class="infor-input"></el-input>
            </el-form-item>
            <el-form-item label="LOGO">
                <sqy-upload uploadButton="#givelistLogo" uploadId="givelistLogo" @onSuccess="clickPreview" upErrorImg="上传公司logo宽高必须是180*180!" upTitle="点击上传LOGO" upError="请上传LOGO" :upInitImg="givelist.logo"></sqy-upload>
            </el-form-item>
            <el-form-item label="营业执照">
               <sqy-upload uploadButton="#givelistPermit" uploadId="givelistPermit" @onSuccess="clickPreview" upTitle="点击上传营业执照" upError="请上传营业执照" :upInitImg="givelist.permit"></sqy-upload>
            </el-form-item>
            <el-form-item label="负责人身份证明">
              <sqy-upload uploadButton="#givelistCard" uploadId="givelistCard" @onSuccess="clickPreview" upTitle="点击上传负责人身份证明" upError="请上传负责人身份证明" :upInitImg="givelist.card"></sqy-upload>
            </el-form-item>
            <el-form-item label="公司资质">
                <li v-for="(item,index) in givelist.certifiList" :key="item.id" class="infor-certifi">
                    <sqy-upload :uploadButton="'#givelistcertifi_'+index" :uploadId="'givelistcertifi_'+index" :upImgClass="'givelistcertifi_'+index" @onSuccess="clickPreview" upTitle="点击上传公司资质证书" upError="请上传公司资质证书" :upInitImg="item.imgUrls"></sqy-upload>
                    <el-button class="infor-certifi-click" @click="certifiRemove(index)" type="danger" v-show='index!=0'>删除</el-button>
                    <el-input v-model="item.name" placeholder="填写证书类型"></el-input>
                    <el-input v-model="item.grade" placeholder="填写证书等级"></el-input>
                </li>
                <li class="infor-certifi">
                  <sqy-upload uploadButton="#givelistcertifiAdd" uploadId="givelistcertifiAdd" upImgClass="givelistcertifiImg" @onSuccess="clickPreview" upTitle="点击上传公司资质证书" upError="请上传公司资质证书" :upInitImg="certifiAdd.imgUrl"></sqy-upload>
                  <el-button class="infor-certifi-click" @click="certifiAddClick()" type="primary">添加</el-button>
                  <el-input v-model="certifiAdd.name" placeholder="填写证书类型"></el-input>
                  <el-input v-model="certifiAdd.grade" placeholder="填写证书等级"></el-input>
                </li>
            </el-form-item>
            <el-form-item label="服务商类型">
                <el-select v-model="givelist.serviceType" multiple placeholder="请选择服务商类型" class="infor-select">
                    <el-option v-for="item in serviceType" :key="item.value" :label="item.label" :value="item.value">
                    </el-option>
                </el-select>
            </el-form-item>
            <el-form-item label="承接业务">
                <el-select v-model="givelist.work" multiple placeholder="请选择承接业务"  class="infor-select">
                    <el-option v-for="item in work" :key="item.value" :label="item.label" :value="item.value">
                    </el-option>
                </el-select>
            </el-form-item>
            <el-form-item label="服务和报价">
                <li v-for="(item,index) in givelist.quoteList" :key="item.id" class="infor-quote">
                  <el-select v-model="item.type" clearable placeholder="请选择服务方式">
                    <el-option v-for="items in quoteList" :key="items.value" :label="items.label" :value="items.value">
                    </el-option>
                  </el-select> 
                  <div class="infor-quote-click">
                    <el-input v-model="item.priceStart" title="请输入起始价格"></el-input>
                    <span class="infor-quote-dash">—</span>
                    <el-input v-model="item.priceEnd" title="请输入终止价格"></el-input>
                    <span>元/㎡</span>
                    <el-button @click="quoteRemove(index)" type="danger" v-show='index!=0'>删除</el-button>
                  </div>
                </li>
                <li class="infor-quote">
                  <el-select v-model="quoteAdd.type" clearable placeholder="请选择服务方式">
                    <el-option v-for="item in quoteList" :key="item.value" :label="item.label" :value="item.value">
                    </el-option>
                  </el-select> 
                  <div class="infor-quote-click">
                    <el-input v-model="quoteAdd.priceStart" title="请输入起始价格"></el-input>
                    <span class="infor-quote-dash">—</span>
                    <el-input v-model="quoteAdd.priceEnd" title="请输入终止价格"></el-input>
                    <span>元/㎡</span>
                    <el-button @click="quoteAddClick()" type="primary">添加</el-button>
                  </div>
                </li>
            </el-form-item>
            <el-form-item label="服务区域">
                <ul class="infor-city" >
                  <li v-for="(item,index) in cityList" :key="item.id"  @click="cityClicked(index)">
                    <i class="sqy-checkbox" v-show='item.status==0'></i>
                    <i class="sqy-checkboxed" v-show='item.status==1'></i>
                    <b>{{item.name}}</b>
                  </li>
                </ul>
            </el-form-item>
            <el-form-item label="公司特色">
                <ul class="infor-featured" >
                  <li v-for="(item,index) in featuredList" :key="item.id"  @click="featuredClicked(index)">
                    <i class="sqy-checkbox" v-show='item.status==0'></i>
                    <i class="sqy-checkboxed" v-show='item.status==1'></i>
                    <b>{{item.name}}</b>
                  </li>
                </ul>
            </el-form-item>
            <el-form-item label="公司成立于">
                <el-input v-model="givelist.found" disabled class="infor-input"></el-input>
            </el-form-item>
            <el-form-item label="注册资金（万）">
                <el-input v-model="givelist.fund" disabled class="infor-input"></el-input>
            </el-form-item>
            <el-form-item label="公司法人">
                <el-input v-model="givelist.person" disabled class="infor-input"></el-input>
            </el-form-item>
            <el-form-item label="公司地址">
                <el-input v-model="givelist.address" class="infor-input"></el-input>
            </el-form-item>
            <el-form-item label="店面信息">
              <li v-for="(item,index) in givelist.branchList" :key="item.id" class="infor-branch">
                <el-input v-model="item.name" class="infor-input"></el-input>
                <el-button @click="branchRemove(index)" type="danger" v-show='index!=0'>删除</el-button>
              </li>
              <li class="infor-branch">
                <el-input v-model="branchAdd.name" class="infor-input"></el-input>
                <el-button @click="branchAddClick()" type="primary">添加</el-button>
              </li>
            </el-form-item>
            <el-form-item label="业务联系人">
                <el-input v-model="givelist.liasion" class="infor-input"></el-input>
            </el-form-item>
            <el-form-item label="联系电话">
                <el-input v-model="givelist.phone" class="infor-input"></el-input>
            </el-form-item>
            <el-form-item label="公司网址">
                <el-input v-model="givelist.website" class="infor-input"></el-input>
            </el-form-item>
            <el-form-item label="高级信息">
            </el-form-item>
            <el-form-item label="开通会员">
              <el-select v-model="givelist.vip" clearable placeholder="请选择开通方式" @change="giveVipPopup">
                <el-option v-for="items in vipList" :key="items.value" :label="items.label" :value="items.value">
                </el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="账户类型">
              
            </el-form-item>
            <el-form-item label="账户积分">
                <el-input v-model="givelist.integral" class="infor-input"></el-input>
            </el-form-item>
            <el-form-item label="用户点评">
                <el-input v-model="givelist.comments" class="infor-input"></el-input>
                <span>条</span>
            </el-form-item>
            <el-form-item label="消费比例">
                <el-input v-model="givelist.scale" class="infor-input"></el-input>
            </el-form-item>
            <el-form-item label="用户名">
                <el-input v-model="givelist.userName" disabled class="infor-input"></el-input>
            </el-form-item>
            <el-form-item label="修改密码">
                <el-input v-model="givelist.password" class="infor-input"></el-input>
            </el-form-item>
            <el-form-item class="infor-submit">
                <el-button type="primary" @click="onSubmit">保存编辑</el-button>
            </el-form-item>
            <transition name="fade">
            <div class="sqy-popup-wrapper" v-show="givelistVipStatus" @click="VipStatusToggle">
                <div class="sqy-popup" ref="sqyPopup">
                  <div class="sqy-popup-title">
                    <span v-show="givelist.vip=='1'">充值账户</span>
                    <span v-show="givelist.vip=='2'">包量账户</span>
                    <i class="el-icon-close" @click="cardClick"></i>
                  </div>
                  <div class="sqy-popup-content clearfloat">
                    <li  v-show="givelist.vip=='1'">
                      <el-form-item label="充值金币">
                        <el-input v-model="vipOne.vipOneVal" class="infor-input" placeholder="请输入充值金币"></el-input>
                      </el-form-item>
                      <el-form-item label="有效时间">
                          <el-date-picker v-model="vipOne.vipOneDate" type="daterange" range-separator="至" start-placeholder="开始日期" end-placeholder="结束日期" format="yyyy 年 MM 月 dd 日"
                            value-format="timestamp">
                          </el-date-picker>
                      </el-form-item>
                    </li>
                    <li v-show="givelist.vip=='2'">
                      <el-form-item label="包量金额">
                        <el-input v-model="vipTwo.vipTwoVal" class="infor-input" placeholder="请输入包量金额"></el-input>
                      </el-form-item>
                      <el-form-item label="包量数量">
                        <el-input v-model="vipTwo.vipTwoItem" class="infor-input" placeholder="请输入包量数量" @change="vipTwoMeanVal"></el-input>
                      </el-form-item>
                      <el-form-item label="包量平均价">
                        <el-input v-model="vipTwo.vipTwoMean" disabled class="infor-input" placeholder="请输入包量数量"></el-input>
                        <span>元/条</span>
                      </el-form-item>
                      <el-form-item label="有效时间">
                          <el-date-picker v-model="vipTwo.vipTwoDate" type="daterange" range-separator="至" start-placeholder="开始日期" end-placeholder="结束日期" format="yyyy 年 MM 月 dd 日"
                            value-format="timestamp">
                          </el-date-picker>
                      </el-form-item>
                    </li>
                    <p style="text-align: center;"><el-button type="primary" @click="vipSubmit">保存编辑</el-button></p>
                  </div>
                </div>
            </div>
        </transition>
        </el-form>
        <el-dialog :visible.sync="inforDialogVisible">
           <img :src="inforDialogImageUrl" alt="" width="100%">
        </el-dialog>
        
        
    </section>
</template>
<script>
import $ from 'jquery'
import sqyUpload from '@/components/Sqyupload';
import { storeIndexGive } from '@/api/store-give'
  export default {
    data() {
      return {
        listLoading: true,
        givelist: {
          fullName: '成都墨斗方尊建筑装饰工程有限公司',
          shortName:'墨斗方尊',
          logo:'https://zqins.oss-cn-shanghai.aliyuncs.com/public/uploads/pic/20191022164011/a8f1efbe11865f6a07eef1d3eb8697a0c5b81ae1.jpeg',
          permit:' https://www.zqins.com/public/uploads/info/20190119/201908301001x1334n.jpg',
          card:'https://www.zqins.com/public/uploads/info/20190119/20190830536E68551B48E001C03F96A8CF4B7E59.png',
          certifiList:[{
            id:'1',
            name:'施工资质1',
            grade:'一级',
            imgUrls:'https://www.zqins.com/public/uploads/info/20190119/201908301001x1334n.jpg'
            },{
            id:'2',
            name:'施工资质2',
            grade:'二级',
            imgUrls:'https://www.zqins.com/public/uploads/info/20190119/20190830536E68551B48E001C03F96A8CF4B7E59.png',
            },{
            id:'3',
            name:'施工资质3',
            grade:'3级',
            imgUrls:'http://file.zqins.com/uploads/images/20191206/201912061401106417_3.webp',
            }],
          serviceType: '',
          work:'',
          quoteList:[
            {
              type:'1',
              priceStart:'500',
              priceEnd:'700',
            }
          ],
          featured:'',
          cityList:'',
          found: '2019',
          fund: '500',
          person: '孙巧银',
          address:'武汉市江岸区竹叶山红星美凯龙4楼',
          branchList:[
            {name:'武汉市江岸区竹叶山红星美凯龙4楼'}
          ],
          liasion:'孙胜男',
          phone:'17685405486',
          website:'www.zqins.com',
          // 高级
          vip:'',
          integral: '100',
          comments:'1000',
          scale:'20%',
          userName:'sdfkjabdgjfdgsyu',
          password:''
        },
        certifiAdd:{
           name:'',
           grade:'',
           imgUrl:''
        },
        quoteAdd:{
           type:'',
           priceStart:'',
           priceEnd:'',
        },
        branchAdd:{
          name:'',
        },
        serviceType: [{
          value: '1',
          label: '总承包商'
        }, {
          value: '2',
          label: '专业工匠'
        }, {
          value: '3',
          label: '蚵仔煎'
        }, {
          value: '4',
          label: '龙须面'
        }, {
          value: '5',
          label: '北京烤鸭'
        }],
        work: [{
          value: '1',
          label: '住宅家装'
        }, {
          value: '2',
          label: '别墅装修'
        }, {
          value: '3',
          label: '蚵仔煎'
        }, {
          value: '4',
          label: '龙须面'
        }, {
          value: '5',
          label: '北京烤鸭'
        }],
        quoteList: [{
          value: '1',
          label: '设计装修方案+装修施工'
        }, {
          value: '2',
          label: '设计方案+施工'
        }, {
          value: '3',
          label: '设计方案+材料+施工'
        }, {
          value: '4',
          label: '软装搭配方案'
        }],
        featuredList: [{
          value: '1',
          status:0,
          name: '零增项'
        }, {
          value: '2',
          status:1,
          name: '自创公益'
        }, {
          value: '3',
          status:0,
          name: '装修贷款'
        }, {
          value: '4',
          status:1,
          name: '标准化施工'
        }],
        cityList: [{
          value: '1',
          status:0,
          name: '零增项'
        }, {
          value: '2',
          status:1,
          name: '自创公益'
        }, {
          value: '3',
          status:0,
          name: '装修贷款'
        }, {
          value: '4',
          status:1,
          name: '标准化施工'
        }],
        vipList:[{
          value: '1',
          label: '充值账户'
        }, {
          value: '2',
          label: '包量账户'
        }],
        inforDialogImageUrl: '',
        inforDialogVisible: false,
        givelistVipStatus:false,
        vipOne:{
          vipOne:'1',
          vipOneVal:'',
          vipOneDate:'',
        },
        vipTwo:{
          vipTwo:'2',
          vipTwoVal:'',
          vipTwoItem:'',
          vipTwoMean:'',
          vipTwoDate:'',
        },
      }
    },
    created() {
        this.fetchData();        
    },
    methods: {
      fetchData() {
        this.listLoading = true
        storeIndexGive().then(response => {
        //   this.tableData = response.data.items
          this.listLoading = false;
        })
      },
      clickPreview(imgurl){
        this.inforDialogImageUrl=imgurl;
        this.inforDialogVisible=true;
      },
      certifiRemove(index){
       this.$confirm("您正在进行删除操作，您确定要删除该证书吗？", '删除证书', {
            cancelButtonText: '取消',
            confirmButtonText: '确定',
            showClose:false,
          }).then(() => {
            this.givelist.certifiList.splice(index,1);
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
      },
      certifiAddClick(){
        let imgUrlss=$("#givelistcertifiImg").attr("src")
        if(imgUrlss==""){
          this.$message.error("请上传资质证书！")
        }else if(this.certifiAdd.name==""){
          this.$message.error("请填写证书名称！")
        }else if(this.certifiAdd.grade==""){
          this.$message.error("请填写证书等级！")
        }else{
          this.givelist.certifiList.push({
            name  : this.certifiAdd.name,
            grade : this.certifiAdd.grade,
            imgUrls: imgUrlss,
          })
          this.certifiAdd.name='';
          this.certifiAdd.grade="";
          this.certifiAdd.imgUrl="";
          $("#givelistcertifiImg").attr("src","");
        }
        console.log(this.givelist.certifiList)
      },
      quoteAddClick(){
        if(this.quoteAdd.type==""){
          this.$message.error("请选择服务方式！")
        }else if(this.quoteAdd.priceStart==""){
          this.$message.error("请输入起始价格！")
        }else if(this.quoteAdd.priceEnd==""){
          this.$message.error("请输入终止价格！")
        }else{
          this.givelist.quoteList.push({
            type  : this.quoteAdd.type,
            priceStart : this.quoteAdd.priceStart,
            priceEnd: this.quoteAdd.priceEnd,
          })
          this.quoteAdd.type='';
          this.quoteAdd.priceStart="";
          this.quoteAdd.priceEnd="";
        } 
      },
      quoteRemove(index){
        this.$confirm("您正在进行删除操作，您确定要删除该服务吗？", '删除服务报价', {
            cancelButtonText: '取消',
            confirmButtonText: '确定',
            showClose:false,
          }).then(() => {
            this.givelist.quoteList.splice(index, 1);
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
      },
      branchAddClick(){
        if(this.branchAdd.name==""){
            this.$message.error("请输入分店地址！")
          }else{
            this.givelist.branchList.push({
            name : this.branchAdd.name,
          })
          this.branchAdd.name='';
         }
      },
      branchRemove(index){
        this.$confirm("您正在进行删除操作，您确定要删除该分店地址吗？", '删除分店地址', {
            cancelButtonText: '取消',
            confirmButtonText: '确定',
            showClose:false,
          }).then(() => {
            this.givelist.branchList.splice(index, 1);
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
      },
      cityClicked(index){
        if(this.cityList[index].status==0){
             this.cityList[index].status=1
        }else{
          this.cityList[index].status=0
        }
      },
      featuredClicked(index){
        if(this.featuredList[index].status==0){
             this.featuredList[index].status=1
        }else{
          this.featuredList[index].status=0
        }
      },
      giveVipPopup(){
        this.givelistVipStatus=!this.givelistVipStatus;
      },
      cardClick(){
        this.givelistVipStatus=!this.givelistVipStatus;
        this.givelist.vip="";
      },
      VipStatusToggle(event){
           let dom = this.$refs.sqyPopup;
          if (dom) {
              if (!dom.contains(event.target)) {
                  this.givelistVipStatus = false;
                  this.givelist.vip="";
              }
          }
        },
      vipTwoMeanVal(){
          this.vipTwo.vipTwoMean=this.vipTwo.vipTwoVal/this.vipTwo.vipTwoItem;
      },
      vipSubmit(){
          if(this.givelist.vip==1){
            console.log(this.vipOne)
          }else{
            console.log(this.vipTwo)
          }
      },
      onSubmit() {
        console.log(this.givelist);
      }
    },
    components: {
      sqyUpload
  }
}
</script>
<style scoped>
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to{
  opacity: 0;
}
.give-infor{
  position: relative;
}
.infor-select{
    width:500px;
}
.infor-input{
    width:300px;
}
.infor-imgfile{
    width:150px;
    height:150px;
    border:1px solid #ddd;
    display: inline-block;
    position: relative;
}
.infor-imgfile input{
    width:150px;
    height:100%;
    position: absolute;
    top:0;
    left:0;
    cursor: pointer;
    opacity: 0;
}
.infor-img{
    width:150px;
    height:100%;
    display: inline-block;
    position: absolute;
    left: 0;
}
.infor-preview{
    position: relative;
    top:-12px;
    left:10px;
}
.infor-certifi{
    width:237px;
    margin-right: 20px;
    float: left;
    position: relative;
}
.infor-certifi .infor-imgfile{
  float: left;
}
.infor-certifi-upload .svg-icon{
  width:10em;
  height:10em;
}
.infor-certifi .el-input{
  width:116px;
  margin-top: 7px;
}
.infor-certifi-click{
  position: absolute;
  right:0;
  top:60px;
}
.infor-quote{
  width:300px;
  display: inline-block;
  margin-right: 20px;
  float: left;
}
.infor-quote .el-select{
  width:100%;
  margin-bottom: 6px;
}
.infor-quote-click{
  width:100%;
  height: 40px;
}
.infor-quote-click .el-input{
  width:75px;
}
.infor-quote-click .el-button{
  float: right;
}
.infor-city{
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr 1fr 1fr 1fr 1fr;
}
.infor-city>li{
  font-size:16px;
  font-weight:400;
  color:rgba(76,76,76,1);
  line-height: 16px;
  display: inline-block;
  cursor: pointer;
  height:auto;
  margin: 9px 0;
}
.infor-city>li b{
  font-weight: 400;
}
.infor-featured{
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr 1fr 1fr 1fr 1fr;
}
.infor-featured>li{
  font-size:16px;
  font-weight:400;
  color:rgba(76,76,76,1);
  line-height: 16px;
  display: inline-block;
  cursor: pointer;
  height:auto;
  margin: 9px 0;
}
.infor-featured>li b{
  font-weight: 400;
}
.infor-branch{
  float: left;
  margin-right: 20px;
  margin-bottom: 5px;
}
.infor-submit{
   position: fixed;
   right:0;
   top:50%;
}
.infor-submit .el-button{
  width:120px;
}
.sqy-popup{
  width:550px;
}
</style>
