<template>
    <div class="clipCase" ref="clipCase" @mousedown="mouseDownBoxActive" 
        :style="{width: width, height: height, top: top, left: left, transform: deg}" :class="{border: _isShow == true}"
        @click="saveData">
        <div class="minBox left-up" ref="leftUp" @mousedown="moveLeftUpActive" v-show="_isShow"></div>
        <div class="minBox up" ref="up" @mousedown="moveUpActive" v-show="_isShow"></div>
        <div class="minBox right-up" ref="rightUp" @mousedown="moveRightUpActive" v-show="_isShow"></div>
        <div class="minBox left" ref="left" @mousedown="moveLeftActive" v-show="_isShow"></div>
        <div class="minBox right" ref="right" @mousedown="moveRightActive" v-show="_isShow"></div>
        <div class="minBox left-down" ref="leftDown" @mousedown="moveLeftDownActive" v-show="_isShow"></div>
        <div class="minBox down" ref="down" @mousedown="moveDownActive" v-show="_isShow"></div>
        <div class="minBox right-down" ref="rightDown" @mousedown="moveRightDownActive" v-show="_isShow"></div>
        <div class="rotate" ref="rotate" @mousedown="moveRotateActive" v-show="_isShow"></div>

        <!-- 剪切框内容 -->
        <div class="content">
            <img :src="_path" alt="">
        </div>
    </div>
</template>

