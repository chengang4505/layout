<template>
    <div class="test" ref="dom">
    </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import * as d3 from 'd3'
import * as d3Sankey from  './lib/sankey'

let dom = ref(null)
onMounted(() => {
    main();
})


function main() {
    let container = dom.value as any as HTMLDivElement;

    const svg = d3.create("svg")
        .attr('width', 800)
        .attr('height', 600)

    container.append(svg.node());

    let group = svg.append('g');
    group.style('transform', 'translate(400px, 300px) scale(0.2)')

    d3.json('./data/test.json').then((data) => {

        console.log(data)
        let { nodes, edges } = data as any;

        d3Sankey.sankey()
            .nodes(nodes)
            .links(edges)
            .nodeId(e => e.id)
            .nodeAlign(d3Sankey.sankeyJustify)
            .nodeWidth(15)
            .nodePadding(10)
            .extent([[-500, -500], [1000, 1000]])();



        console.log(nodes)

        const link = group.append("g")
            .attr("stroke", "#999")
            .attr("stroke-opacity", 0.6)
            .selectAll("line")
            .data(edges)
            .join("line");

        const node = group.append("g")
            .attr("fill", "#fff")
            .attr("stroke", "#000")
            .attr("stroke-width", 1.5)
            .selectAll("circle")
            .data(nodes)
            .join("circle")
            .attr("r", 50)

        node
            .attr("cx", d => d.x0)
            .attr("cy", d => d.y0);

        link
            .attr("x1", d => d.source.x0)
            .attr("y1", d => d.source.y0)
            .attr("x2", d => d.target.x0)
            .attr("y2", d => d.target.y0);

    })

}

</script>

<style>
.test {
    width: 100%;
    height: 100%;
}
</style>
