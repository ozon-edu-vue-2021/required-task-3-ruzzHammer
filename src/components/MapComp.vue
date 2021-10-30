<template>
    <div class="map">
        <h3>Карта офиса</h3>

        <div v-if="!isLoading" class="map-root">
            <MapSVG ref="svg" />

            <TableSVG v-show="false" ref="table" />
        </div>
        <div v-else>Loading...</div>
    </div>
</template>

<script>
import MapSVG from '@/assets/images/map.svg';
import TableSVG from '@/assets/images/workPlace.svg';
import * as d3 from 'd3';
import tables from '@/assets/data/tables.json';
import legend from '@/assets/data/legend.json';

export default {
    components: {
        MapSVG,
        TableSVG,
    },
    data() {
        return {
            isLoading: false,
            svg: null,
            g: null,
            tables: [],
            tableSVG: null,
        };
    },
    methods: {
        drawTables() {
            const svgTablesGroup = this.g
                .append('g')
                .classed('groupPlaces', true);

            this.tables.map((table) => {
                const svgTable = svgTablesGroup
                    .append('g')
                    .attr(
                        'transform',
                        `translate(${table.x}, ${table.y}) scale(.45)`
                    )
                    .attr('id', table._id)
                    .classed('employer-place', true);
                svgTable
                    .append('g')
                    .attr('transform', `rotate(${table.rotate || 0})`)
                    .attr('group_id', table.group_id)
                    .html(this.tableSVG.html())
                    .attr(
                        'fill',
                        legend.find((item) => item.group_id === table.group_id)
                            ?.color ?? 'transparent'
                    );
            });
        },
    },
    mounted() {
        this.svg = d3.select(this.$refs.svg);
        this.g = this.svg.select('g');
        this.tableSVG = d3.select(this.$refs.table);
        this.tables = tables;

        if (this.g) {
            this.drawTables();
        } else console.log('ERROR');
    },
};
</script>

<style scoped>
.map {
    height: 100%;
    width: 100%;
    padding: 24px;
    overflow: hidden;
    box-sizing: border-box;
    display: flex;
    flex-direction: column;
}

.map-root {
    height: 100%;
    width: 100%;
    overflow: hidden;
    box-sizing: border-box;
}

h3 {
    margin-top: 0px;
}

::v-deep svg {
    height: 100%;
    width: 100%;
}

::v-deep .table {
    cursor: pointer;
}
</style>