<script>
import interact from 'interactjs'
export default {
    name: 'clipCase',
    props: {
        _width: String,
        _height:String,
        _top: String,
        _left: String,
        _isShow: Boolean,

        // _width: {
        //     default: '150px'
        // },
        // _height: {
        //     default: '150px'
        // },
        // _top: {
        //     default: '500px'
        // },
        // _left: {
        //     default: '50px'
        // },
        // _rotate: {
        //     default: false
        // },
        _path: String,
        // _isShow: {
        //     default: true
        // },
        _id: Number,
        _item: Object
    },
    data(){
        return {
            width: this._width,
            height: this._height,
            top: this._top,
            left: this._left,
            // rotate: this._rotate,
            path: this._path,
            show: this._isShow,
            coverageId: this._id,
            offsetBottom: '',
            offsetRight: '',
            item: this._item,
            deg: "rotate(0deg)",
            center: {
                x: 0,
                y: 0
            },
            origin: {
                x: 0,
                y: 0
            }
        }
    },
    methods: {
        // 鼠标按下,拖动剪切框
        mouseDownBoxActive(event){
            var clipCase = this.$refs.clipCase
            var event = event || window.event;  //兼容IE浏览器
            //鼠标点击物体那一刻相对于物体左侧边框的距离=点击时的位置相对于浏览器最左边的距离-物体左边框相对于浏览器最左边的距离
            var diffX = event.clientX - clipCase.offsetLeft;
            var diffY = event.clientY - clipCase.offsetTop;
            var self = this
            if(typeof clipCase.setCapture !== 'undefined'){
                clipCase.setCapture(); 
            }
            document.onmousemove = function(e){
                var event = e || window.event;
                // 鼠标移动的坐标   左移上移为负    
                var moveX = event.clientX - diffX;
                var moveY = event.clientY - diffY;
                var ev = e || event;
                ev.cancelBubble=true;
                ev.returnValue = false;
                if(moveX < 0){
                    moveX = 0
                }else if(moveX > clipCase.parentNode.offsetWidth - clipCase.offsetWidth){
                    moveX = clipCase.parentNode.offsetWidth - clipCase.offsetWidth
                }
                if(moveY < 0){
                    moveY = 0
                }else if(moveY > clipCase.parentNode.offsetHeight - clipCase.offsetHeight){
                    moveY =  clipCase.parentNode.offsetHeight - clipCase.offsetHeight
                }
                clipCase.style.left = moveX + 'px';
                clipCase.style.top = moveY + 'px'

                event.stopPropagation(); 
            }
            document.onmouseup = function(event){
                // 剪切框底部离其父级底部的高度
                self.offsetBottom = clipCase.parentNode.offsetHeight - clipCase.offsetHeight - clipCase.offsetTop;
                self.offsetRight = clipCase.parentNode.offsetWidth - clipCase.offsetWidth - clipCase.offsetLeft
                self.top = self.$refs.clipCase.style.top
                self.left = self.$refs.clipCase.style.left

                // 中心点坐标
                self.getCenter()
                this.onmousemove = null;
                this.onmouseup = null;
                 //修复低版本ie bug  
                if(typeof clipCase.releaseCapture!='undefined'){  
                   clipCase.releaseCapture();  
                }  
            }
                event.stopPropagation(); 

        },

        // 单击顶部触点
        moveUpActive(){
            var clipCase = this.$refs.clipCase
            // 剪切框的父级
            var clipCaseParent = clipCase.parentNode
            var parentHeight = clipCaseParent.offsetHeight;
            var right = this.$refs.right;
            var self = this
            // 鼠标按下时   鼠标离浏览器顶部的距离
            var startClientY= event.clientY
            window.onmousemove = function(event){
                var y = startClientY - event.clientY
                clipCase.style.height = parseFloat(self.height) + y + "px"
                clipCase.style.top = parseFloat(self.top) - y + "px"
                if(clipCase.offsetTop <= 0){
                    clipCase.style.top = 0;
                    clipCase.style.height = clipCase.parentNode.offsetHeight - self.offsetBottom + 'px'
                }
                if(clipCase.offsetHeight - 2 <= 0){
                    clipCase.style.top = clipCase.parentNode.offsetHeight - self.offsetBottom + 'px'
                }
                event.stopPropagation(); 
            }
            window.onmouseup = function(event){
                self.height = self.$refs.clipCase.style.height
                self.top = self.$refs.clipCase.style.top
                self.getCenter()
                this.onmousemove = null;
                this.onmouseup = null;
            }
            event.stopPropagation(); 
        },

        // 单击右上角触点
        moveRightUpActive(){
            var clipCase = this.$refs.clipCase
            // 鼠标按下时相对于浏览器的坐标
            var x = event.clientX
            var y = event.clientY
            var self = this
            window.onmousemove = function(event){
                var moveX = event.clientX - x
                var moveY = event.clientY - y
                clipCase.style.top = parseFloat(self.top) + moveY + 'px'
                clipCase.style.width = parseFloat(self.width) + moveX + 'px'
                clipCase.style.height = parseFloat(self.height) - moveY + 'px'
                
                if(parseFloat(clipCase.style.top) <= 0){
                    clipCase.style.top = 0
                    clipCase.style.height = clipCase.parentNode.offsetHeight - self.offsetBottom + 'px'
                }else if(parseFloat(clipCase.style.top) >= clipCase.parentNode.offsetHeight - self.offsetBottom){
                    clipCase.style.top = clipCase.parentNode.offsetHeight - self.offsetBottom + 'px'
                }

                if(clipCase.offsetLeft + clipCase.offsetWidth >= clipCase.parentNode.offsetWidth ){
                    clipCase.style.width = clipCase.parentNode.offsetWidth - clipCase.offsetLeft + 'px'
                }
            }
            window.onmouseup = function(){
                self.top = clipCase.style.top
                self.left = clipCase.style.left
                self.width = clipCase.style.width
                self.height = clipCase.style.height
                self.getCenter()
                this.onmousemove = null;
                this.onmouseup = null;
            }
            event.stopPropagation();
        },

        // 单击右侧触点
        moveRightActive(){
            var clipCase = this.$refs.clipCase
            // 剪切框的父级
            var clipCaseParent = clipCase.parentNode
            var parentWidth = clipCaseParent.offsetWidth;
            var right = this.$refs.right;
            var self = this
            // 鼠标按下时   鼠标离浏览器左侧的距离
            var startClientX = event.clientX
            window.onmousemove = function(event){
                var x = startClientX - event.clientX
                clipCase.style.width = parseFloat(self.width) - x + "px"
                // 鼠标移动后到其父级左侧边框的距离
                var a = event.clientX - clipCase.parentNode.offsetLeft + 4
                if(a > clipCase.parentNode.offsetWidth){
                    clipCase.style.width = clipCase.parentNode.offsetWidth - clipCase.offsetLeft + "px"
                }
                event.stopPropagation(); 
            }
            window.onmouseup = function(event){
                self.offsetRight = clipCase.parentNode.offsetWidth - clipCase.offsetWidth - clipCase.offsetLeft
                self.width = self.$refs.clipCase.style.width
                self.getCenter()
                this.onmousemove = null;
                this.onmouseup = null;
            }
            event.stopPropagation(); 
        },

        // 单击右下触点
        moveRightDownActive(){
            var clipCase = this.$refs.clipCase
            // 鼠标按下时相对于浏览器的坐标
            var x = event.clientX
            var y = event.clientY
            var self = this
            window.onmousemove = function(event){
                var moveX = event.clientX - x
                var moveY = event.clientY - y
                clipCase.style.width = parseFloat(self.width) + moveX + 'px'
                clipCase.style.height = parseFloat(self.height) + moveY + 'px'
                
                if(clipCase.offsetLeft + clipCase.offsetWidth >= clipCase.parentNode.offsetWidth){
                    clipCase.style.width = clipCase.parentNode.offsetWidth - clipCase.offsetLeft + 'px'
                }
                if(clipCase.offsetTop + clipCase.offsetHeight >= clipCase.parentNode.offsetHeight){
                    clipCase.style.height = clipCase.parentNode.offsetHeight - clipCase.offsetTop + 'px'
                }
                
            }
            window.onmouseup = function(){
                self.top = clipCase.style.top
                self.left = clipCase.style.left
                self.width = clipCase.style.width
                self.height = clipCase.style.height
                self.getCenter()
                this.onmousemove = null;
                this.onmouseup = null;
            }
            event.stopPropagation(); 
        },

        // 单击底部触点
        moveDownActive(){
            var clipCase = this.$refs.clipCase
            // 剪切框的父级
            var clipCaseParent = clipCase.parentNode
            var parentHeight = clipCaseParent.offsetHeight;
            var right = this.$refs.right;
            var self = this
            // 鼠标按下时   鼠标离浏览器顶部的距离
            var startClientY= event.clientY
            window.onmousemove = function(event){
                // 鼠标移动后到其父级顶部边框的距离
                var y = startClientY - event.clientY
                clipCase.style.height = parseFloat(self.height) - y + "px"
                var a = clipCase.offsetTop + clipCase.offsetHeight
                if(a >= clipCase.parentNode.offsetHeight){
                    clipCase.style.top = clipCase.offsetTop
                    clipCase.style.height = clipCase.parentNode.offsetHeight - clipCase.offsetTop + 'px'
                }
            }
            window.onmouseup = function(event){
                self.offsetBottom = clipCase.parentNode.offsetHeight - clipCase.offsetHeight - clipCase.offsetTop;
                self.height = self.$refs.clipCase.style.height
                self.getCenter()
                this.onmousemove = null;
                this.onmouseup = null;
            }
            event.stopPropagation(); 
        },

        // 单击左下角触点
        moveLeftDownActive(){
            var clipCase = this.$refs.clipCase
            // 鼠标按下时相对于浏览器的坐标
            var x = event.clientX
            var y = event.clientY
            var self = this
            window.onmousemove = function(event){
                var moveX = event.clientX - x
                var moveY = event.clientY - y
                clipCase.style.left = parseFloat(self.left) + moveX + 'px'
                clipCase.style.width = parseFloat(self.width) - moveX + 'px'
                clipCase.style.height = parseFloat(self.height) + moveY + 'px'
                if(parseFloat(clipCase.style.top) <= 0){
                    clipCase.style.top = 0
                }
                if(clipCase.offsetTop + clipCase.offsetHeight >= clipCase.parentNode.offsetHeight){
                    clipCase.style.height = clipCase.parentNode.offsetHeight - clipCase.offsetTop + 'px'
                }

                if(parseFloat(clipCase.style.left) <= 0){
                    clipCase.style.left = 0
                    clipCase.style.width = clipCase.parentNode.offsetWidth - self.offsetRight + 'px'
                }else if(parseFloat(clipCase.style.left) >= clipCase.parentNode.offsetWidth - self.offsetRight){
                    clipCase.style.left = clipCase.parentNode.offsetWidth - self.offsetRight + 'px'
                }
            }
            window.onmouseup = function(){
                self.top = clipCase.style.top
                self.left = clipCase.style.left
                self.width = clipCase.style.width
                self.height = clipCase.style.height
                self.getCenter()
                this.onmousemove = null;
                this.onmouseup = null;
            }
            event.stopPropagation(); 
        },

        // 单击左侧触点
        moveLeftActive(){
            var clipCase = this.$refs.clipCase
            // 剪切框的父级
            var clipCaseParent = clipCase.parentNode
            var parentWidth = clipCaseParent.offsetWidth;
            var right = this.$refs.right;
            var self = this
            // 鼠标按下时   鼠标离浏览器左侧的距离
            var startClientX = event.clientX
            window.onmousemove = function(event){
                var x = startClientX - event.clientX
                clipCase.style.width = parseFloat(self.width) + x + "px"
                clipCase.style.left = parseFloat(self.left) - x + "px"

                if(clipCase.offsetLeft <= 0){
                    clipCase.style.left = 0
                    clipCase.style.width = clipCase.parentNode.offsetWidth - self.offsetRight + 'px'
                }
                if(clipCase.offsetWidth - 2 <= 0){
                    clipCase.style.left = clipCase.parentNode.offsetWidth - self.offsetRight + 'px'
                }
                event.stopPropagation(); 
            }
            window.onmouseup = function(){
                self.width = self.$refs.clipCase.style.width
                self.left = self.$refs.clipCase.style.left
                self.getCenter()
                this.onmousemove = null;
                this.onmouseup = null;
            }
            event.stopPropagation(); 
        },

        // 单击左上角触点
        moveLeftUpActive(){
            var clipCase = this.$refs.clipCase
            // 鼠标按下时相对于浏览器的坐标
            var x = event.clientX
            var y = event.clientY
            var self = this
            window.onmousemove = function(event){
                var moveX = event.clientX - x
                var moveY = event.clientY - y
                clipCase.style.top = parseFloat(self.top) + moveY + 'px'
                clipCase.style.left = parseFloat(self.left) + moveX + 'px'
                clipCase.style.width = parseFloat(self.width) - moveX + 'px'
                clipCase.style.height = parseFloat(self.height) - moveY + 'px'
                
                if(parseFloat(clipCase.style.top) <= 0){
                    clipCase.style.top = 0
                    clipCase.style.height = clipCase.parentNode.offsetHeight - self.offsetBottom + 'px'
                }else if(parseFloat(clipCase.style.top) + 2 >= clipCase.parentNode.offsetHeight - self.offsetBottom){
                    clipCase.style.top = clipCase.parentNode.offsetHeight - self.offsetBottom + 'px'
                }

                if(parseFloat(clipCase.style.left) <= 0){
                    clipCase.style.left = 0
                    clipCase.style.width = clipCase.parentNode.offsetWidth - self.offsetRight + 'px'
                }else if(parseFloat(clipCase.style.left) >= clipCase.parentNode.offsetWidth - self.offsetRight){
                    clipCase.style.left = clipCase.parentNode.offsetWidth - self.offsetRight + 'px'
                }
            }
            window.onmouseup = function(){
                self.top = clipCase.style.top
                self.left = clipCase.style.left
                self.width = clipCase.style.width
                self.height = clipCase.style.height
                self.getCenter()
                this.onmousemove = null;
                this.onmouseup = null;
            }
            event.stopPropagation(); 
        },

        // 单击旋转
        moveRotateActive(){
            
        },

        // 获得中心点坐标
        getCenter(){
            // 中心点
            this.center.x = parseFloat(this.width) / 2 +  parseFloat(this.left)
            this.center.y = parseFloat(this.height) / 2 +  parseFloat(this.top)

            // console.log(this.center)
        },
        // 

        saveData(){
            let obj = {}
            obj.id = this.coverageId;
            obj.width = this.width;
            obj.height = this.height;
            obj.top = this.top;
            obj.left = this.left;
            obj.show = this.show;
            obj.img = this.path

            this.$emit('getData', obj)
            // let arr = localStorage.getItem('coverageData');
            // let coverageData = JSON.parse(arr)
            // if(arr){
            //     let obj = {}
            //     obj.width = this.width;
            //     obj.height = this.height;
            //     obj.top = this.top;
            //     obj.left = this.left;
            //     obj.show = this.show;
            //     obj.img = this.path
            //     coverageData[this.coverageIndex] = obj
            //     let _arr = JSON.stringify(coverageData)
            //     localStorage.setItem('coverageData', _arr)
            // }else{
            //     return
            // }
        },

        initData(){
            let clipCase = this.$refs.clipCase
            // 获得初始的offsetBottom,offsetRight,width，height
            this.offsetBottom = clipCase.parentNode.offsetHeight - clipCase.offsetHeight
            this.offsetRight = clipCase.parentNode.offsetWidth - clipCase.offsetWidth
            this.width = clipCase.style.width
            this.height = clipCase.style.height
        },
    },
    

    mounted(){
        this.initData()
    },

    created(){
        // console.log(this.item)
    },

    watch: {
        '_width': function(val){
            this.width = val
            // this.saveData()
        },
        '_height': function(val){
            this.height = val
            // this.saveData()
        },
        '_top': function(val){
            this.top = val
            // this.saveData()
        },
        '_left': function(val){
            this.left = val
            // this.saveData()
        },
        '_path': function(val){
            this.path = val
            // this.saveData()
        },
        '_isShow': function(val){
            this.isShow = val
        }
    }
}
</script>

