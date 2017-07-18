<!DOCTYPE html>
<html>

<head>
    <meta charset="UTF-8">
    <title>轮播图</title>
    <style>
        * {
            margin: 0;
            padding: 0;
        }
        
        li {
            list-style: none;
        }
        
        #tab {
            width: 670px;
            height: 240px;
            border: 1px solid #ccc;
            margin: 50px auto;
            position: relative;
            overflow: hidden;
        }
        
        #tab ul {
            width: 2680px;
            height: 240px;
            position: absolute;
            left: 0;
            top: 0;
            overflow: hidden;
        }
        
        #tab ul li {
            float: left;
            width: 670px;
        }
        
        #tab ul li img {
            width: 670px;
        }
        
        #tab ol {
            width: 80px;
            position: absolute;
            bottom: 10px;
            left: 50%;
            margin-left: -40px;
            overflow: hidden;
        }
        
        #tab ol li {
            float: left;
            width: 10px;
            height: 10px;
            background: #ccc;
            border-radius: 50%;
            margin: 5px;
            cursor: pointer;
        }
        
        #tab ol .on {
            background: #f00;
        }
        
        #tab .prev,
        #tab .next {
            display: none;
            width: 40px;
            height: 60px;
            background: rgba(0, 0, 0, .3);
            /*filter: alpha(opacity: 30);*/
            text-decoration: none;
            text-align: center;
            line-height: 60px;
            font-size: 30px;
            color: #fff;
            position: absolute;
            top: 50%;
            margin-top: -30px;
        }
        
        #tab .prev {
            left: 0;
        }
        
        #tab .next {
            right: 0;
        }
    </style>
</head>

<body>
    <div id="tab">
        <ul>
            <li><img src="https://www.julend.com/images?uuid=c74b05ce-14ab-46fc-b126-19bcd94ca092" alt="" /></li>
            <li><img src="https://www.julend.com/jdk/images/bg.jpg" alt="" /></li>
            <li><img src="https://www.julend.com/images?uuid=a9d09a9e-dd1b-4b97-b9ff-d6e9d0b56d23" alt="" /></li>
        </ul>
        <ol>
            <li class="on"></li>
            <li></li>
            <li></li>
        </ol>
        <a href="javascript:;" class="prev" id="prev">-</a>
        <a href="javascript:;" class="next" id="next">-</a>
    </div>


</body>
<script>
    window.onload = function() {
        var oTab = document.getElementById('tab');
        var oUl = oTab.getElementsByTagName('ul')[0];
        var aLi1 = oUl.children;
        var oOl = oTab.getElementsByTagName('ol')[0];
        var aLi2 = oOl.children;
        var prev = document.getElementById('prev');
        var next = document.getElementById('next');
        //设置ul的初始位置
        var iNow = 1;
        oUl.style.left = -aLi1[0].offsetWidth * iNow + 'px';
        //定时器
        var timer = null;
        //克隆第一张图片 添加在ul的最后面
        var oLi1 = aLi1[0].cloneNode(true);
        //克隆最后一张图片 添加在ul的最前面
        var oLi2 = aLi1[aLi1.length - 1].cloneNode(true);
        oUl.appendChild(oLi1);
        oUl.insertBefore(oLi2, aLi1[0]);
        oUl.style.width = aLi1[0].offsetWidth * aLi1.length + "px";
        //鼠标移入tab: 关闭定时器，左右按钮显示
        oTab.onmouseover = function() {
            clearInterval(timer);
            prev.style.display = 'block';
            next.style.display = 'block';
        };
        //鼠标移出tab: 开启定时器，左右按钮隐藏
        oTab.onmouseout = function() {
            timer = setInterval(function() {
                toNext();
            }, 2000);
            prev.style.display = 'none';
            next.style.display = 'none';
        };
        //点击小圆点
        for (var i = 0; i < aLi2.length; i++) {
            (function(index) {
                aLi2[index].onclick = function() {
                    iNow = index + 1;
                    for (var i = 0; i < aLi2.length; i++) {
                        aLi2[i].className = '';
                    }
                    aLi2[index].className = 'on';
                    animate(oUl, {
                        left: -iNow * aLi1[0].offsetWidth
                    });
                }
            })(i);
        }
        //上一个
        prev.onclick = function() {
                iNow--;
                animate(oUl, {
                    left: -iNow * aLi1[0].offsetWidth
                }, {
                    complete: function() {
                        if (iNow == 0) {
                            iNow = aLi1.length - 2;
                            oUl.style.left = -aLi1[0].offsetWidth * iNow + 'px';
                        }
                        for (var i = 0; i < aLi2.length; i++) {
                            aLi2[i].className = '';
                        }
                        aLi2[iNow - 1].className = 'on';
                    }
                });
            }
            //下一个
        next.onclick = function() {
            toNext();
        }

        function toNext() {
            iNow++;
            animate(oUl, {
                left: -iNow * aLi1[0].offsetWidth
            }, {
                complete: function() {
                    if (iNow == aLi1.length - 1) {
                        iNow = 1;
                        oUl.style.left = -aLi1[0].offsetWidth * iNow + 'px';
                    }
                    for (var i = 0; i < aLi2.length; i++) {
                        aLi2[i].className = '';
                    }
                    if (iNow > 0) {
                        aLi2[iNow - 1].className = 'on';
                    }

                }
            });
        }
        //设置定时器
        timer = setInterval(function() {
            toNext();
        }, 3000);
    }

    /*
     * 参数说明:
     * obj: 运动对象
     * json(json形式): 需要修改的属性
     * options(json形式): 
     * duration: 运动时间
     * easing: 运动方式（匀速、加速、减速）
     * complete: 运动完成后执行的函数
     */
    function animate(obj, json, options) {
        var options = options || {};
        var duration = options.duration || 500; //运动时间,默认值为500ms;
        var easing = options.easing || 'linear'; //运动方式,默认为linear匀速
        var start = {};
        var dis = {};

        for (var name in json) {
            start[name] = parseFloat(getStyle(obj, name)); //起始位置
            dis[name] = json[name] - start[name]; //总距离
        }

        var count = Math.floor(duration / 30); //总次数
        var n = 0; //次数

        clearInterval(obj.timer);
        obj.timer = setInterval(function() {
            if (n > count) {
                clearInterval(obj.timer);
                options.complete && options.complete();
            } else {
                for (var name in json) {
                    switch (easing) {
                        //匀速
                        case 'linear':
                            var a = n / count;
                            var cur = start[name] + dis[name] * a; //当前位置
                            break;
                            //加速
                        case 'ease-in':
                            var a = n / count;
                            var cur = start[name] + dis[name] * a * a * a;
                            break;
                            //减速
                        case 'ease-out':
                            var a = 1 - n / count;
                            var cur = start[name] + dis[name] * (1 - a * a * a);
                            break;
                    }
                    if (name == 'opacity') {
                        obj.style.opacity = cur;
                        obj.style.filter = 'alpha(opacity=' + cur * 100 + ')'; //兼容IE8及以下
                    } else {
                        obj.style[name] = cur + 'px';
                    }
                }
            }
            n++;
        }, 30);
    }
    //获取非行间样式
    function getStyle(obj, sName) {
        return (obj.currentStyle || getComputedStyle(obj, false))[sName];
    }
</script>

</html>
