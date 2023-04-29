<script setup lang="ts">
import "echarts";
import VChart from "vue-echarts";
import { uniq } from "lodash";
import { PropType, computed } from "vue";
import { Persona } from "../types";

const props = defineProps({
    persone: {
        type: Array as PropType<Persona[]>,
        required: true,
    },
});
const options = computed(() => {
    const settimane = uniq(props.persone.map((p) => p.settimana));
    const maschi = [];
    const femmine = [];
    for (const s of settimane) {
        const pset = props.persone.filter((x) => x.settimana == s);
        maschi.push(pset.filter((p) => p.sesso == "Maschio").length);
        femmine.push(pset.filter((p) => p.sesso == "Femmina").length);
    }

    return {
        dark: true,
        legend: {
            data: ["bar", "bar2", "bar3", "bar4"],
            left: "10%",
        },
        brush: {
            toolbox: ["rect", "polygon", "lineX", "lineY", "keep", "clear"],
            xAxisIndex: 0,
        },
        toolbox: {
            feature: {
                magicType: {
                    type: ["stack"],
                },
                dataView: {},
            },
        },
        tooltip: {},
        xAxis: {
            data: settimane,
            name: "X Axis",
            axisLine: { onZero: true },
            splitLine: { show: false },
            splitArea: { show: false },
        },
        yAxis: {},
        grid: {
            bottom: 100,
        },
        series: [
            {
                name: "M",
                type: "bar",
                stack: "one",
                data: maschi,
                itemStyle: {
                    color: "#5af",
                },
            },
            {
                name: "F",
                type: "bar",
                stack: "one",
                data: femmine,
                itemStyle: {
                    color: "#fba",
                },
            },
        ],
    };
});
</script>
<template>
    <VChart :option="options" />
</template>
