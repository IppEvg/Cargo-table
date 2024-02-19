<template>
<div class="newRow">
          <button class="adderRow" @click="$emit('addNewRow')"><span class="plus">+</span> Добавить строку</button>
        </div>
</template>
<script>
export default {
    name:'NewRowAdder',
    emits:['addNewRow'],
}
</script>