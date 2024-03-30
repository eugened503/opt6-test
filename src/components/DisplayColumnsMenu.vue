<template>
    <div class="menu">
        <label for="id">id
            <input type="checkbox" id="id" value="id" v-model="checkedNames.id">
        </label>

        <label for="john">action
            <input type="checkbox" id="action" value="action" v-model="checkedNames.action">
        </label>

        <label for="nameUnit">nameUnit
            <input type="checkbox" id="nameUnit" value="nameUnit" v-model="checkedNames.nameUnit">
        </label>

        <label for="price">price
            <input type="checkbox" id="price" value="price" v-model="checkedNames.price">
        </label>

        <label for="quantity">quantity
            <input type="checkbox" id="quantity" value="quantity" v-model="checkedNames.quantity">
        </label>

        <label for="productName">productName
            <input type="checkbox" id="productName" value="productName" v-model="checkedNames.productName">
        </label>

        <label for="total">total
            <input type="checkbox" id="total" value="total" v-model="checkedNames.total">
        </label>
        <br>
        <span>Отмеченные имена: {{ checkedNames }}</span>
    </div>
</template>


<script setup>
import { watch, toRef, reactive } from "vue";
const emit = defineEmits(['getCheckedNames'])

const checkedNames = toRef(reactive({
    id: true,
    action: true,
    nameUnit: true,
    price: true,
    quantity: true,
    productName: true,
    total: true,
}));

watch(checkedNames, (newCheckedNames) => {
    if (newCheckedNames) {
        emit('getCheckedNames', checkedNames.value);
    }
}, { deep: true });

</script>


<style scoped lang="scss">
.menu {
    display: flex;
    flex-direction: column;
    gap: 10px;
    margin: 20px;
}
</style>