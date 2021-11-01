<template>
    <div :class="[{ 'menu--ext': isUserOpened }, 'menu']">
        <div class="toolbar">
            <div class="toolbar__header">
                <template v-if="!isUserOpened">
                    <h3>Информация</h3>
                </template>
                <template v-else>
                    <div class="action">
                        <div class="arrow" @click="closeProfile"></div>
                    </div>
                    <h3>Профиль</h3>
                </template>
            </div>
            <div class="toolbar__actions"></div>
        </div>
        <div class="content">
            <div v-if="!isUserOpened" class="legend">
                <div class="legend__data">
                    <div v-if="legend.length > 0" class="legend__items">
                        <Draggable v-model="legend">
                            <LegendItem
                                v-for="(item, index) in legend"
                                :key="index"
                                :color="item.color"
                                :text="item.text"
                                :counter="counter[index]"
                                class="legend__item"
                            />
                        </Draggable>
                    </div>
                    <span v-else class="legend--empty"> Список пуст </span>
                </div>
                <div class="legend__chart">
                    <Pie ref="chart" />
                </div>
            </div>
            <div v-else class="profile">
                <div v-if="!person" class="profile__empty">Место пустое</div>

                <PersonCard :person="person" />
            </div>
        </div>
    </div>
</template>

<script>
import LegendItem from './SideMenu/LegendItem.vue';
import PersonCard from './SideMenu/PersonCard.vue';

import legend from '@/assets/data/legend.json';
import tables from '@/assets/data/tables.json';
import Draggable from 'vuedraggable';
import { Pie } from 'vue-chartjs';

export default {
    props: {
        isUserOpened: {
            type: Boolean,
            default: false,
        },
        person: {
            type: Object,
            default: null,
        },
    },
    components: {
        LegendItem,
        PersonCard,
        Draggable,
        Pie,
    },
    data() {
        return {
            legend: [],
            counter: {},
        };
    },
    created() {
        this.loadLegend();
        this.countTeamMembers();
    },
    mounted() {
        this.makeChart();
    },
    updated() {
        if (this.isUserOpened === false) {
            this.makeChart();
        }
    },
    methods: {
        loadLegend() {
            this.legend = legend;
        },
        countTeamMembers() {
            tables.forEach((table) => {
                if (this.counter[table.group_id]) {
                    this.counter[table.group_id]++;
                } else {
                    this.counter[table.group_id] = 1;
                }
            });
        },
        closeProfile() {
            this.$emit('update:isUserOpened', false);
        },
        makeChart() {
            const chartData = {
                labels: this.legend.map((legendItem) => legendItem.text),
                datasets: [
                    {
                        label: 'Легенда',
                        backgroundColor: this.legend.map(
                            (legendItem) => legendItem.color
                        ),
                        data: Object.values(this.counter).map((count) => count),
                    },
                ],
            };
            const options = {
                display: false,
            };
            this.$refs.chart.renderChart(chartData, options);
        },
    },
};
</script>

<style scoped>
.menu {
    border-left: 1px solid #ccd8e4;
    padding: 24px;
    display: flex;
    flex-direction: column;
    overflow: hidden;
    width: 320px;
    transition: 0.2s ease;
}

.menu--ext {
    width: 400px;
}

.toolbar {
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.toolbar .toolbar__actions button {
    font-size: 0.76rem;
    text-transform: uppercase;
    letter-spacing: 0.08rem;
    padding: 2px 6px;
}

.toolbar__header {
    display: flex;
    align-items: center;
    margin-bottom: 12px;
}

.toolbar__header .action {
    cursor: pointer;
    margin-right: 14px;
    width: 20px;
    height: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
}

.toolbar__header .action .arrow {
    width: 10px;
    height: 10px;
    border-top: 2px solid blue;
    border-right: 2px solid blue;
    transform: rotate(-135deg);
}

h3 {
    margin: 0;
}

.content {
    flex: 1;
}

.content .legend {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    height: 100%;
}

.content .legend .legend__data {
    display: flex;
    height: 100%;
}

.content .legend .legend__items {
    flex: 1;
    width: 100%;
}

.content .legend .legend__items .legend__item:not(:first-child) {
    margin-top: 16px;
}

.content .legend .legend__items .legend__item {
    cursor: pointer;
}

.content .legend .legend__items .legend__item.sortable-chosen {
    opacity: 25%;
}

.content .legend .legend__chart {
    max-width: 270px;
    margin: 0 auto;
}

.content .legend .legend--empty {
    align-self: center;
    width: 100%;
    text-align: center;
}

.profile {
    padding-top: 20px;
}
</style>
