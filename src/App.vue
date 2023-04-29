<script setup lang="ts">
import { Persona } from "./types";

// import data from "./assets/data.json";

import { computed, ref } from "vue";
import { each, sortBy, uniq } from "lodash";
import GraficoDelSesso from "./components/GraficoDelSesso.vue";
import TabellaCorso from "./components/TabellaCorso.vue";
const tutti = ref([] as Persona[]);
const settimane = computed(() => uniq(tutti.value.map((x) => x.settimana)));
const corsi = computed(() =>
    uniq(tutti.value.map((x) => x["periodo del corso"]))
);
const filtro_settimana = ref(undefined as undefined | any);
const filtro_corso = ref(undefined as undefined | any);
const persone = computed(() => {
    let ret = tutti.value;
    if (filtro_settimana.value) {
        ret = ret.filter((c) => c.settimana == filtro_settimana.value);
    }
    if (filtro_corso.value) {
        ret = ret.filter((c) => c["periodo del corso"] == filtro_corso.value);
    }
    return ret;
});
void refresh();
async function refresh() {
    let res;
    while (true) {
        let pass = localStorage.getItem("link") || prompt("Password");
        if (!pass?.startsWith("https://")) {
            localStorage.removeItem("link");
            alert("Link sbagliato");
            continue;
        }
        try {
            res = await fetch(pass, {
                redirect: "follow",
            });
        } catch (e) {
            localStorage.removeItem("link");
            alert("Link sbagliato");
            continue
        }
        console.log(res.status);
        if (res.status == 200) {
            localStorage.setItem("link", pass);
            break;
        }
        if (res.status == 404) {
            localStorage.removeItem("link");
            alert("Link sbagliato");
        } else {
            alert("Errore di connessione");
        }
    }
    const text_data = (await res.text()).split("\n");
    const data: Persona[] = [];
    const header = text_data[0]?.split("\t");
    if (header.length < 10) {
        localStorage.removeItem("link");
        alert("Link sbagliato");
    }
    for (const line_str of text_data.slice(1)) {
        const line = line_str.split("\t");
        const row: Persona = {};
        each(header, (name, index) => {
            row[name] = line[index] ?? "";
        });
        data.push(row);
    }
    tutti.value = sortBy(data, (x) => x.settimana);
}
</script>
<template>
    <div v-if="!persone.length">Caricamento...</div>
    <div v-else>
        <div style="height: 400px">
            <GraficoDelSesso :persone="tutti" />
        </div>
        <div>
            Settimana:
            <select v-model="filtro_settimana">
                <option :value="undefined">Tutto</option>
                <option v-for="c in settimane">{{ c }}</option>
            </select>
            Corso:
            <select v-model="filtro_corso">
                <option :value="undefined">Tutto</option>
                <option v-for="c in corsi">{{ c }}</option>
            </select>

            Totale: {{ persone.length }}
        </div>
        <TabellaCorso :persone="sortBy(persone, (p) => p['Submitted On'])" />
    </div>
</template>
