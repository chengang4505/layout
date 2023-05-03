<template>
    <div class="test" ref="dom">
    </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import * as d3 from 'd3'
import * as d3Sankey from './lib/sankey'
import G6 from '@antv/g6'

let dom = ref(null)
onMounted(() => {
    main();
})


function main() {

    d3.json('./data/test.json').then((data) => {

        console.log(data)
        let { nodes, edges } = data as any;

        edges.forEach(e => {
            e.value = 1;
        })
        nodes.forEach(e => {
            e.value = 1;
            e.fixedValue = 1;
        })

        console.log(d3Sankey)

        d3Sankey.sankey()
            .nodes(nodes)
            .links(edges)
            .nodeId(e => e.id)
            .nodeAlign(d3Sankey.sankeyJustify)
            .nodeWidth(50)
            .nodePadding(50)
            .extent([[-500, -500], [500, 500]])();


        // console.log(nodes)

        initGraph({
            nodes: nodes.map(e => {
                return { id: e.id, x: e.x0, y: e.y0,label: e.id.slice(0,5) }
            }),
            edges: edges.map(e => {
                return { source: e.source.id, target: e.target.id }
            })
        });


    })

}

function initGraph(data: any) {

    console.log(data)

    let container = dom.value as any as HTMLDivElement;
    // 创建 G6 图实例
    const graph = new G6.Graph({
        container: container,
        // 画布宽高
        width: container.clientWidth,
        height: container.clientHeight,
        fitView: true,
        modes: {
            default: ['drag-node', 'zoom-canvas', 'drag-canvas'],
        },
        defaultEdge: {
            // ... 其他属性
            style: {
                endArrow: true,
            },
        },
    });
    // 读取数据
    graph.data(data);
    // 渲染图
    graph.render();

}

</script>

<style>
.test {
    width: 100%;
    height: 100%;
    overflow: hidden;
}
</style>
