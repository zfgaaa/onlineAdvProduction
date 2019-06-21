<template>
    <div id="onlineAdvProduction">
        <div class="top">

        </div>
        <div class="bottom">
            <!-- 图层 -->
            <div class="left">
                <div class="title">图层</div>
                <div class="scollContent" ref="content">
                    <div class="scroll_left">
                        <div class="coverage" v-for="(item, index) in coverageList" :key="index" 
                            :class="{coverage_active: selectCoverageIndex == index}" @click="coverageActive(item, index)">
                            <div class="coverage_left">{{index+1}}</div>
                            <div class="coverage_right" :style="{background: item.backgroundColor}">
                                <img class="img" :src="item.img" v-show="item.img" alt="">
                                <!-- 背景图 -->
                                <img class="backgroundImg" :src="item.backgroundImg" v-show="item.backgroundImg" alt="">
                                <div class="coverageText" v-show="item.text">
                                    <p class="aaa">{{item.text}}</p>
                                </div>
                                <!-- 垃圾桶 -->
                                <span class="iconfont iconziyuan" @click="delateCoverageActive(item, index, $event)"></span>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <!-- 中心展示区和编辑区 -->
            <div class="center" @click="clear">
                <div class="center_content" :style="{background: backgroundColor, width: centerWidth, height: centerHeight}">
                    <img :src="backgroundImg" alt="">
                    <clipCase v-show="centerCoverage[0]" v-for="(item, index) in centerCoverage" :key="index" 
                        @click.native="clipCaseActive(index, item, $event)"
                        :_width="item.width"
                        :_height="item.height"
                        :_id="item.id" 
                        :_path="item.img"   
                        :_top="item.top"
                        :_left="item.left"
                        :_isShow="item.show"
                        :_item="item"
                        @getData="getData"></clipCase>
                </div>
            </div>
            <!-- 操作区 -->
            <div class="right">
                <!-- 背景 -->
                <div class="right_item">
                    <!-- 宽高比 -->
                    <div class="scale">
                        <div class="scale_left">
                            <span>宽高比</span>
                        </div>
                        <div class="scale_right">
                            <select v-model="scaleValue">
                                <option v-for="(item, index) in scaleList" :key="index">{{item}}</option>
                            </select>
                        </div>
                    </div>
                    <div class="title">
                        <div class="title_left">
                            <span>背景</span>
                        </div>
                        <div class="title_right">
                            <span v-for="(item, index) in backgroundColorClass" :key="index"
                                :class="{span_active: backgroundColorClassIndex == index}" @click="backgroundColorClassActive(index)">{{item}}</span>
                        </div>
                    </div>
                    <div class="backgroundColor">
                        <li class="backgroundColor_item" v-for="(item, index) in backgroundColorHotColor" :key="index" :style="{background: item}"
                            @click="backgroundColorSelect(item)"></li>
                        <li class="backgroundColor_item iconfont iconmore" v-show="backgroundColorClassIndex == 0" @click="colorIsShow_active"></li>
                    </div>
                    <!-- 图片 -->
                    <div class="img">
                        <div class="img_title">
                            <span>图片</span>
                        </div>
                        <div class="img_content" @click="selectBackground">
                            <img :src="backgroundImg" class="backgroundImg" alt="">
                        </div>
                    </div>
                    <div class="handleBtn">
                        <span @click="changeBackgroundImgActive">更换</span>
                        <!-- <span>裁剪</span> -->
                        <span @click="delateActive" v-show="backgroundImg">删除</span>
                    </div>
                </div>
                <!-- 编辑图片 -->
                <div class="editImg" v-show="false">
                    <div class="title">图片</div>
                    <div class="treeAccordion">
                        <el-collapse  accordion>
                            <el-collapse-item title="图片" name="1">
                                <div class="treeAccordionImg">
                                    <img src="https://ss1.baidu.com/-4o3dSag_xI4khGko9WTAnF6hhy/image/h%3D300/sign=2581bca42f3fb80e13d167d706d02ffb/4034970a304e251fb1a2546da986c9177e3e53c9.jpg" alt="" class="img">
                                </div>
                                <div class="handleBtn">
                                    <span>更换</span>
                                    <span>裁剪</span>
                                </div>
                            </el-collapse-item>
                            <el-collapse-item title="样式" name="2">
                                <!-- 透明度 -->
                                <li class="treeItem">
                                    <span class="text">透明</span>
                                    <input type="text" class="inputOpacity" placeholder="0%">
                                </li>
                                <!-- 颜色 -->
                                <li class="treeItem">
                                    <span class="text">颜色</span>
                                    <div class="colorBox">
                                        <span class="color"></span>
                                    </div>
                                </li>
                                <!-- 圆角 -->
                                <li class="treeItem">
                                    <span class="text">圆角</span>
                                    <input type="text" class="inputOpacity" placeholder="0px">
                                </li>
                                <!-- 边距 -->
                                <li class="treeItem">
                                    <span class="text">边距</span>
                                    <input type="text" class="inputOpacity" placeholder="0px">
                                </li>
                                <!-- 边框 -->
                                <li class="treeItem">
                                    <span class="text">边框</span>
                                    <div class="rightBox">
                                        <select class="inputOpacity">
                                            <option>无</option>
                                            <option>实线</option>
                                            <option>虚线</option>
                                            <option>点线</option>
                                            <option>双线</option>
                                        </select>
                                        <div class="colorBox ">
                                            <span class="color"></span>
                                        </div>
                                        <input type="text" class="inputOpacity" placeholder="0px">
                                    </div>
                                </li>
                                <li class="treeItem">
                                    <span class="text">阴影</span>
                                    <input type="checkbox" name="vehicle"/>
                                </li>
                            </el-collapse-item>
                            <el-collapse-item title="位置" name="3">
                                <li class="treeItem">
                                    <div class="itemLeft">
                                        <span class="text">宽度</span>
                                        <input type="text" placeholder="0px">
                                    </div>
                                    <div class="itemRight">
                                        <span class="text">高度</span>
                                        <input type="text" placeholder="0px">
                                    </div>
                                </li>
                                <li class="treeItem">
                                    <div class="itemLeft">
                                        <span class="text">X</span>
                                        <input type="text" placeholder="0px">
                                    </div>
                                    <div class="itemRight">
                                        <span class="text">Y</span>
                                        <input type="text" placeholder="0px">
                                    </div>
                                </li>
                                <li class="treeItem">
                                    <div class="itemLeft">
                                        <span class="text">旋转</span>
                                        <input type="text" placeholder="0度">
                                    </div>
                                </li>
                                <li class="treeItem">
                                    <div class="itemLeft">
                                        <span class="text">旋转</span>
                                        <span class="btn">原始</span>
                                    </div>
                                </li>
                            </el-collapse-item>
                        </el-collapse>
                    </div>
                </div>
                <ul class="right_nav">
                    <li class="nav_item" v-for="(item, index) in right_nav_item" :key="index" @click="right_nav_active(item, index)">
                        <span :class="item.icon"></span>
                        <span>{{item.text}}</span>
                    </li>
                </ul>
                <!-- 颜色吸取器 -->
                <chrome-picker v-model="colors" v-show="color_isShow" class="colorModel"></chrome-picker>
            </div>
        </div>
        <div class="mask" v-show="color_isShow" @click="color_isShow_active"></div>
        <!-- 图片库 -->
        <div class="mask_img" v-show="imgGallely_isShow"></div>     <!-- 遮罩层 -->
        <div class="imgGallely" v-show="imgGallely_isShow">
            <div class="imgGallely_title">
                <div class="title_nav">
                    <span v-for="(item, index) in imgNav" :key="index"
                        :class="{active: selectImgNavIndex == index}" @click="imgNav_active(index)">{{item}}</span>
                </div>
                <div class="icon" @click="cancelActive">
                    <span class="iconfont iconziyuanldpi"></span>
                </div>
            </div>
            <!-- 我的图文件 -->
            <div class="imgGallelyCon" v-show="selectImgNavIndex == 0">
                <div class="content">
                    <div class="imgBox" v-for="(item, index) in fileList" :key="item.id" @click="myImgActive(item, index)">
                        <span class="iconfont iconcuo" @click="del_active(index)"></span>
                        <img :src="item.path" alt="" ref="myImg">
                    </div>
                </div>
                <form action="" enctype="multipart/form-data">
                    <input type="file" name="file" id="file" @change="changepic(this)" class="inputfile" /> 
                    <label for="file" class='uploadBtn'>点击上传</label>
                </form>
            </div>
            <!-- 素材库 -->
            <div class="imgGallelyCon" v-show="selectImgNavIndex == 1">
                <div class="content">
                    <div class="imgBox" v-for="(item, index) in fodders" :key="item.id" @click="selectFoddersAcitve(item, index)">
                        <img :src="item.path" alt="" ref="gallelyImg">
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import BScroll from 'better-scroll'
import { Chrome } from 'vue-color'
import { watch } from 'fs';
import clipCase from '@/components/clipCase/clipCase.vue'

