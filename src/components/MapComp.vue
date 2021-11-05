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
import { select } from 'd3';
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
            svgGroup: null,
            tables: [],
            tableSVG: null,
        };
    },
    methods: {
        drawTables() {
            const svgTablesGroup = this.svgGroup
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
                    )
                    .on('click', () => {
                        this.passTableID(table._id);
                    });
            });
        },
        passTableID(id) {
            this.$emit('table', id);
        },
        hideInfo() {
            this.$emit('update:isUserOpened', false);
        },
    },
    mounted() {
        this.svg = select(this.$refs.svg);
        this.svgGroup = this.svg.select('g');
        this.tableSVG = select(this.$refs.table);
        this.tables = tables;

        if (this.svgGroup) {
            this.drawTables();
        } else console.log('ERROR');

        document.body.addEventListener('click', (e) => {
            if (
                !(
                    e.target.classList.contains('employer-place') ||
                    e.target.closest('.employer-place')
                ) &&
                !(
                    e.target.classList.contains('.person') ||
                    e.target.closest('.person')
                )
            ) {
                this.hideInfo();
            }
        });
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
