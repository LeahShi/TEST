---
title: d3.js学习笔记（四） — 布局
date: 2017-03-17 11:05:19
tags: [D3,JS,数据可视化]
categories: work
---

接上篇d3布局。上次提到了比较常用的饼状图和力导向图。
今天得空记录下集群图、树状图、（两者类似）；打包图、地图的制作...

<!-- more -->

## 集群图d3.layout.cluster()
集群图主要用来表示种用于表示包含与被包含关系的图表；类似于列举大纲。
上篇布局曾说过所有d3布局都可以有三步走，数据以及d3特殊的数据转化 → 图形生成器生成图形 → 向图形界面添加图形元素。
今天介绍的几种图形绘制也不例外。
![](/images/d3_cluster.jpg)
### 数据格式
```
{
"name":"第一级",
"children":
[
	{
	  "name":"第二级" ,
  	  "children":
  	  [
	  	  	{"name":"第三级" },
	  	  	{"name":"第三级" },
	  	  	{"name":"第三级" },
	  	  	{"name":"第三级" }
  	  ]
  	},

	{
		"name":"第二级" ,
		"children":
		[
			{"name":"第三级" },
            {"name":"第三级" },
            {"name":"第三级" },
            {"name":"第三级" }
		]
	}
]
}

```
### demo及分析
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>集群图</title>
    <style>

        .node circle {
          fill: #fff;
          stroke: steelblue;
          stroke-width: 1.5px;
        }

        .node {
          font: 12px sans-serif;
        }

        .link {
          fill: none;
          stroke: #ccc;
          stroke-width: 1.5px;
        }

    </style>
</head>
<body>
<script src="http://d3js.org/d3.v3.min.js"></script>
<script>
    //1------定义画布
    var width = 500,height = 500;
    var svg = d3.select("body").append('svg').attr({"width":width,"height":height})
                //若第一级节点没有超出画布外，则没有必要；
                .append("g").attr("transform", "translate(40,0)");

    //3-----定义图形生成器
    //d3.svg.diagonal()是一个对角线生成器，只需要输入两个顶点坐标，即可生成一条贝塞尔曲线。
    //projection() 是一个点变换器
    var diagonal = d3.svg.diagonal().projection(function(d) { return [d.y, d.x]; });

    //2-----数据及数据转换
    //布局保存在变量 cluster 中，变量 cluster 可用于转换数据
    //size() 设定范围
    var cluster = d3.layout.cluster().size([width-100,height - 100]);
    //转换数据
    d3.json("city.json",function(error,data){
        var nodes = cluster.nodes(data);
        var links = cluster.links(nodes);
        //本地文件只有Firefox能读取，用其打开才有效果
        //与其他图形相似，集群图有节点以及连线；连线又分为起点，连接点
       console.log(nodes);    //顶点（节点）
       console.log(links);    //连线  = source + target

       //3-----生成图形
       //连线
        var link = svg.selectAll(".link")
                      .data(links)
                      .enter()
                      .append("path")
                      .attr("class", "link")
                      .attr("d", diagonal);

        //节点画圆
        var node = svg.selectAll(".node")
                      .data(nodes)
                      .enter()
                      .append("g")
                      .attr("class", "node")
                      .attr("transform", function(d) { return "translate(" + d.y + "," + d.x + ")"; })

        node.append("circle").attr("r", 4.5);

        //填文字
        node.append("text")
            .attr("dx", function(d) { return d.children ? -8 : 8; })
            .attr("dy", 3)
            .style("text-anchor", function(d) { return d.children ? "end" : "start"; })
            .text(function(d) { return d.name; });

    })




</script>
</body>
</html>
```

### 总结
- d3布局都可以有三步走，数据以及d3特殊的数据转化 → 图形生成器生成图形 → 向图形界面添加图形元素
- d3.svg.diagonal()是一个对角线生成器，只需要输入两个顶点坐标，即可生成一条贝塞尔曲线。
- d3.layout.cluster()集群图生成器、用于数据转换绘制图形等。
- 所有用于表示两者数据关联【包含、联系等】的图表都会有节点nodes、连线links；连线有理所当然的包含起点source、被连接点target.

## 树状图d3.layout.tree()
所有方法和集群图都一致。