export default {
    components: {
        'chrome-picker': Chrome,
        clipCase,
    },

    data(){
        return {
            // 图层样式
            coverageStyle: {
                width: '',
                height: '',
                top: '',
                left: ''
            },


            // 中间区域样式
            backgroundColor: '#fff',
            centerWidth: '314px',
            centerHeight: '558px',
            // 中间区域图层
            centerCoverage: [
            ],

            backgroundImg: '',
            selectCoverageIndex: 0,
            selectNavIndex: 0,
            color_isShow: false,
            // 宽高比
            scaleList: ['9 : 16', '3 : 4'],
            scaleValue: '9 : 16',
            fileList: [],
            imgNav: ['我的', '素材库'],
            selectImgNavIndex: 0,
            right_nav_item: [
                {icon: 'iconfont iconbackground', text: '背景'},
                {icon: 'iconfont icontupian', text: '图片'},
                {icon: 'iconfont iconwenzi', text: '文字'},
            ],
            backgroundColorClass: ['纯色', '渐变'],
            backgroundColorClassIndex: 0,
            backgroundColorHotColor: [],
            // 背景颜色热门颜色  ======   纯色
            aaa: ['#fff', '#37bc9b', '#3bafda', '#4a89dc', '#0076ff', '#8cc152', '#f6bb42', '#f47983', '#da4453', '#e9573f', '#967adc', '#a8b0bc', '#27282a'],
            // 背景颜色热门颜色  ======   渐变
            bbb: ["-webkit-linear-gradient(#a7fdfb, #c0f4d8)",
                "-webkit-linear-gradient(#faeebf, #5abac7)",
                "-webkit-linear-gradient(#a5de61, #56ab2f)",
                "-webkit-linear-gradient(#00c5ff, #0073ff)",
                "-webkit-linear-gradient(#fcd5d5, #ab96ee)",
                "-webkit-linear-gradient(#e2a4ff, #756efc)",
                "-webkit-linear-gradient(#ffb2a1, #f14193)",
                "-webkit-linear-gradient(#f6c724, #e13437)",
                "-webkit-linear-gradient(#eed09d, #f0be41)",
                "-webkit-linear-gradient(#e9cfa2, #735a43)",
                "-webkit-linear-gradient(#dfbb74, #7c5d2f)",
                "-webkit-linear-gradient(#2a4e83, #091628)",
                "-webkit-linear-gradient(#69767e, #1a0b08)",
                "-webkit-linear-gradient(#dddddd, #3e3f3f)"],

            colors: {
                hex: '#194d33',
                hsl: { h: 150, s: 0.5, l: 0.2, a: 1 },
                hsv: { h: 150, s: 0.66, v: 0.30, a: 1 },
                rgba: { r: 25, g: 77, b: 51, a: 1 },
                a: 1
            },
            imgGallely_isShow: false,
            sum: 0,
            // 素材
            fodders: [
                { id: 0, path: 'https://ss1.baidu.com/-4o3dSag_xI4khGko9WTAnF6hhy/image/h%3D300/sign=2581bca42f3fb80e13d167d706d02ffb/4034970a304e251fb1a2546da986c9177e3e53c9.jpg' },
                { id: 1, path: 'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1561450982&di=1392710b9224eef36958e5a3d1169923&imgtype=jpg&er=1&src=http%3A%2F%2Fpic18.nipic.com%2F20120107%2F7775331_121009264124_2.jpg' },
                { id: 2, path: 'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1560856263475&di=500216a460365a644c4ddd190fab4f8a&imgtype=0&src=http%3A%2F%2Fimg5q.duitang.com%2Fuploads%2Fblog%2F201404%2F02%2F20140402160355_Sk4Pm.jpeg' },
                { id: 3, path: 'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1560856263475&di=e382e56e5b0edd01615afffe5735fcd0&imgtype=0&src=http%3A%2F%2Fimg5.pcpop.com%2Fbizhi%2Fbig%2F10%2F060%2F986%2F10060986.jpg' },
                { id: 4, path: 'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1560856263475&di=e382e56e5b0edd01615afffe5735fcd0&imgtype=0&src=http%3A%2F%2Fimg5.pcpop.com%2Fbizhi%2Fbig%2F10%2F060%2F986%2F10060986.jpg' },
                { id: 5, path: 'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1560856263475&di=e382e56e5b0edd01615afffe5735fcd0&imgtype=0&src=http%3A%2F%2Fimg5.pcpop.com%2Fbizhi%2Fbig%2F10%2F060%2F986%2F10060986.jpg' },
                { id: 6, path: 'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1560856263475&di=e382e56e5b0edd01615afffe5735fcd0&imgtype=0&src=http%3A%2F%2Fimg5.pcpop.com%2Fbizhi%2Fbig%2F10%2F060%2F986%2F10060986.jpg' },
                { id: 7, path: 'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1560856263475&di=e382e56e5b0edd01615afffe5735fcd0&imgtype=0&src=http%3A%2F%2Fimg5.pcpop.com%2Fbizhi%2Fbig%2F10%2F060%2F986%2F10060986.jpg' },
            ],
            isClick: true,
            // 图层列表
            coverageList: [
                // {text: '', backgroundImg: '', img: ''}
            ],
            _coverageList: []
        }
    },

    methods: {
        getData(item){
            let id = item.id
            for(var i = 0; i < this.coverageList.length; i++){
                if(this.coverageList[i].id == id){
                    this.coverageList[i] = item
                }
            }
            this.centerCoverage = this.coverageList
        },

        coverageActive(item, index){
            this.selectCoverageIndex = index
            this.hide()
            this.coverageList[index].show = true
            console.log('leftIndex =' + index)
        },
        right_nav_active(item, index){
            this.selectNavIndex = index
            if(index == 1){
                this.imgGallely_isShow = true
            }
        },
        backgroundColorClassActive(index){
            this.backgroundColorClassIndex = index
            if(this.backgroundColorClassIndex == 0){
                this.backgroundColorHotColor = this.aaa
            }else if(this.backgroundColorClassIndex == 1){
                this.backgroundColorHotColor = this.bbb 
            }
        },
        // 背景颜色选择
        backgroundColorSelect(item){
            this.backgroundColor = item
            // this.coverageList[0].backgroundColor = item
        },
        colorIsShow_active(){
            this.color_isShow = !this.color_isShow
        },
        color_isShow_active(){
            this.color_isShow = false
        },
        cancelActive(){
            this.imgGallely_isShow = false
        },
        // 上传图片并预览
        changepic(){
            var reads = new FileReader();
            let self = this
            var f = document.getElementById('file').files[0];
            reads.readAsDataURL(f);
            reads.onload=function (e) {
                self.sum = self.sum + 1;
                let obj = {id: '', path: ''};
                obj.id = self.sum;
                console.log(this)
                obj.path = this.result; 
                self.fileList.push(obj)
            };
        },
        // 删除指定的元素
        del_active(index){
            this.fileList.splice(index, 1);
        },
        imgNav_active(index){
            this.selectImgNavIndex = index;
        },
        // 素材选择
        selectFoddersAcitve(item, index){
            
            if(this.selectNavIndex == 1){
                // 获得宽高
                let width = this.$refs.gallelyImg[index].width * 2;
                let height = this.$refs.gallelyImg[index].height * 2;

                this.img = item.path
                this.hide()
                let obj = { 
                    id: item.id, 
                    text: '', 
                    img: item.path, 
                    show: true,
                    width: width + "px",
                    height: height + 'px',
                    top: '',
                    left: '' 
                }
                // console.log(obj)
                this.coverageList.push(obj)
                this.imgGallely_isShow = false;
            }else{
                this.backgroundImg = item.path;  
                // this.coverageList[0].backgroundImg = item.path
                this.imgGallely_isShow = false;
                // 如果没有背景图片则可以点击  否则反之
                if(this.backgroundImg == ''){
                    this.isClick = true;
                }else{
                    this.isClick = false
                } 
            }

            this.centerCoverage = this.coverageList
        },
        // 选择背景图片
        selectBackground(){
            this.selectNavIndex = 0
            console.log(this.isClick)
            if(this.isClick == true){
                this.imgGallely_isShow = true
            }else{
                return
            }
        },

        // 我的图片库选择
        myImgActive(item, index){
            if(this.selectNavIndex == 1){
                // 获得宽高
                let width = this.$refs.myImg[index].width * 2;
                let height = this.$refs.myImg[index].height * 2;
                this.hide()
                this.img = item.path
                let obj = { 
                    id: index, 
                    text: '', 
                    img: item.path, 
                    show: true,
                    width: width + "px",
                    height: height + 'px',
                    top: '',
                    left: '' 
                }
                console.log(obj)
                this.coverageList.push(obj)
            }else{
                this.backgroundImg = item.path
            }
            this.imgGallely_isShow = false;
            this.centerCoverage = this.coverageList
        },

        // 点击更改背景图
        changeBackgroundImgActive(){
            this.selectNavIndex = 0
            this.imgGallely_isShow = true
        },
        // 点击删除
        delateActive(){
            this.isClick = true
            this.backgroundImg = ''
            // this.coverageList[0].backgroundImg = ''
        },

        // 将宽高比例转变到样式中
        changeScale(width, scale){
            let _width = parseFloat(width)   //  9 / 16  ==   width / height
            let scaleWidth = parseInt(scale.split(':')[0])
            let scaleHeight = parseInt(scale.split(':')[1])
            let height = parseInt(scaleHeight * _width / scaleWidth)
            return height
        },

        // 单击中间显示区域的图层
        clipCaseActive(index, item, e){
            this.hide()
            item.show = true
            // 阻止事件冒泡
            e.stopPropagation(); 
        },

        // 隐藏所有剪切框
        clear(e){
            this.hide()
        },

        // 图层删除键
        delateCoverageActive(item, index, e){
            var ev = e || event;
            ev.cancelBubble=true;
            ev.returnValue = false;

            this.coverageList.splice(index, 1)
        },

        // 隐藏剪切框
        hide(){
            for(var i = 0; i <= this.coverageList.length; i++){
                if(this.coverageList[i] == undefined){
                    return
                }else{
                    this.coverageList[i].show = false
                }
            }
        }
    },

    mounted(){
        let _BScroll = new BScroll(this.$refs.content, {
            scrollX: false,
            scrollY: true
        })
        this.backgroundColorHotColor = this.aaa
    },

    created(){
        this.centerHeight = this.changeScale(this.centerWidth, this.scaleValue) + 'px';  
        // let arr = localStorage.getItem('coverageData') 
        // if(arr){
        //     this.coverageList = JSON.parse(arr)
        //     this.centerCoverage = JSON.parse(arr)
        // }else{
        //     return
        // }
    },

    watch: {
        "colors": function(){
            this.backgroundColor = this.colors.hex
            this.coverageList[0].backgroundColor = this.colors.hex
        },

        'scaleValue': function(){
            this.centerHeight = this.changeScale(this.centerWidth, this.scaleValue) + 'px'
        },
        
        "backgroundImg": function(){
            if(this.backgroundImg == ''){
                this.isClick = true
            }else{
                this.isClick = false
            }
        },
        "coverageList": function(old, from){
            // let arr = JSON.stringify(this.coverageList)
            // localStorage.setItem('coverageData', arr)
        }
    }
}
</script>

