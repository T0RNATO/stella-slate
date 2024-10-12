<script setup lang="ts">
import {Ref, ref} from "vue";

const selected = ref(false);
const el: Ref<HTMLDivElement> = ref(null);

const pointLevel = ref(0);

function click() {
    if (!props.scoring) {
        if (props.numSelected >= 10 && !selected.value) return;
        el.value.animate([
            { backgroundSize: '0 0', transform: 'scale(1)' },
            { transform: 'scale(1.05)', offset: 0.1 },
            { transform: 'scale(1)', offset: 0.2 },
            { backgroundSize: '2000% 2000%' }
        ], {
            duration: 2000,
            easing: 'linear'
        });
        selected.value = !selected.value;
        emit("select", selected.value);
    } else if (selected.value) {
        switch (pointLevel.value) {
            case 0:
                pointLevel.value = 2;
                emit("score", 2);
                break;
            case 2:
                pointLevel.value = 3;
                emit("score", 1);
                break;
            case 3:
                pointLevel.value = 0;
                emit("score", -3);
                break;
        }
        el.value.animate([
            { transform: 'scale(1)' },
            { transform: 'scale(1.05)', offset: 0.5 },
            { transform: 'scale(1)'},
        ], {
            duration: 400,
            easing: 'linear'
        });
    }
}

const props = defineProps<{scoring: boolean, numSelected: number}>();
const emit = defineEmits(["score", "select"]);

defineExpose({
    reset() {
        selected.value = false;
        pointLevel.value = 0;
    }
})
</script>

<template>
<div class="cell" ref="el" @click="click"
     :class="{pts2: pointLevel == 2, pts3: pointLevel == 3}">
    {{selected ? "✘" : "‎"}}
    <div v-if="pointLevel !== 0" class="absolute bottom-0 right-0 p-1 text-lg">{{pointLevel}}</div>
</div>
</template>

<style scoped>
.cell {
    @apply w-full h-full bg-slate-100 rounded-lg flex items-center justify-center text-4xl relative transition-colors;
    background-image: radial-gradient(transparent 0%, theme(colors.purple.200) 40%, theme(colors.purple.200) 60%, transparent 100%);
    background-repeat: no-repeat;
    background-position: center;
    background-size: 0 0;
}
.pts2 {
    @apply bg-green-200;
}
.pts3 {
    @apply bg-green-400;
}
</style>