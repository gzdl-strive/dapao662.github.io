---
title: 图结构的封装
date: 2021-03-09 09:54:02
tag: algorigthm
---

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>图结构</title>
</head>
<body>
<!-- 引入字典类(封装边)和队列类(广度优先使用队列存储) -->
<script src="./dict.js"></script>
<script src="./queue.js"></script>
<script>
    /*
    使用邻接表封装图结构
    */
    // 封装图结构类
    function Graph() {
        // 属性：顶点(数组)、边(字典)->引入字典类
        this.vertexes = [];//顶点
        this.edges = new Dictionary();//边

        // 方法
        // 添加方法
        // 1、添加顶点的方法
        Graph.prototype.addVertex = function (v) {
            this.vertexes.push(v);
            this.edges.set(v, []);
        }
        // 2、添加边的方法
        Graph.prototype.addEdge = function (v1, v2) {
            this.edges.get(v1).push(v2);
            this.edges.get(v2).push(v1);
        }
        // 3、toString方法
        Graph.prototype.toString = function () {
            // 1、定义字符串，保存最终结果
            let resultString = '';
            // 2、遍历所有顶点，以及顶点对应的边
            for (let i = 0; i < this.vertexes.length; i++) {
                resultString += this.vertexes[i] + "->";
                let vEdges = this.edges.get(this.vertexes[i]);
                for (let j = 0; j < vEdges.length; j++) {
                    resultString += vEdges[j] + ' ';
                }
                resultString += '\n';
            }
            return resultString;
        }
        // 4、初始化状态颜色
        Graph.prototype.initializeColor = function () {
            let colors = [];
            for (let i = 0; i < this.vertexes.length; i++) {
                colors[this.vertexes[i]] = 'white';
            }
            return colors;
        }
        // 5、广度优先搜索(BFS)
        Graph.prototype.bfs = function (initV, handler) {
            //1、初始化颜色
            let colors = this.initializeColor();
            // 2、创建一个队列
            const queue = new Queue();
            // 3、将顶点加入到队列中
            queue.enqueue(initV);
            // 4、循环从队列中取出元素
            while (!queue.isEmpty()) {
                // 4.1、从队列取出一个顶点
                let v = queue.dequeue();

                // 4.2、获取和顶点相连的另外节点
                let vList = this.edges.get(v);

                // 4.3、将v的颜色设置成灰色
                colors[v] = 'gray';

                // 4.4、遍历所有的顶点，并且加入到队列中
                for (let i = 0; i < vList.length; i++) {
                    let e = vList[i];
                    if (colors[e] === 'white') {
                        colors[e] = 'gray';
                        queue.enqueue(e);
                    }
                }

                // 4.5、访问顶点
                handler(v);

                // 4.6、将顶点设置位黑色
                colors[v] = 'black';
            }
        }
        // 6、深度优先搜索(DFS)
        Graph.prototype.dfs = function (initV, handler) {
            // 1、初始化颜色
            let colors = this.initializeColor();
            // 2、从某个顶点开始一次递归访问
            this.dfsVisit(initV, colors, handler);
        }
        Graph.prototype.dfsVisit = function (v, colors, handler) {
            // 1、先将颜色设置位灰色
            colors[v] = 'gray';

            // 2、处理v顶点
            handler(v);

            // 3、访问v相连的顶点
            let vList = this.edges.get(v);
            for (let i = 0; i < vList.length; i++) {
                let e = vList[i];
                if (colors[e] === 'white') {
                    this.dfsVisit(e, colors, handler);
                }
            }

            // 4、将v设置位灰色
            colors[v] = 'black';
        }
    }

    //测试
    const graph = new Graph();
    // 1、添加顶点
    const myVertex = ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I'];
    myVertex.map(item => {
        graph.addVertex(item);
    });

    // 2、添加边
    graph.addEdge('A', 'B');
    graph.addEdge('A', 'C');
    graph.addEdge('A', 'D');
    graph.addEdge('C', 'D');
    graph.addEdge('C', 'G');
    graph.addEdge('D', 'G');
    graph.addEdge('D', 'H');
    graph.addEdge('B', 'E');
    graph.addEdge('B', 'F');
    graph.addEdge('E', 'I');

    // 3、测试toString方法
    console.log(graph.toString());
    /*
    A->B C D 
    B->A E F 
    C->A D G 
    D->A C G H 
    E->B I 
    F->B 
    G->C D 
    H->D 
    I->E 
    */

    // 4、测试BFS
    let result = '';
    graph.bfs(graph.vertexes[0], function (v) {
        result += v + ' ';
    })
    console.log('广度优先：',result);//广度优先： A B C D E F G H I

    // 5、测试DFS
    let result2 = '';
    graph.dfs(graph.vertexes[0], function (v) {
        result2 += v + ' ';
    })
    console.log('深度优先：',result2);//深度优先： A B E I F C D G H 
</script>
</body>
</html>
```