<style lang="scss" scoped>
.clipCase{
    position: absolute;
    top: 0;
    left: 0;
    z-index: 10;
    -moz-user-select: none;
    -khtml-user-select: none; 
    user-select: none;
    // transform: rotate(45deg);
    cursor: move;
    .content{
        width: 100%;
        height: 100%;
        img{
            width: 100%;
            height: 100%;
        }
    }
    .minBox{
        position: absolute;
        width: 8px;
        height: 8px;
        border-radius: 50%;
        background-color: #fff;
        border:1px solid blue;
    }
    .left-up{
        top: -4px;
        left: -4px;
        cursor: nw-resize;
    }
    .up{
        left: 50%;
        margin-left: -4px;
        top: -4px;
        cursor: n-resize;
    }
    .right-up{
        right: -4px;
        top: -4px;
        cursor: ne-resize;
    }
    .left {
        top: 50%;
        margin-top: -4px;
        left: -4px;
        cursor: w-resize;
    }
    .right {
        top: 50%;
        margin-top: -4px;
        right: -4px;
        cursor: w-resize;
    }
    .left-down {
        bottom: -4px;
        left: -4px;
        cursor: sw-resize;
    }
    .down {
        bottom: -4px;
        left: 50%;
        margin-left: -4px;
        cursor: s-resize;
    }
    .right-down {
        bottom: -4px;
        right: -4px;
        cursor: se-resize;
    }
    .rotate{
        position: absolute;
        width: 10px;
        height: 10px;
        border-radius: 50%;
        border: 1px solid blue;
        background-color: #fff;
        top: -25px;
        left: 50%;
        margin-left: -5px;
        // cursor: url("../../assets/xuanzhuan.jpg");
        cursor: pointer;
    }
}
.border{
    border: 1px solid blue;
}
</style>

