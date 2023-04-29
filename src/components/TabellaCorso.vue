<script setup lang="ts">
import { PropType } from "vue";
import { Persona } from "../types";

defineProps({
    persone: {
        type: Array as PropType<Persona[]>,
        required: true,
    },
});
</script>
<template>
    <table>
        <thead>
            <tr>
                <th>Iscritto</th>
                <th>Nome</th>
                <th>Cognome</th>
                <th>Età</th>
                <th>Sesso</th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="p in persone">
                <td>{{ p["Submitted On"] }}</td>
                <td>{{ p["nome dellallievo"] }}</td>
                <td>{{ p["cognome dellallievo"] }}</td>
                <td>{{ p["ETÀ"] }}</td>
                <td
                    :style="
                        p['sesso'] == 'Maschio' ? 'color: #5af' : 'color: #fba'
                    "
                >
                    {{ p["sesso"].substring(0, 1) }}
                </td>
            </tr>
        </tbody>
    </table>
    <!-- {{ persone }} -->
</template>