<style lang="scss" scoped>
#onlineAdvProduction{
    width: 100%;
    height: 100%;
    .top{
        height: 44px;
        width: 100%;
        background-color: #000;
    }
    .bottom{
        position: absolute;
        top: 44px;
        left: 0;
        bottom: 0;
        width: 100%;
        display: flex;
        .left{
            position: relative;
            width: 152px;
            background-color: #505050;
            height: 100%;
            border-right: 1px solid #000;
            .title{
                color: #fff;
                line-height: 30px;
                text-align: center;
            }
            .scollContent{
                position: absolute;
                top: 30px;
                left: 0;
                bottom: 0;
                width: 100%;
                overflow: hidden;
                .coverage{
                    position: relative;
                    width: 100%;
                    height: 150px;
                    color: #fff;
                    .coverage_left{
                        position: absolute;
                        top: 50%;
                        transform: translateY(-50%);
                        left: 12px;
                    }
                    .coverage_right{
                        position: absolute;
                        left:0;
                        right:0;
                        top: 0;
                        bottom: 0;
                        margin: auto;
                        width: 84px;
                        height: 132px;
                        background-color: #fff;
                        background-size: 100%;
                        .img{
                            max-width: 70px;
                            max-height: 112px;
                            position: absolute;
                            left:0;
                            right:0;
                            top: 0;
                            bottom: 0;
                            margin: auto;
                        }
                        .backgroundImg{
                            width: 100%;
                            height: 100%;
                        }
                        .coverageText{
                            width: 70px;
                            position: absolute;
                            left:0;
                            right:0;
                            top: 0;
                            bottom: 0;
                            margin: auto;
                            padding-top: 20px;
                            p.aaa{
                                color: #000;
                                font-size: 7px;
                                line-height: 12px;
                            }
                        }
                        .iconfont{
                            position: absolute;
                            color: #ccc;
                            right: -24px;
                            top: 50%;
                            transform: translateY(-50%);
                        }
                    }
                }
                .coverage_active{
                    background-color: #666666;
                }
            }
        }
        .center{
            flex: 1;
            background-color: #505050;
            position: relative;
            .center_content{
                position: absolute;
                left: 50%;
                transform: translateX(-50%);
                top: 50px;
                width: 314px;
                height: 558px;
                background-color: #fff;
                img{
                    width: 100%;
                    height: 100%;
                }
            }
        }
        .right{
            width: 350px;
            height: 100%;
            background-color: #eee;
            display: flex;
            // 背景
            .right_item{
                width: 270px;
                height: 100%;
                padding: 10px;
                .scale{
                    width: 100%;
                    height: 24px;
                    display: flex;
                    margin-bottom: 10px;
                    justify-content: space-between;
                    .scale_left{
                        line-height: 24px;
                        span{
                            font-size: 12px;
                        }
                    }
                }
                .title{
                    width: 100%;
                    height: 24px;
                    display: flex;
                    justify-content: space-between;
                    .title_left{
                        line-height: 24px;
                        span{
                            font-size: 12px;
                        }
                    }
                    .title_right{
                        display: flex;
                        span{
                            display: block;
                            width: 72px;
                            height: 24px;
                            line-height: 22px;
                            text-align: center;
                            background-color: #fff;
                            border: 1px solid #626262;
                            font-size: 12px;
                        }
                        span:first-child{
                            border-radius: 4px 0 0 4px;
                            border-right: 0;
                        }
                        span:last-child{
                            border-radius: 0 4px 4px 0;
                        }
                        .span_active{
                            background-color: #626262;
                            color: #fff;
                        }
                    }
                }
                .backgroundColor{
                    width: 100%;
                    display: flex;
                    flex-wrap: wrap;
                    padding-top: 20px;
                    .backgroundColor_item{
                        width: 24px;
                        height: 24px;
                        border-radius: 50%;
                        margin-right: 10px;
                        margin-bottom: 10px;
                        line-height: 24px;
                        text-align: center;
                        font-size: 18px;
                        cursor:pointer;
                    }
                }
                .img{
                    .img_title{
                        margin-bottom: 10px;
                        font-size: 14px;
                    }
                    .img_content{
                        width: 100%;
                        height: 120px;
                        background-color: #fff;
                        position: relative;
                        img{
                            position: absolute;
                            height: 100%;
                            top: 0;
                            left: 0;
                            bottom: 0;
                            right: 0;
                            margin: auto;
                        }
                    }
                }
                .handleBtn{
                    display: flex;
                    margin-top: 10px;
                    span{
                        display: block;
                        width: 48px;
                        height: 24px;
                        background-color: #626262;
                        color: #fff;
                        border-radius: 4px;
                        text-align: center;
                        line-height: 24px;
                        font-size: 14px;
                        margin-right: 14px;
                        cursor: pointer;
                    }
                    span:hover{
                        opacity: 0.8;
                    }
                }
            }
            // 图片
            .editImg{
                width: 270px;
                height: 100%;
                .title{
                    width: 100%;
                    height: 32px;
                    text-align: center;
                    line-height: 32px;
                    background-color: #6b6b6b;
                    font-size: 14px;
                    color: #fff;
                }
                .treeAccordion{
                    width: 100%;
                    background-color: #fff;
                    .el-collapse-item{
                        padding: 0 10px;
                    }
                    .treeAccordionImg{
                        position: relative;
                        width: 100%;
                        height: 120px;
                        background-color: #f5f6f8;
                        img{
                            position: absolute;
                            height: 100%;
                            top: 0;
                            left: 0;
                            bottom: 0;
                            right: 0;
                            margin: auto;
                        }
                    }
                    .handleBtn{
                        display: flex;
                        margin-top: 10px;
                        span{
                            display: block;
                            width: 48px;
                            height: 24px;
                            background-color: #626262;
                            color: #fff;
                            border-radius: 4px;
                            text-align: center;
                            line-height: 24px;
                            font-size: 14px;
                            margin-right: 14px;
                            cursor: pointer;
                        }
                        span:hover{
                            opacity: 0.8;
                        }
                    }
                    .treeItem{
                        padding: 0 10px;
                        display: flex;
                        margin-bottom: 10px;
                        justify-content: space-between;
                        .inputOpacity{
                            width: 60px;
                            height: 24px;
                            border: 0;
                            border-radius: 4px;
                            text-align: center;
                            border: 1px solid #ccc;
                            font-size: 14px;
                        }
                        select{
                            -webkit-tap-highlight-color: rgba(0,0,0,0);
                        }
                        
                        .colorBox{
                            width: 60px;
                            height: 24px;
                            border: 1px solid #ccc;
                            border-radius: 4px;
                            position: relative;
                            .color{
                                display: inline-block;
                                position: absolute;
                                top: 0;
                                left: 0;
                                bottom: 0;
                                right: 0;
                                margin: auto;
                                border-radius: 4px;
                                width: 50px;
                                height: 16px;
                                background-color: red;
                            }
                        }

                        .itemLeft, .itemRight{
                            display: flex;
                            width: 50%;
                            .text{
                                display: inline-block;
                                width: 25%;
                                margin-right: 10px;
                            }
                            input{
                                width: 60px;
                                height: 24px;
                                border: 0;
                                border-radius: 4px;
                                text-align: center;
                                border: 1px solid #ccc;
                                font-size: 14px;
                            }
                            .btn{
                                display: inline-block;
                                width: 60px;
                                height: 24px;
                                border-radius: 4px;
                                text-align: center;
                                font-size: 14px;
                                line-height: 24px;
                                color: #fff;
                                background-color: #626262;
                            }
                        }
                        .itemRight{
                            display: flex;
                            width: 50%;
                        }
                    }
                    .treeItem:nth-child(5){
                        .rightBox{
                            display: flex;
                            .colorBox{
                                width: 40px;
                                .color{
                                    width: 30px;
                                }
                            }
                        }
                    }
                }
            }
            .right_nav{
                width: 80px;
                height: 100%;
                padding: 20px 10px 0 10px;
                background-color: #463f3f;
                .nav_item{
                    width: 100%;
                    height: 36px;
                    line-height: 34px;
                    text-align: center;
                    border-radius: 12px;
                    background-color: #fff;
                    margin-bottom: 20px;
                    span{
                        font-size: 14px;
                    }
                    span.iconfont{
                        margin-right: 6px;
                    }
                }
                .nav_item:first-child{
                    background-color: rgb(87, 87, 231);
                    color: #fff;
                }
            }
            .colorModel{
                position: absolute;
                top: 120px;
                right: 20px;
                z-index: 1000;
            }
        }
    }
    .mask{
        position: absolute;
        top: 0;
        left: 0;
        bottom: 0;
        width: 100%;
        z-index: 999;
    }
    .mask_img{
        position: absolute;
        top: 0;
        left: 0;
        bottom: 0;
        width: 100%;
        z-index: 999;
        background-color: rgba($color: #000000, $alpha: 0.5);
    }
    .imgGallely{
        position: absolute;
        top: 0;
        left: 0;
        bottom: 0;
        right: 0;
        margin: auto;
        width: 670px;
        height: 534px;
        background-color: #fff;
        z-index: 1000;
        .imgGallely_title{
            width: 100%;
            height: 46px;
            border-bottom: 1px solid #dcdcdc;
            display: flex;
            background-color: #eee;
            justify-content: space-between;
            .title_nav{
                display: flex;
                span{
                    display: block;
                    width: 100px;
                    text-align: center;
                    line-height: 46px;
                    font-size: 14px;   
                }
                .active{
                    background-color: #fff;
                }
            }
            .icon{
                line-height: 45px;
                margin-right: 10px;
                span{
                    font-size: 12px;
                    color: #ababab;
                }
            }
        }
        .imgGallelyCon{
            width: 100%;
            position: relative;
            height: 488px;
            padding: 15px;
            .imgBox{
                position: relative;
                display: inline-block;
                margin-right: 20px;
                width: 90px;
                height: 90px;
                margin-bottom: 18px;
                background-color: #ccc;
                .iconfont.iconcuo{
                    position: absolute;
                    right: 0;
                    top: 0;
                    color: #fff;
                    top: 2px;
                    font-size: 18px;
                    z-index: 10;
                    cursor: pointer;
                    display: none;
                }
                img{
                    position: absolute;
                    top: 0;
                    left: 0;
                    bottom: 0;
                    right: 0;
                    margin: auto;
                    max-width: 100%;
                    max-height: 100%;
                }
            }
            .inputfile{
                opacity: 0;
            }
            .uploadBtn{
                position: absolute;
                width: 74px;
                height: 30px;
                color: #fff;
                background-color: #33aaff;
                border-radius: 4px;
                text-align: center;
                line-height: 30px;
                font-size: 14px;
                right: 15px;
                bottom: 15px;
            }
            .imgBox:nth-child(6n){
                margin-right: 0;
            }
            .imgBox:hover .iconcuo{
                display: block;
            }
        }
    }
}
</style>

